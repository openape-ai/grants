# @openape/grants

OpenAPE authorization server library — grant lifecycle management, AuthZ-JWT issuance/verification, and pluggable storage.

## Installation

```bash
npm install @openape/grants
```

## API

```typescript
import {
  approveGrant,
  createGrant,
  denyGrant,
  InMemoryGrantStore,
  introspectGrant,
  issueAuthzJWT,
  revokeGrant,
  useGrant,
  verifyAuthzJWT,
} from '@openape/grants'

const store = new InMemoryGrantStore()

// Create a pending grant
const grant = await createGrant({
  requester: 'agent@example.com',
  target: 'api.example.com',
  grant_type: 'once',
  permissions: ['read:data'],
}, store)

// Approve (human decision)
const approved = await approveGrant(grant.id, 'admin@example.com', store)

// Issue AuthZ-JWT from approved grant
const jwt = await issueAuthzJWT(approved, 'https://openape.example.com', privateKey)

// Verify AuthZ-JWT
const result = await verifyAuthzJWT(jwt, { publicKey, expectedAud: 'api.example.com' })

// Use a grant (marks 'once' grants as used)
const used = await useGrant(grant.id, store)

// Revoke, introspect
await revokeGrant(grant.id, store)
const status = await introspectGrant(grant.id, store)
```

### Grant Types

- `once` — single use, marked as `used` after first use
- `timed` — valid for a duration, auto-expires
- `always` — persistent, renewable (1hr AuthZ-JWT TTL)

### Pluggable Storage

Implement the `GrantStore` interface for production use. Ships with `InMemoryGrantStore`.

## License

MIT
