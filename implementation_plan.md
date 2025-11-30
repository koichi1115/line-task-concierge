# Implementation Plan: LINE Task Management Bot

## Goal
Build a personal task management LINE Bot that supports individual and group contexts, utilizing AI for recommendations.
The system will be deployed on a serverless architecture to minimize cost and maintenance.

## User Review Required
> [!IMPORTANT]
> **Tech Stack Selection**:
> - **Framework**: [Hono](https://hono.dev/) (Lightweight, fast web framework)
> - **Runtime**: [Cloudflare Workers](https://workers.cloudflare.com/) (Free, low latency, no cold starts)
> - **Database**: [Supabase](https://supabase.com/) (PostgreSQL, Free tier available)
> - **AI**: OpenAI API (gpt-4o-mini)
> - **Language**: TypeScript
>
> Please confirm if you have accounts for Cloudflare, Supabase, and OpenAI, or are willing to create them.

## Proposed Changes

### 1. Project Setup & GitHub
- Initialize a new Node.js project with Hono template.
- Configure TypeScript and ESLint/Prettier.
- Initialize Git repository and set up `.gitignore`.
- **Action**: User to create a remote GitHub repository and push the code.

### 2. Database Schema (Supabase)
- Create tables in Supabase:
    - `users`: Store user profiles.
    - `groups`: Store group contexts.
    - `tasks`: Store task details with `owner_id` and `owner_type`.
    - `messages`: (Optional) Cache for quote-reply feature.

### 3. Backend Implementation (Hono)
#### [NEW] `src/index.ts`
- Main entry point.
- Handle LINE Webhook verification.
- Route events to appropriate handlers.

#### [NEW] `src/handlers/`
- `messageHandler.ts`: Handle text messages (RegExp or AI intent matching).
- `postbackHandler.ts`: Handle button taps (Rich Menu, Flex Message).
- `joinHandler.ts`: Handle bot joining groups.

#### [NEW] `src/services/`
- `lineService.ts`: Wrapper for LINE Messaging API (Reply, Push, Profile, Flex Message).
- `taskService.ts`: CRUD operations against Supabase.
- `aiService.ts`: Logic for NLU and Task Recommendations using OpenAI.

#### [NEW] `src/utils/`
- `dateUtils.ts`: Smart date parsing logic.

### 4. Infrastructure & Deployment
- Configure `wrangler.toml` for Cloudflare Workers.
- Set up environment variables (Channel Secret, Access Token, DB URL, OpenAI Key).

## Verification Plan

### Automated Tests
- Unit tests for `taskService` and `dateUtils` using Vitest.
- Mock tests for `aiService`.

### Manual Verification
1. **Deploy to Cloudflare Workers (Staging/Dev)**.
2. **Connect to LINE Official Account**.
3. **Test Scenarios**:
    - Add task via text ("Buy milk").
    - List tasks (Check Flex Message display).
    - Complete task (Tap button).
    - Ask for recommendation ("What should I do?").
    - Add bot to a group and verify task isolation.
    - Test quote-reply task creation.
