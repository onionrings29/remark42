# Remark42 Development Guidelines

## Build/Test/Lint Commands
- **Backend**:
  - Run server: `make rundev`
  - Build: `make backend`
  - Race test: `make race_test`
- **Backend Testing**:
  - Run all tests: `cd backend/app && go test -timeout=60s -count 1 ./...`
  - Run single test: `cd backend/app && go test -run TestName ./path/to/package`
  - **IMPORTANT**: Run example tests: `cd backend/_example/memory_store && go test -race ./... && go build -race ./...`
- **Frontend**:
  - Development: `cd frontend && pnpm dev:app`
  - Tests: `cd frontend && pnpm test`
- **Lint**:
  - Backend: `cd backend && golangci-lint run`
  - **IMPORTANT**: Example lint: `cd backend/_example/memory_store && golangci-lint run --config ../../.golangci.yml`
  - Frontend: `cd frontend && pnpm lint`
  - **Before committing**: Always run tests and linter on both main backend AND examples

## Code Style
- **Backend**: Formatting with golangci-lint, strict error handling
- **Frontend**: TypeScript with ESLint, Stylelint and Prettier
- **Imports**: Group stdlib, external packages, then internal packages
- **CSS**: CSS Modules for new components (`component.module.css`)

## Key Backend Packages
- **Web/API**: `github.com/go-chi/chi/v5`, `github.com/go-pkgz/rest`
- **Auth**: `github.com/go-pkgz/auth/v2`
- **Logging**: `github.com/go-pkgz/lgr`
- **Testing**: `github.com/stretchr/testify`
- **Notifications**: `github.com/go-pkgz/notify`

## Repository Structure
- Backend: Go server using BoltDB for storage
- Frontend: Preact/Redux-based UI with iframe embedding

---

## saulutions.ca Custom Theme

This fork is customized for [saulutions.ca](https://saulutions.ca). **Only CSS/styling is changed.** No logic changes. The server binary is upstream - we only rebuild the frontend.

### Changed Files

1. **`frontend/apps/remark42/app/styles/custom-properties.css`** - Core color variables
   - Remapped all teal/neutral colors to Tokyo Night blue palette
   - Light mode: blue primary (#0059d6), blue-gray neutrals
   - Dark mode: sky-blue primary (#78cff7), blue-tinted dark grays (#16181d base)
   - Background set to transparent for seamless iframe embedding

2. **`frontend/apps/remark42/app/styles/global.css`** - Font + transparent body
   - Added Outfit font family import
   - Set body background to transparent

3. **Component CSS** (border-radius tweaks from commit `9d8d392b`):
   - Buttons: 4px → 8px, Inputs: 4px → 8px, Forms: 2px → 8px
   - Dropdowns: 3px → 8px, Code blocks: 3px → 6px
   - Auth components: various small increases
   - Blockquotes: added primary accent border + tinted bg

### Do NOT Change

- **Avatar sizing** - Upstream `.avatar` uses `max-width: 100%` inside 24x24px `.comment__avatar` container. Changing border-radius to 50% or altering width/height breaks mobile layout. Keep at `border-radius: 4px`.
- **Layout/positioning CSS** - Only change colors and border-radius
- **Any TypeScript/JavaScript** - We use the upstream binary, not a custom server build

### Build Frontend

```bash
cd frontend/apps/remark42
pnpm install    # first time only
pnpm build      # outputs to public/
```

### Server Deployment

- **Server:** 10.129.20.49 (root or remark42 user, password: awantasayo)
- **Service:** `remark42.service` (systemd)
- **Binary:** `/home/remark42/remark42`
- **Config:** `/etc/remark42.env`
- **Custom frontend dir:** `/home/remark42/web/` (WEB_ROOT env var)
- **URL:** https://comments.saulutions.ca
- **Admin ID:** `github_b7cc9d6fd46a31b1e10904c8acbdb2a880392f95`

```bash
# Deploy updated frontend
sshpass -p 'awantasayo' scp -r public/* root@10.129.20.49:/home/remark42/web/
ssh root@10.129.20.49 systemctl restart remark42
```

### Color Mapping Reference

| Variable | Role | Dark Value | Light Value |
|----------|------|-----------|-------------|
| --color8 | Form/page bg | #16181d | #ffffff |
| --color22 | Input bg | #1d1f27 | #f5f7fa |
| --color9 | Primary accent | #78cff7 | #0059d6 |
| --color16 | Borders | #2e3040 | #d0d7de |
| --primary-color | RGB triplet | 120,207,247 | 0,89,214 |

### Upstream Sync

```bash
git remote add upstream https://github.com/umputun/remark42.git  # once
git fetch upstream
git merge upstream/master
# Resolve conflicts in custom-properties.css
```
