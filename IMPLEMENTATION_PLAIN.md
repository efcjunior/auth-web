# Auth Web Implementation Plan

## Project definition

- Repository and application name: `auth-web`
- Location: `applications/auth-web`
- Package/application scope: `@coding4world/auth-web`
- Language: TypeScript
- UI framework: React with Vite
- Microfrontend style: remote application consumable by a host shell
- Auth dependency: `auth-client`
- Source language: English for source code, comments, documentation, UI messages, and logs
- Distribution target: static web assets and Docker image

## Purpose

`auth-web` is the reusable authentication user interface for applications that use `auth-api`.

It should be implemented once and reused by multiple frontend ecosystems. It must not be coupled to Brazil Public Data branding or to `bdi-api`, although those applications can configure it.

Responsibilities:

- login screen
- logout flow
- authenticated session bootstrap from `auth-client`
- redirect after successful login
- user-facing authentication errors
- optional current-user/account summary
- future password/account flows

Non-responsibilities:

- domain business screens
- BDI-specific UI
- resource API data rendering
- user administration UI unless explicitly added in a later phase

## Architecture

The project will be a small microfrontend that can run standalone during development and be mounted by a host shell in production.

Primary modules:

- `app`: standalone development shell
- `auth`: login/logout pages and auth-specific state
- `routing`: local routes and redirect handling
- `config`: runtime configuration from environment or host-provided props
- `integration`: microfrontend expose/mount contract
- `shared`: reusable UI primitives and utilities

`auth-web` will consume `auth-client` instead of implementing direct authentication mechanics itself.

## Microfrontend contract

The first version should support a host shell contract similar to:

```ts
mountAuthWeb({
  container: document.getElementById("auth-root"),
  authApiBaseUrl: "http://localhost:8080",
  audience: "bdi-api",
  appName: "Brazil Public Data",
  redirectAfterLogin: "/",
  onAuthenticated: session => {
    // host shell receives or reloads authenticated state
  },
})
```

The exact implementation can use Vite Module Federation, a build-time library export, or another microfrontend mechanism selected during Phase 1. The key requirement is that `auth-web` remains reusable by more than one host shell.

## Runtime configuration

`auth-web` must be configurable by the host application or by environment variables:

| Configuration | Purpose |
| --- | --- |
| `authApiBaseUrl` | Base URL of `auth-api` |
| `audience` | Required audience requested from `auth-api` |
| `appName` | Name shown in the login experience |
| `logoUrl` | Optional brand logo |
| `redirectAfterLogin` | Destination after login succeeds |
| `redirectAfterLogout` | Destination after logout succeeds |

No default should hardcode `bdi-api` as a universal audience.

## Delivery schedule

Implementation progress:

- [ ] Contract and bootstrap
- [ ] Standalone login experience
- [ ] Auth-client integration
- [ ] Microfrontend integration contract
- [ ] Session, routing, and redirects
- [ ] Error, loading, and accessibility states
- [ ] Theming and host configuration
- [ ] Delivery tooling
- [ ] Integration with Brazil Public Data shell
- [ ] Hardening and release

| Step | Stage | Deliverables | Completion gate |
| --- | --- | --- | --- |
| 1 | Contract and bootstrap | Create React/Vite TypeScript project, test tooling, lint/format tooling, README, CI, Dockerfile, and runtime config skeleton | App starts locally and tests run |
| 2 | Standalone login experience | Implement login page UI, validation, loading states, and basic layout | Login page renders and validates inputs |
| 3 | Auth-client integration | Use `auth-client` for login, refresh, logout, session storage, and error handling | Successful login obtains a session through mocked or local `auth-api` |
| 4 | Microfrontend integration contract | Expose mount/unmount contract and document host integration | Host test app can mount and unmount `auth-web` |
| 5 | Session, routing, and redirects | Implement redirect parameters, post-login navigation, logout route, and authenticated-state bootstrap | Redirect flow works in standalone and mounted modes |
| 6 | Error, loading, and accessibility states | Add Problem Details mapping, invalid credentials messages, audience errors, keyboard navigation, and accessible form labels | UI tests cover errors and accessibility basics |
| 7 | Theming and host configuration | Allow host-provided app name, logo, and minimal theme tokens | Same build can display different host branding |
| 8 | Delivery tooling | Add Docker image publishing, static asset build, CI, release notes, and deployment documentation | Release can publish static assets or Docker image |
| 9 | Integration with Brazil Public Data shell | Mount `auth-web` in the Brazil Public Data frontend shell and validate BDI login flow | User can log in through shell and access BDI frontend |
| 10 | Hardening and release | Security review, storage review, browser compatibility, documentation, and final verification | First stable release is tagged and published |

## Test strategy

- Login form validates required email and password.
- Login submits explicit configured audience.
- Invalid credentials show generic authentication failure.
- Missing or invalid audience shows a clear configuration/authentication error.
- Successful login redirects to the configured target.
- Logout clears local session and redirects.
- Refresh/session bootstrap uses `auth-client`, not duplicated HTTP logic.
- Microfrontend mount/unmount does not leak React roots or event listeners.
- Host-provided configuration overrides default development configuration.
- UI has accessible labels, button states, and keyboard-friendly behavior.

## Acceptance criteria

- `auth-web` uses `auth-client` for auth mechanics.
- `auth-web` does not duplicate business-domain authentication logic.
- `auth-web` does not hardcode Brazil Public Data or BDI-specific assumptions.
- The same build can be configured for multiple host shells.
- The project can run standalone during development.
- The project can be consumed as a microfrontend by a host shell.
- Documentation explains standalone usage and host integration.

## Assumptions

- `auth-api` is already implemented and exposes login, refresh, logout, me, and JWKS.
- `auth-client` will be implemented before or alongside `auth-web`.
- The first version uses the current custom login API, not full OAuth2/OIDC authorization-code flow.
- User-management administration screens are out of scope for the first `auth-web` release.
- Password reset, MFA, email verification, and profile editing are deferred unless added to `auth-api` first.
