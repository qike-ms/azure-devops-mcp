# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Azure DevOps MCP (Model Context Protocol) Server that provides access to Azure DevOps services through standardized tools. The server enables AI agents to interact with Azure DevOps APIs for managing projects, work items, builds, releases, repositories, and more.

## Development Commands

### Building and Development
- `npm run build` - Compile TypeScript to JavaScript in `dist/` directory
- `npm run watch` - Watch mode for continuous compilation during development
- `npm run prepare` - Runs build automatically (used by npm lifecycle)
- `npm run clean` - Remove the `dist/` directory
- `npm run validate-tools` - Validate tool names and TypeScript compilation

### Code Quality
- `npm run eslint` - Run ESLint linter
- `npm run eslint-fix` - Run ESLint with automatic fixes
- `npm run format` - Format code with Prettier
- `npm run format-check` - Check code formatting without making changes

### Testing
- `npm test` - Run Jest test suite
- Coverage thresholds are set to 40% for branches, functions, lines, and statements
- Test files are located in `test/` directory and follow the pattern `**/?(*.)+(spec|test).[jt]s?(x)`

### Running the Server
- `npm start` - Start the MCP server (requires Azure DevOps organization name as argument)
- `npm run inspect` - Run the server with MCP inspector for debugging

### Local Development Setup
For source installation (development mode):
1. Clone repository and run `npm install`
2. Configure `.vscode/mcp.json` with local command: `"mcp-server-azuredevops"`
3. The server requires an Azure DevOps organization name as the first argument

## Architecture

### Core Components
- **Entry Point**: `src/index.ts` - Main server initialization, Azure authentication, and MCP server setup
- **Tool Configuration**: `src/tools.ts` - Aggregates all tool modules and configures them with the MCP server
- **Authentication**: Uses `@azure/identity` with `DefaultAzureCredential` for Azure DevOps API access
- **Client Setup**: Creates Azure DevOps WebApi clients with proper user agent and authentication

### Tool Organization
Tools are organized by Azure DevOps service area in `src/tools/`:
- `core.ts` - Projects and teams management
- `work.ts` - Iterations and team work management
- `workitems.ts` - Work item CRUD operations and queries
- `repos.ts` - Repository and pull request management
- `builds.ts` - Build pipelines and execution
- `releases.ts` - Release management
- `testplans.ts` - Test planning and execution
- `wiki.ts` - Wiki content management
- `search.ts` - Search across code, wiki, and work items
- `advsec.ts` - Advanced Security alerts management
- `auth.ts` - Authentication utilities

### Key Patterns
- Each tool module follows the pattern: `configure[ServiceName]Tools(server, tokenProvider, connectionProvider, ...)`
- All tools use Zod schemas for input validation and JSON schema generation
- Authentication is handled centrally through token and connection providers
- User agent composition includes package version and MCP client information

### Environment Variables
- `ADO_MCP_AZURE_TOKEN_CREDENTIALS` - Optional override for Azure token credentials
- Falls back to `AZURE_TOKEN_CREDENTIALS = "dev"` for development

### Project Structure
- `src/` - TypeScript source code
- `dist/` - Compiled JavaScript output (created by build)
- `test/` - Jest test files with mocks in `test/mocks/`
- `docs/` - Documentation files (FAQ, HOWTO, TROUBLESHOOTING)

## Important Notes

### Adding New Tools
- Follow existing patterns in tool modules under `src/tools/`
- Use Azure DevOps TypeScript clients when available, fall back to direct API calls only when necessary
- Add tool configuration to `configureAllTools()` in `src/tools.ts`
- Include comprehensive input validation with Zod schemas
- Follow the established naming convention: `[service]_[action]_[object]`

### Testing
- Mock Azure DevOps API responses in `test/mocks/`
- Test files mirror the source structure in `test/src/`
- Use Jest with ts-jest preset for TypeScript support

### Code Quality Standards
- TypeScript with strict mode enabled
- ESLint configuration with Prettier integration
- All files must include Microsoft copyright headers
- Follow existing code patterns for consistency