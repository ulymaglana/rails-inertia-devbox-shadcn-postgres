# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

### Development
```bash
bin/setup                    # Install deps, prepare DB, start server
bin/dev                      # Start Rails + Vite dev servers (Procfile.dev)
```

### Testing
```bash
bin/rails spec               # Run all RSpec tests
bin/rspec spec/path_spec.rb  # Run a single spec file
bin/rspec spec/path_spec.rb:42  # Run a specific test by line number
```

### Code Quality
```bash
npm run check                # TypeScript type checking
npm run lint                 # ESLint (frontend)
npm run lint:fix             # ESLint with auto-fix
npm run format               # Prettier check
npm run format:fix           # Prettier auto-format
bin/rubocop                  # Ruby linting (Rails Omakase style)
bin/brakeman                 # Security analysis
```

## Architecture

### Inertia.js Integration
This is a monolithic Rails app using Inertia.js to connect the Rails backend with a React frontend. Controllers render Inertia pages instead of traditional views.

- **InertiaController** (`app/controllers/inertia_controller.rb`): Base controller for Inertia pages. Uses `inertia_config default_render: true` for automatic page resolution and `inertia_share` to pass auth data to all pages.
- **Page components** live in `app/frontend/pages/` and map to controller actions (e.g., `dashboard/index.tsx` → `DashboardController#index`)
- **Inertia entrypoint** (`app/frontend/entrypoints/inertia.tsx`): Configures the React app, page resolution, and default layout

### Frontend Structure (`app/frontend/`)
- `pages/` - Inertia page components (mapped from controller actions)
- `components/` - React components (app-specific)
- `components/ui/` - shadcn/ui components
- `layouts/` - Page layouts (auth, app, settings)
- `hooks/` - Custom React hooks
- `types/index.ts` - Shared TypeScript types (User, Session, Auth, SharedData)
- `routes/index.d.ts` - Generated route types from js-routes gem

### Authentication
Uses Authentication Zero pattern with cookie-based sessions:
- `Current` model holds request-scoped `session` and `user` via CurrentAttributes
- `ApplicationController` authenticates via signed `session_token` cookie
- Test helper `sign_in_as(user)` available in request/system specs

### Path Aliases
TypeScript uses `@/` and `~/` as aliases for `app/frontend/` (configured in tsconfig.app.json).

### Code Style
- Frontend: ESLint with TypeScript type-checked rules, Prettier, import ordering enforced
- Backend: RuboCop Rails Omakase
- Use `type` imports for TypeScript types (`@typescript-eslint/consistent-type-imports`)
