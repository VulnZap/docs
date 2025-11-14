# VulnZap API Reference

Base URL: `https://api.vulnzap.com/api/scan`

## Authentication

All endpoints require authentication via API key in the request header:

```
x-api-key: your_api_key_here
```

---

## Endpoints

### 1. Start GitHub Repository Scan

Initiates a security scan for an entire GitHub repository.

**Endpoint:** `POST /github`

**Headers:**
- `x-api-key` (required): Your VulnZap API key
- `Content-Type`: `application/json`

**Request Body:**

```json
{
  "repository": "owner/repo",
  "branch": "main",
  "userIdentifier": "optional-user-id"
}
```

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `repository` | string | Yes | GitHub repository in `owner/repo` format |
| `branch` | string | No | Branch to scan (default: `main`) |
| `userIdentifier` | string | No | Optional identifier for tracking |

**Response:** `200 OK`

```json
{
  "success": true,
  "data": {
    "jobId": "uuid-v4-job-id",
    "projectId": "uuid-v4-project-id",
    "status": "pending",
    "message": "GitHub repository scan started successfully",
    "repository": "owner/repo",
    "branch": "main",
    "remaining": 100
  }
}
```

**Error Responses:**

- `400 Bad Request` - Invalid repository format or missing required fields
- `403 Forbidden` - Subscription limit exceeded
- `500 Internal Server Error` - Failed to start scan

**Example:**

```bash
curl -X POST https://api.vulnzap.com/api/scan/github \
  -H "x-api-key: your_api_key_here" \
  -H "Content-Type: application/json" \
  -d '{
    "repository": "facebook/react",
    "branch": "main"
  }'
```

---

### 2. Start Commit Scan

Scans specific files from a commit for security vulnerabilities.

**Endpoint:** `POST /commit`

**Headers:**
- `x-api-key` (required): Your VulnZap API key
- `Content-Type`: `application/json`

**Request Body:**

```json
{
  "commitHash": "abc123def456",
  "repository": "owner/repo",
  "branch": "main",
  "files": [
    {
      "name": "src/api/auth.ts",
      "content": "// file content here"
    }
  ],
  "userIdentifier": "optional-user-id"
}
```

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `commitHash` | string | Yes | Git commit hash |
| `repository` | string | No | Repository identifier |
| `branch` | string | No | Branch name |
| `files` | array | Yes | Array of file objects with `name` and `content` |
| `userIdentifier` | string | No | Optional identifier for tracking |

**File Object:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Relative file path (e.g., `src/api/auth.ts`) |
| `content` | string | Yes | Complete file content |

**Response:** `200 OK`

```json
{
  "success": true,
  "data": {
    "jobId": "uuid-v4-job-id",
    "projectId": "uuid-v4-project-id",
    "status": "pending",
    "message": "Commit scan started successfully"
  }
}
```

**Error Responses:**

- `400 Bad Request` - Invalid request body or validation error
- `403 Forbidden` - Subscription limit exceeded
- `500 Internal Server Error` - Failed to start scan

**Example:**

```bash
curl -X POST https://api.vulnzap.com/api/scan/commit \
  -H "x-api-key: your_api_key_here" \
  -H "Content-Type: application/json" \
  -d '{
    "commitHash": "abc123",
    "repository": "owner/repo",
    "branch": "main",
    "files": [
      {
        "name": "src/auth.js",
        "content": "const password = \"hardcoded123\";"
      }
    ]
  }'
```

---

### 3. Get Scan Job Status

Retrieves the status and results of a specific scan job.

**Endpoint:** `GET /jobs/:jobId`

