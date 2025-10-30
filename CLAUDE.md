# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the documentation repository for **VulnZap**, a security scanning tool for agentic coding. The documentation is built with **Mintlify**, a documentation framework that converts MDX files into a polished documentation website.

VulnZap provides real-time security scanning for AI-generated code with agent-aware context understanding, OWASP-aligned vulnerability detection, and merge-ready patches.

## Technology Stack

- **Documentation Framework:** Mintlify
- **Content Format:** MDX (Markdown with JSX components)
- **Configuration:** `docs.json` (Mintlify configuration file)
- **API Documentation:** OpenAPI/Swagger specification at `api-reference/openapi.json`

## Development Commands

### Preview Documentation Locally

```bash
# Install Mintlify CLI globally
npm i -g mint

# Run development server (from repository root where docs.json is located)
mint dev

# View at http://localhost:3000
```

### Update Mintlify CLI

```bash
mint update
```

## Repository Structure

```
docs/
├── docs.json                 # Mintlify configuration (navigation, theme, metadata)
├── index.mdx                 # Homepage
├── quickstart.mdx            # Quick start guide
├── how-it-works.mdx          # Architecture and methodology
├── api-reference/            # API documentation
│   ├── introduction.mdx
│   ├── authentication.mdx
│   ├── openapi.json          # OpenAPI spec (currently placeholder)
│   └── endpoint/*.mdx        # API endpoint documentation
├── features/                 # Core features documentation
│   ├── dashboard.mdx
│   ├── scanning.mdx
│   ├── security-coverage.mdx
│   └── ide-integration.mdx
├── integration/              # Integration guides
│   ├── cli.mdx
│   ├── ci-cd.mdx
│   └── mcp-protocol.mdx
├── team/                     # Team and enterprise docs
│   ├── management.mdx
│   ├── deployment-options.mdx
│   └── compliance.mdx
├── resources/                # Additional resources
│   ├── pricing.mdx
│   ├── supported-languages.mdx
│   ├── troubleshooting.mdx
│   └── faq.mdx
├── ai-tools/                 # AI tool integrations
├── images/                   # Image assets
├── logo/                     # Logo files
└── snippets/                 # Reusable MDX snippets
```

## Content Architecture

### Navigation Structure

The documentation is organized into two main tabs (defined in `docs.json`):

1. **Documentation Tab:**
   - Getting Started (index, quickstart, how-it-works)
   - Core Features (dashboard, scanning, security-coverage, ide-integration)
   - Integration (cli, ci-cd, mcp-protocol)
   - Team & Enterprise (management, deployment-options, compliance)
   - Resources (pricing, supported-languages, troubleshooting, faq)

2. **API Reference Tab:**
   - API Documentation (introduction, authentication)
   - Endpoints (scan, findings, projects, patches)

### Core Product Details

**Verified CLI Commands:**
- `vulnzap scan` - Main scanning command
- `vulnzap secure --ide <ide_name>` - MCP integration for IDEs (cursor, windsurf)

**Authentication:**
- Uses environment variable: `VULNZAP_API_KEY`
- API keys obtained from: `vulnzap.com/dashboard` → Settings → API Keys

**MCP Integration Paths:**
- Cursor: `.cursor/mcp.json`
- Windsurf: `.codeium/windsurf/mcp_config.json`

**Supported Languages:**
- Production ready: JavaScript, TypeScript, Python
- Coming soon: Go, Rust, Java, .NET, Ruby, PHP, Docker, Terraform, Kubernetes, Swift

## Working with MDX Files

### Mintlify Components

The documentation uses Mintlify-specific components:

```mdx
<Card title="..." icon="..." href="...">Content</Card>
<CardGroup cols={2}>...</CardGroup>
<Accordion title="...">Content</Accordion>
<AccordionGroup>...</AccordionGroup>
<Tip>Helpful tip</Tip>
<Warning>Warning message</Warning>
<Info>Information</Info>
<Check>Success message</Check>
<Steps>...</Steps>
<Step title="...">Content</Step>
<Tabs>...</Tabs>
<Tab title="...">Content</Tab>
<CodeGroup>...</CodeGroup>
<ParamField path="..." type="..." required>...</ParamField>
<Columns cols={2}>...</Columns>
```

### Frontmatter

Every MDX file must have frontmatter:

```mdx
---
title: "Page Title"
description: "Page description for SEO"
icon: "icon-name"  # Optional, Font Awesome icon
---
```

### Code Blocks

Use language-specific code blocks with optional titles:

````mdx
```bash
command here
```

```javascript filename.js
code here
```
````

## Important Constraints

### Accuracy Requirements

**CRITICAL:** The `CORRECTIONS.md` file documents major corrections made to the documentation. When making changes:

1. **Only document features that actually exist** - Do not invent CLI commands, options, or features
2. **Verify URLs** - Use `vulnzap.com/dashboard` (NOT `app.vulnzap.com`)
3. **Verify package names** - The CLI package may have specific naming (check CORRECTIONS.md)
4. **Verify commands** - Only use commands documented in the CORRECTIONS.md verified list

### Content Guidelines

1. **No placeholder content** - The OpenAPI spec (`api-reference/openapi.json`) is currently a Mintlify template and needs to be replaced with actual VulnZap API specification
2. **Maintain tone** - Technical, precise, production-grade (avoid marketing fluff)
3. **Link consistency** - Internal links use relative paths (e.g., `/quickstart`), external links use full URLs
4. **Component usage** - Use Mintlify components for better UX (Cards, Accordions, etc.)

## Publishing

Changes are automatically deployed to production when pushed to the `main` branch via Mintlify's GitHub integration. No manual build or deploy steps are required.

## Key Concepts

### VulnZap Architecture

- **Agent-aware scanning** - Understands AI prompts, tool outputs, and generated diffs
- **MCP protocol integration** - Native integration with Cursor, Windsurf, Cline
- **Exploitability-first ranking** - Focuses on reachable, real-world vulnerabilities
- **OWASP-aligned detection** - Detects SQLi, XSS, path traversal, command injection, etc.
- **Context-aware patches** - Style-preserving, framework-aware fixes

### Scanning Modes

1. **Inline scanning** - Real-time diff-based scanning as you code
2. **Repository scanning** - Full codebase analysis with call graphs
3. **CI/CD scanning** - Automated pipeline integration
4. **Agent-aware scanning** - MCP-based guardrails for AI coding tools

## Common Tasks

### Adding a New Page

1. Create the MDX file in the appropriate directory
2. Add frontmatter with title, description, and icon
3. Update `docs.json` navigation to include the new page
4. Use Mintlify components for consistent styling
5. Preview with `mint dev`

### Updating Navigation

Edit `docs.json` → `navigation` → `tabs` → `groups` → `pages` array. Page paths are relative to the docs root without the `.mdx` extension.

### Updating Theme/Branding

Edit `docs.json` → `colors`, `logo`, `favicon`, `navbar`, `footer` sections.

### Working with API Documentation

The API reference section is designed to work with OpenAPI specifications. The placeholder `openapi.json` should be replaced with the actual VulnZap API specification. Mintlify will automatically generate endpoint documentation from a valid OpenAPI spec.
