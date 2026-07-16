# Auth Web

Reusable authentication web UI for applications that use `auth-api`.

`auth-web` provides user-facing authentication screens such as login and logout. It should be consumed by frontend shells and domain microfrontends instead of each project implementing its own login interface.

## Current phase

Planning only. No implementation phase has been completed yet.

Latest planned phase:

- [ ] Phase 1 — Contract and bootstrap

The repository currently contains the implementation plan and README only.

## Relationship with other projects

| Project | Responsibility |
| --- | --- |
| `auth-api` | Backend authentication, users, refresh tokens, JWT issuing, JWKS |
| `auth-client` | Reusable TypeScript authentication engine |
| `auth-web` | Reusable login/logout UI built on top of `auth-client` |
| Web shell projects | Host microfrontends and provide runtime configuration |
| Domain web projects | Business UI that uses authenticated API access |

## Intended usage

The project should be usable in two modes.

### Standalone development

Run `auth-web` by itself during development to test authentication flows against local `auth-api`.

Future command shape:

```bash
npm install
npm run dev
```

### Mounted by a host shell

A host shell should be able to mount `auth-web` with configuration:

```ts
mountAuthWeb({
  container: document.getElementById("auth-root"),
  authApiBaseUrl: "http://localhost:8080",
  audience: "bdi-api",
  appName: "Brazil Public Data",
  redirectAfterLogin: "/",
})
```

The host application supplies the audience. `auth-web` must not hardcode `bdi-api`.

## Planned features

- Login page
- Logout flow
- Authenticated session bootstrap
- Integration with `auth-client`
- Host-provided runtime configuration
- Microfrontend mount/unmount contract
- Redirect after login/logout
- User-facing error states
- Basic theming/branding inputs
- Docker/static delivery workflow

## Out of scope for the first release

- Domain business screens
- BDI-specific UI
- Public user registration
- Password reset
- MFA
- Email verification
- User-management administration UI

## Implementation plan

See [IMPLEMENTATION_PLAIN.md](IMPLEMENTATION_PLAIN.md).

## Development

Development commands will be added in Phase 1 after the frontend tooling is created.

Expected future commands:

```bash
npm install
npm test
npm run build
npm run dev
```

## Design principle

Authentication UI should be implemented once and reused. Domain applications should consume authentication state and access tokens; they should not each build their own login page.