**Headers:**
- `x-api-key` (required): Your VulnZap API key

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jobId` | string | Yes | Job ID returned from scan initiation |

**Response (Pending/Running):** `200 OK`

```json
{
  "success": true,
  "data": {
    "jobId": "uuid-v4-job-id",
    "projectId": "uuid-v4-project-id",
    "status": "running",
    "progress": 45,
    "metadata": {
      "repository": "owner/repo",
      "branch": "main",
      "scanType": "full_repository"
    },
    "startedAt": "2025-11-14T10:30:00.000Z",
    "completedAt": null,
    "error": null
  }
}
```

**Response (Completed):** `200 OK`

```json
{
  "success": true,
  "data": {
    "jobId": "uuid-v4-job-id",
    "projectId": "uuid-v4-project-id",
    "status": "completed",
    "progress": 100,
    "metadata": {
      "repository": "owner/repo",
      "branch": "main",
      "scanType": "full_repository"
    },
    "startedAt": "2025-11-14T10:30:00.000Z",
    "completedAt": "2025-11-14T10:35:30.000Z",
    "error": null,
    "results": {
      "scanId": "repositoryscan_timestamp_random",
      "repositoryId": "project-id",
      "timestamp": "2025-11-14T10:35:30.000Z",
      "scanType": "full",
      "totalFiles": 150,
      "linesOfCode": 25000,
      "vulnerabilities": [
        {
          "id": "vuln_1731582930000_abc123",
          "type": "SQL_INJECTION",
          "severity": "critical",
          "confidence": "high",
          "location": {
            "file": "src/api/users.ts",
            "startLine": 45,
            "startColumn": 12,
            "endLine": 45,
            "endColumn": 65,
            "codeSnippet": "const query = `SELECT * FROM users WHERE id = ${userId}`;"
          },
          "description": "SQL injection vulnerability detected in user query",
          "evidence": "Unsanitized user input directly concatenated into SQL query",
          "cwe": "CWE-89",
          "owasp": "A03:2021-Injection",
          "tags": ["sql", "injection", "critical"],
          "falsePositive": false,
          "detectionMethod": "taint",
          "createdAt": "2025-11-14T10:35:25.000Z",
          "source": {
            "file": "src/api/users.ts",
            "startLine": 42,
            "startColumn": 8,
            "endLine": 42,
            "endColumn": 30,
            "codeSnippet": "const userId = req.params.id;"
          },
          "sink": {
            "file": "src/api/users.ts",
            "startLine": 45,
            "startColumn": 12,
            "endLine": 45,
            "endColumn": 65,
            "codeSnippet": "const query = `SELECT * FROM users WHERE id = ${userId}`;"
          },
          "taintFlowPath": [
            {
              "stepNumber": 1,
              "file": "src/api/users.ts",
              "line": 42,
              "column": 8,
              "codeSnippet": "const userId = req.params.id;",
              "description": "User input received from request parameters"
            },
            {
              "stepNumber": 2,
              "file": "src/api/users.ts",
              "line": 45,
              "column": 12,
              "codeSnippet": "const query = `SELECT * FROM users WHERE id = ${userId}`;",
              "description": "Tainted data flows into SQL query without sanitization"
            }
          ]
        }
      ],
      "summary": {
        "critical": 5,
        "high": 12,
        "medium": 8,
        "low": 3,
        "info": 2,
        "total": 30
      },
      "processingStats": {
        "duration": 330000,
        "tokensUsed": 125000,
        "falsePositivesFiltered": 15,
        "duplicatesRemoved": 8
      }
    },
    "resultId": "result-uuid",
    "createdAt": "2025-11-14T10:35:30.000Z",
    "publicShareUrl": "abc123de"
  }
}
```

**Response (Shared Scan):** `200 OK`

For scans that are part of a shared repository scan (multiple users scanning the same repo/branch):

```json
{
  "success": true,
  "data": {
    "jobId": "your-job-id",
    "projectId": "your-project-id",
    "status": "running",
    "progress": 45,
    "metadata": {
      "repository": "owner/repo",
      "branch": "main"
    },
    "startedAt": "2025-11-14T10:30:00.000Z",
    "completedAt": null,
    "error": null,
    "isSharedScan": true,
    "originalJobId": "original-job-uuid"
  }
}
```

**Job Status Values:**

- `pending` - Job is queued and waiting to start
- `running` - Scan is currently in progress
- `completed` - Scan finished successfully
- `failed` - Scan encountered an error
- `cancelled` - Scan was cancelled by user

**Error Responses:**

- `404 Not Found` - Job not found or doesn't belong to user
- `500 Internal Server Error` - Failed to fetch job

**Example:**

```bash
curl -X GET https://api.vulnzap.com/api/scan/jobs/uuid-v4-job-id \
  -H "x-api-key: your_api_key_here"
