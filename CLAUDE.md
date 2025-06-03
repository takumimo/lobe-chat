# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Essential Commands

- `pnpm dev` - Start development server with Turbopack on port 3010
- `pnpm build` - Build for production
- `pnpm test` - Run both app and server tests
- `pnpm test-app` - Run client-side tests only
- `pnpm test-server` - Run server-side tests only
- `pnpm lint` - Run all linting (TypeScript, styles, type-check, circular deps)
- `pnpm type-check` - TypeScript type checking

### Database Commands

- `pnpm db:generate` - Generate Drizzle schema and client migrations
- `pnpm db:migrate` - Run database migrations
- `pnpm db:studio` - Open Drizzle Studio for database inspection
- `pnpm db:push` - Push schema changes to database

### Desktop/Electron

- `pnpm dev:desktop` - Start desktop development server on port 3015
- `pnpm desktop:build` - Build Electron application

### Testing

- Use `pnpm test-app:coverage` for client test coverage
- Use `pnpm test-server:coverage` for server test coverage
- Test files follow `.test.ts/.test.tsx` pattern
- Use `~test-utils` alias for test utilities

## Architecture Overview

### Tech Stack

- **Framework**: Next.js 15 with App Router
- **Database**: PostgreSQL with Drizzle ORM
- **State Management**: Zustand with middleware for persistence
- **Styling**: Antd + antd-style for theming
- **AI/LLM**: Multiple provider support (OpenAI, Anthropic, Google, etc.)
- **Real-time**: Server-Sent Events (SSE) for streaming
- **Testing**: Vitest with Happy-DOM

### Key Architecture Patterns

#### Store Structure (Zustand)

The application uses a modular Zustand store architecture located in `src/store/`:

- **Domain-based slices**: `agent/`, `chat/`, `user/`, `session/`, `tool/`, etc.
- **Selectors pattern**: Each store has dedicated selectors for computed values
- **Middleware**: Custom persistence middleware with IndexedDB and localStorage
- **Type safety**: Full TypeScript integration with proper typing

#### Database Layer

- **Dual database support**: Client-side (PGlite) and server-side (PostgreSQL)
- **Migrations**: Automated with Drizzle Kit in `src/database/migrations/`
- **Models**: Type-safe models in `src/database/models/`
- **Repositories**: Data access layer in `src/database/repositories/`

#### Service Layer

Services in `src/services/` handle business logic:

- `chat.ts` - Core chat functionality and AI provider communication
- `agent.ts` - Agent management and configuration
- `knowledgeBase.ts` - File and knowledge base operations
- `upload.ts` - File upload and processing

#### Feature-Based Components

`src/features/` contains complex, reusable feature components:

- Each feature is self-contained with its own components, hooks, and stores
- Examples: `ChatInput/`, `Conversation/`, `FileManager/`, `AgentSetting/`

### AI Provider Integration

#### Runtime System

- **AgentRuntime**: Abstraction layer for different AI providers
- **Client vs Server**: Supports both client-side and server-side execution
- **Streaming**: Real-time response streaming via SSE
- **Tool Calling**: Function calling support for plugins

#### Provider Configuration

- Supports 40+ AI providers including OpenAI, Anthropic, Google, etc.
- Dynamic provider configuration in `src/config/llm.ts`
- Authentication handling in `src/services/_auth.ts`

### Plugin System

- **Dynamic loading**: Plugins loaded at runtime
- **Security**: Sandboxed execution environment
- **Gateway**: Centralized plugin gateway for API calls
- **Types**: Built-in tools vs custom plugins vs MCP (Model Context Protocol)

### Authentication & Multi-tenancy

- **Flexible auth**: Supports NextAuth, Clerk, and custom OIDC
- **Multi-user**: User isolation and data separation
- **Session management**: Secure session handling

## File Structure Conventions

### Import Aliases

- `@/*` - Maps to `src/*`
- `~test-utils` - Maps to `tests/utils.tsx`

### Key Directories

- `src/app/` - Next.js App Router pages and API routes
- `src/app/(backend)/` - Server-side API routes and middleware
- `src/store/` - Zustand stores with domain separation
- `src/features/` - Complex feature components
- `src/components/` - Simple, reusable UI components
- `src/database/` - Database schemas, models, and migrations
- `src/services/` - Business logic and external API integration
- `src/types/` - TypeScript type definitions
- `src/utils/` - Utility functions
- `locales/` - Internationalization files (18 languages)

### Testing Structure

- Tests co-located with source code using `.test.ts/.test.tsx`
- Integration tests in `tests/` directory
- Mock data in `__mocks__/` and `tests/fixtures/`

## Database Schema

### Core Entities

- **Users**: User profiles and authentication
- **Sessions**: Chat sessions with agents
- **Messages**: Individual chat messages with tool calls
- **Agents**: AI agent configurations
- **Files**: Uploaded files and knowledge base
- **Plugins**: Installed and configured plugins

### Migration Strategy

- Use `pnpm db:generate` to create new migrations
- Migrations are automatically applied during build
- Schema changes require both server and client migration files

## Development Patterns

### State Management

```typescript
// Store structure example
const useStore = create<State>()(
  subscribeWithSelector(
    persist(
      immer((set, get) => ({
        // state and actions
      })),
      {
        name: 'store-name',
        storage: createHyperStorage(),
      },
    ),
  ),
);
```

### API Routes

- Use tRPC for type-safe API calls
- Streaming responses use Server-Sent Events
- Authentication middleware applied automatically

### Component Patterns

- Feature components are self-contained with their own state
- Use Ant Design components as base UI primitives
- Responsive design with mobile-first approach

## Environment Configuration

### Required Environment Variables

- `DATABASE_URL` - PostgreSQL connection string
- `OPENAI_API_KEY` - Default AI provider (others optional)
- `NEXTAUTH_SECRET` - Authentication secret
- `NEXTAUTH_URL` - Application URL

### Optional Configuration

- `NEXT_PUBLIC_CLIENT_DB` - Enable client-side database
- `S3_*` - File storage configuration
- Provider-specific API keys for additional AI services

## Deployment

### Production Build

- `pnpm build` creates optimized production build
- Docker support with `Dockerfile` and `docker-compose.yml`
- Vercel deployment with one-click setup

### Database Setup

- Development: Use Docker Compose for local PostgreSQL
- Production: Configure DATABASE_URL for hosted PostgreSQL
- Migrations run automatically during deployment

This codebase follows modern React/Next.js patterns with strong TypeScript integration, comprehensive testing, and a focus on AI-first user experiences.
