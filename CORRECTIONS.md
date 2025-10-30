# VulnZap Documentation Corrections

## Critical Corrections Made

### 1. Dashboard URL
- ❌ **Incorrect:** `app.vulnzap.com`
- ✅ **Correct:** `vulnzap.com/dashboard`
- **Status:** Fixed across all documentation files

### 2. Package Name
- ❌ **Incorrect:** `npm install -g vulnzap`
- ✅ **Correct:** `npm install -g vulnzap-cli`
- **Status:** Fixed in all installation instructions

### 3. Authentication Method
- ❌ **Incorrect:** `vulnzap auth` command (doesn't exist)
- ✅ **Correct:** `export VULNZAP_API_KEY="your_key"` (environment variable)
- **Status:** Fixed in all authentication sections

### 4. MCP Configuration
- ❌ **Incorrect:** `vulnzap mcp` command
- ✅ **Correct:** `vulnzap secure --ide cursor` or `vulnzap secure --ide windsurf`
- **Status:** Fixed in IDE integration docs

### 5. MCP File Locations
- ✅ **Cursor:** `.cursor/mcp.json`
- ✅ **Windsurf:** `.codeium/windsurf/mcp_config.json`
- **Status:** Corrected with proper JSON structure

## Verified Working Commands

Based on actual implementation:

```bash
# Installation
npm install -g vulnzap-cli

# Authentication
export VULNZAP_API_KEY="your_api_key_here"

# Scanning
vulnzap scan

# MCP Integration (used in config files)
vulnzap secure --ide cursor
vulnzap secure --ide windsurf
```

## Files Updated

1. ✅ `quickstart.mdx` - Fixed URLs, auth method, MCP config
2. ✅ `index.mdx` - Fixed installation command
3. ✅ `docs.json` - Fixed dashboard URLs
4. ✅ `integration/cli.mdx` - Completely rewritten with only verified commands
5. ✅ `features/ide-integration.mdx` - Fixed MCP configuration
6. ✅ All other files - Batch replaced `app.vulnzap.com` → `vulnzap.com/dashboard`

## What Was Removed

Removed all hallucinated CLI commands that don't actually exist:
- `vulnzap auth`
- `vulnzap init`
- `vulnzap mcp`
- `vulnzap fix`
- `vulnzap config`
- `vulnzap cache`
- `vulnzap db`
- `vulnzap packages`
- And many others...

## Remaining Work

Please verify actual feature set and add back ONLY features that actually exist.

The documentation now contains:
- Correct dashboard URL (vulnzap.com/dashboard)
- Correct package name (vulnzap-cli)
- Correct authentication method (environment variables)
- Correct MCP configuration (vulnzap secure --ide <name>)
- Only verified command: `vulnzap scan`