```

---

### 4. GitHub Scan Events (SSE)

Server-Sent Events (SSE) endpoint for real-time GitHub scan progress updates.

**Endpoint:** `GET /github/:scanId/events`

**Headers:**
- `x-api-key` (required): Your VulnZap API key

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `scanId` | string | Yes | Job ID from the GitHub scan initiation |

**Connection:**

This endpoint establishes a Server-Sent Events (SSE) connection that streams real-time updates.

**Event Types:**

#### 1. Connected Event

Sent immediately upon successful connection.

```json
{
  "type": "connected",
  "scanId": "uuid-v4-job-id",
  "data": {
    "message": "Connected to scan progress stream",
    "jobStatus": "running",
    "progress": 0
  },
  "timestamp": "2025-11-14T10:30:00.000Z"
}
```

#### 2. Progress Event

Sent periodically during the scan with status updates.

```json
{
  "type": "progress",
  "scanId": "uuid-v4-job-id",
  "data": {
    "message": "Analyzing security patterns in TypeScript files..."
  },
  "timestamp": "2025-11-14T10:31:15.000Z"
}
```

#### 3. Vulnerability Event

Sent when a vulnerability is detected (optional, based on scan configuration).

```json
{
  "type": "vulnerability",
  "scanId": "uuid-v4-job-id",
  "data": {
    "id": "vuln_1731582930000_abc123",
    "type": "SQL_INJECTION",
    "severity": "critical",
    "file": "src/api/users.ts",
    "line": 45
  },
  "timestamp": "2025-11-14T10:32:45.000Z"
}
```

#### 4. Completed Event

Sent when the scan finishes successfully.

```json
{
  "type": "completed",
  "scanId": "uuid-v4-job-id",
  "data": {
    "message": "Scan completed for owner/repo"
  },
  "timestamp": "2025-11-14T10:35:30.000Z"
}
```

#### 5. Failed Event

Sent when the scan encounters an error.

```json
{
  "type": "failed",
  "scanId": "uuid-v4-job-id",
  "data": {
    "message": "Scan failed for owner/repo (main). Won't be billed for this scan."
  },
  "timestamp": "2025-11-14T10:33:00.000Z"
}
```

#### 6. Error Event

Sent for connection-level errors.

```json
{
  "type": "error",
  "scanId": "uuid-v4-job-id",
  "data": {
    "message": "Scan job not found",
    "error": "Invalid scan ID"
  },
  "timestamp": "2025-11-14T10:30:01.000Z"
}
```

**Connection Behavior:**

- Connection stays open until scan completes, fails, or client disconnects
- Events are sent as `data: {json}\n\n` format (SSE standard)
- If auto-cancellation is enabled (default), disconnecting all clients will cancel the scan

**Example (JavaScript):**

```javascript
const eventSource = new EventSource(
  'https://api.vulnzap.com/api/scan/github/uuid-v4-job-id/events',
  {
    headers: {
      'x-api-key': 'your_api_key_here'
    }
  }
);

eventSource.onmessage = (event) => {
  const data = JSON.parse(event.data);
  
  switch(data.type) {
    case 'connected':
      console.log('Connected to scan stream');
      break;
    case 'progress':
      console.log('Progress:', data.data.message);
      break;
    case 'completed':
      console.log('Scan completed!');
      eventSource.close();
      break;
    case 'failed':
      console.error('Scan failed:', data.data.message);
      eventSource.close();
      break;
  }
};

eventSource.onerror = (error) => {
  console.error('SSE error:', error);
  eventSource.close();
};
```

**Example (curl):**

```bash
curl -N -H "x-api-key: your_api_key_here" \
  https://api.vulnzap.com/api/scan/github/uuid-v4-job-id/events
```

---

### 5. Commit Scan Events (SSE)

Server-Sent Events (SSE) endpoint for real-time commit scan progress updates.

**Endpoint:** `GET /commit/:jobId/events`

**Headers:**
- `x-api-key` (required): Your VulnZap API key

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jobId` | string | Yes | Job ID from the commit scan initiation |

