# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

`anki-profiles-cc-sdk-codex` is a React-based frontend application that digitizes paper profile books (交換ノート・クラス名簿) and optimizes them for spaced repetition learning. The app manages person-specific Q&A records, converts them into flashcards, and generates review queues using a spaced repetition workflow. Built with SDK-oriented architecture for future third-party integration.

**Language Note**: Think in English, but generate responses in Japanese (思考は英語、回答の生成は日本語で行うように).

## Development Commands

### Core Workflow
```bash
# Development server (Vite HMR)
pnpm dev

# Production build (TypeScript compilation + Vite bundling)
pnpm build

# Linting
pnpm lint

# Preview production build
pnpm preview
```

### Testing
```bash
# Run tests with Vitest (when configured)
pnpm vitest

# Run Storybook (when configured)
pnpm storybook dev
```

### Package Management
- **Required**: pnpm 10.21+ (enforced by `packageManager` field)
- **Node.js**: 20+
- **Catalog Mode**: Uses `pnpm-workspace.yaml` catalog for centralized dependency versioning
- All dependencies use catalog references for consistency

## Architecture & Code Organization

### Tech Stack Core
- **Language**: TypeScript 5.9+ (strict mode, ES2023 target)
- **Framework**: React 19 + React StrictMode
- **Build Tool**: Vite 5 with React Compiler plugin (`babel-plugin-react-compiler`)
- **State Management**: Zustand (designed for SDK extraction)
- **Styling**: Tailwind CSS + PostCSS (global styles in `src/index.css`)

### TypeScript Configuration
Extremely strict type safety enabled:
- `strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`
- `allowJs: false` (no JavaScript allowed)
- `useUnknownInCatchVariables` (explicit error handling)
- `noImplicitReturns`, `noPropertyAccessFromIndexSignature`
- Project references: `tsconfig.app.json` and `tsconfig.node.json`

### Directory Structure

```
src/
├── main.tsx              # Entry point (React 19 createRoot)
├── App.tsx               # Root component
├── index.css             # Global styles, Tailwind directives
├── App.css               # App-specific styles
├── assets/               # Bundled static assets (SVG, images)
└── features/<domain>/    # Feature-first organization (to be added)
    ├── components/
    ├── hooks/
    └── stores/           # Zustand stores per feature

.kiro/
├── steering/             # Project-wide policies and standards
│   ├── product.md        # Product vision and use cases
│   ├── tech.md           # Technology decisions
│   └── structure.md      # Code organization principles
├── specs/                # Active feature specifications
└── settings/             # Templates and rules for AI-DLC workflow
```

### Code Organization Principles
1. **Feature-First**: Group by domain (`src/features/<domain>/`), not by type
2. **Entry Point Purity**: `main.tsx` only handles UI composition, no business logic
3. **Zustand Store Isolation**: Place stores in `src/features/<domain>/stores/` for SDK extraction
4. **Loose Coupling**: Separate UI layer from data model layer for SDK-oriented design
5. **Documentation-Driven**: Update `.kiro/steering/` and `.kiro/specs/` before implementing

### Naming Conventions
- **React Components**: `PascalCase.tsx` (default export with descriptive suffix like `View`/`Section`)
- **Logic/Hooks/Stores**: `camelCase.ts` (hooks prefixed with `use`, e.g., `useProfileCardsStore`)
- **Import Organization**: Managed by `@trivago/prettier-plugin-sort-imports`

### Import Paths
- **Current**: Relative imports (`../components/ProfileCard`)
- **Future**: Path aliases (`@/` → `src/`) may be configured in both `tsconfig.json` and `vite.config.ts`

## Quality Tools

### Linting & Formatting
- **ESLint**: Extensive plugin coverage including:
  - `eslint-plugin-functional` (immutability recommendations)
  - `eslint-plugin-security`, `eslint-plugin-sonarjs` (code quality)
  - `eslint-plugin-strict-dependencies`, `eslint-plugin-boundaries` (architectural boundaries)
  - React-specific: `react-hooks`, `react-refresh`, `react` configs
- **Prettier**: Configured with `sort-imports` and `tailwindcss` plugins
- **Stylelint**: CSS/Tailwind validation with performance and browser compatibility checks
- **Lefthook**: Git hooks for pre-commit/pre-push automation (configuration exists but needs setup)

### Testing Stack
- **Unit/Integration**: Vitest + Testing Library (DOM/React)
- **API Mocking**: MSW (Mock Service Worker)
- **Visual Regression**: Storybook + Chromatic
- **Mutation Testing**: Stryker (for verifying algorithm robustness)
- **Accessibility**: Storybook addon-a11y