**Connection:**

This endpoint establishes a Server-Sent Events (SSE) connection that streams real-time updates.

**Event Types:**

Same event types as GitHub Scan Events (see above):

1. `connected` - Initial connection confirmation
2. `progress` - Scan progress updates
3. `vulnerability` - Vulnerability detection (optional)
4. `completed` - Scan completion
5. `failed` - Scan failure
6. `error` - Connection errors

**Event Format:**

All events follow the same structure as GitHub Scan Events:

```json
{
  "type": "event_type",
  "jobId": "uuid-v4-job-id",
  "data": {
    "message": "Event-specific data"
  },
  "timestamp": "2025-11-14T10:30:00.000Z"
}
```

**Connection Behavior:**

- Connection stays open until scan completes, fails, or client disconnects
- Events are sent as `data: {json}\n\n` format (SSE standard)
- Commit scans are typically faster than full repository scans

**Example (JavaScript):**

```javascript
const eventSource = new EventSource(
  'https://api.vulnzap.com/api/scan/commit/uuid-v4-job-id/events',
  {
    headers: {
      'x-api-key': 'your_api_key_here'
    }
  }
);

eventSource.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log(`[${data.type}]`, data.data.message);
  
  if (data.type === 'completed' || data.type === 'failed') {
    eventSource.close();
  }
};
```

**Example (curl):**

```bash
curl -N -H "x-api-key: your_api_key_here" \
  https://api.vulnzap.com/api/scan/commit/uuid-v4-job-id/events
```

---

## Common Response Codes

| Code | Description |
|------|-------------|
| `200` | Success |
| `400` | Bad Request - Invalid parameters or request body |
| `401` | Unauthorized - Missing or invalid API key |
| `403` | Forbidden - Subscription limit exceeded |
| `404` | Not Found - Resource not found |
| `500` | Internal Server Error |

---

## Rate Limits

Rate limits are enforced based on your subscription plan. When exceeded, you'll receive:

```json
{
  "success": false,
  "error": "Subscription limit exceeded. You have used 100 of 100 scans this month."
}
```

---

## Vulnerability Object Schema

Each vulnerability in the results follows this schema:

**Common Fields (All Vulnerabilities):**

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique vulnerability identifier |
| `type` | string | Vulnerability type (e.g., `SQL_INJECTION`, `XSS`) |
| `severity` | enum | `critical`, `high`, `medium`, `low`, or `info` |
| `confidence` | enum | `high`, `medium`, or `low` |
| `location` | object | Primary location with file, line numbers, and code snippet |
| `description` | string | Clear description of the vulnerability |
| `evidence` | string | Evidence supporting the finding |
| `cwe` | string | CWE identifier (e.g., `CWE-89`) |
| `owasp` | string | OWASP category (e.g., `A03:2021-Injection`) |
| `tags` | array | Tags for categorization |
| `falsePositive` | boolean | Whether this is a false positive |
| `detectionMethod` | enum | `standalone` or `taint` |
| `createdAt` | string | ISO timestamp when found |

**Taint-Specific Fields (Only when `detectionMethod: "taint"`):**

| Field | Type | Description |
|-------|------|-------------|
| `source` | object | Where tainted data originates (e.g., user input) |
| `sink` | object | Where tainted data reaches dangerous operation |
| `taintFlowPath` | array | Step-by-step flow from source to sink |

---

## Best Practices

1. **Polling vs SSE**: Use SSE endpoints for real-time updates. If SSE is not available, poll `/jobs/:jobId` endpoint every 5-10 seconds.

2. **Error Handling**: Always check the `success` field in responses and handle errors appropriately.

3. **Shared Scans**: When multiple users scan the same repository/branch simultaneously, VulnZap optimizes by sharing the scan. Monitor the `isSharedScan` field in responses.

4. **API Key Security**: Never expose your API key in client-side code. Use server-side proxies.

5. **Rate Limiting**: Implement exponential backoff for failed requests and respect rate limits.

---

## Support

For questions or issues:
- Email: support@vulnzap.com
- Documentation: https://docs.vulnzap.com
- Status Page: https://status.vulnzap.com