### Documentation & Content Quality
- **Textlint**: Japanese technical writing validation
  - Presets: `@textlint-ja/textlint-rule-preset-ai-writing`, `textlint-rule-preset-ja-technical-writing`
- **Remark**: Markdown linting with GFM support
- **Secretlint**: Prevent credential leaks
- **Dependency Cruiser**: Architectural dependency validation

## AI-DLC Workflow (Kiro-style Spec-Driven Development)

This project uses a structured AI Development Life Cycle with three phases:

### Phase 0 (Optional): Steering Setup
```bash
/prompts:kiro-steering           # Initialize default steering files
/prompts:kiro-steering-custom    # Add custom steering (auth, API standards, etc.)
```

### Phase 1: Specification
```bash
/prompts:kiro-spec-init "feature description"          # Initialize spec
/prompts:kiro-spec-requirements {feature}              # Define requirements (EARS format)
/prompts:kiro-validate-gap {feature}                   # Optional: Gap analysis for existing code
/prompts:kiro-spec-design {feature} [-y]               # Create technical design
/prompts:kiro-validate-design {feature}                # Optional: Design review
/prompts:kiro-spec-tasks {feature} [-y]                # Break down into tasks
```

### Phase 2: Implementation
```bash
/prompts:kiro-spec-impl {feature} [tasks]              # Implement feature tasks
/prompts:kiro-validate-impl {feature}                  # Optional: Post-implementation validation
/prompts:kiro-spec-status {feature}                    # Check progress anytime
```

### Approval Workflow
- **Human review required** at each phase boundary (Requirements → Design → Tasks → Implementation)
- Use `-y` flag only for intentional fast-tracking (bypasses approval)
- Keep `.kiro/steering/` files current and verify alignment before implementation

### Project Memory System
- **`.kiro/steering/`**: Project-wide policies (architecture, naming, security, tech stack)
- **Local `AGENTS.md`**: Feature/library-specific context (e.g., `src/lib/payments/AGENTS.md`)
- **`.kiro/specs/<feature>/`**: Active specification notes for each feature

## Key Technical Decisions

1. **React Compiler**: Enabled from the start (`babel-plugin-react-compiler`) for automatic re-render optimization
2. **pnpm Catalog**: Centralized dependency versioning in `pnpm-workspace.yaml` for easier updates
3. **Zustand for SDK**: State management designed for future extraction as standalone SDK
4. **Strict TypeScript**: Maximum type safety with `noUncheckedIndexedAccess` and related flags
5. **Lefthook Automation**: Pre-commit/push quality checks (ESLint, Textlint, Secretlint) before CI
6. **Feature-First Organization**: Scale by domain, not by technical layer

## Working with this Codebase

### Before Implementation
1. Check `.kiro/steering/` for project-wide standards
2. Review relevant specs in `.kiro/specs/<feature>/`
3. Check for local `AGENTS.md` files in feature directories
4. Run `/prompts:kiro-spec-status {feature}` to understand current state

### During Development
1. Follow strict TypeScript settings (no `any`, handle all edge cases)
2. Keep business logic out of `App.tsx` and `main.tsx`
3. Place Zustand stores in feature-specific directories
4. Use Tailwind utilities for styling; avoid inline styles
5. Default export for components, named exports for utilities

### Code Quality Standards
- No security vulnerabilities (XSS, injection, OWASP Top 10)
- Immutability patterns encouraged by `eslint-plugin-functional`
- Architectural boundaries enforced by `eslint-plugin-strict-dependencies` and `eslint-plugin-boundaries`
- Test coverage for spaced repetition algorithms (Stryker mutation testing)
- Accessibility validation via Storybook addon-a11y

## Common Patterns

### React Component
```tsx
// src/features/profiles/components/ProfileCard.tsx
export default function ProfileCard() {
  return <div>...</div>
}
```

### Zustand Store
```ts
// src/features/profiles/stores/useProfileCardsStore.ts
import { create } from 'zustand'

export const useProfileCardsStore = create((set) => ({
  cards: [],
  addCard: (card) => set((state) => ({ cards: [...state.cards, card] }))
}))
```

### Import Order (Prettier auto-sorted)
```tsx
import { useState } from 'react'           // External
import { useProfileCardsStore } from '@/features/profiles/stores'  // Internal
import reactLogo from './assets/react.svg' // Assets
```

## Additional Resources

- **Steering Files**: `.kiro/steering/product.md`, `tech.md`, `structure.md`
- **AGENTS.md**: Describes AI-DLC workflow and project memory system
- **Kiro Templates**: `.kiro/settings/templates/` for specs and steering
- **Kiro Rules**: `.kiro/settings/rules/` for design principles, EARS format, gap analysis, etc.
