---
order: 0
title: "Auth"
format: "code"
---

## `createKey()`

Creates a new key for a user.

```ts
const createKey: (
	userId: string,
	keyData: {
		providerId: string;
		providerUserId: string;
		password: string | null;
	}
) => Promise<Key>;
```

##### Parameters

| name                     | type             | description                      |
| ------------------------ | ---------------- | -------------------------------- |
| `userId`                 | `string`         | The user id of the key to create |
| `keyData.providerId`     | `string`         | The provider id of the key       |
| `keyData.providerUserId` | `string`         | The provider user id of the key  |
| `keyData.password`       | `string \| null` | The password for the key         |

##### Returns

| type      | description |
| --------- | ----------- |
| [`Key`]() | A new key   |

##### Errors

| name                    |
| ----------------------- |
| `AUTH_INVALID_USER_ID`  |
| `AUTH_DUPLICATE_KEY_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

const key = await auth.createKey(userId, {
	providerId: "email",
	providerUserId: "user@example.com",
	password: "123456"
});

const key = await auth.createKey(userId, {
	providerId: "github",
	providerUserId: githubUserId,
	password: null
});
```

## `createSession()`

Creates a new session for a user.

```ts
const createSession: (
	userId: string,
	options: {
		attributes: Lucia.DatabaseSessionAttributes;
	}
) => Promise<Session>;
```

##### Parameters

`options` can be undefined if `Lucia.DatabaseSessionAttributes` is an empty object.

| name                 | type                                  | description                          |
| -------------------- | ------------------------------------- | ------------------------------------ |
| `userId`             | `string`                              | The user id of the session to create |
| `options.attributes` | [`Lucia.DatabaseSessionAttributes`]() | Database session attributes          |

##### Returns

| type          | description   |
| ------------- | ------------- |
| [`Session`]() | A new session |

##### Errors

| name                   |
| ---------------------- |
| `AUTH_INVALID_USER_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

const session = await auth.createSession(userId, {
	attributes: {
		created_at: new Date()
	}
});
```

## `createSessionCookie()`

Creates a session in the form of [`Cookie`](). Returns a blank session cookie that will override the existing cookie and clears them if `null` is provided as parameter `session`.

```ts
const createSessionCookie: (session: Session | null) => Cookie;
```

##### Parameters

| name      | type                    | description                    |
| --------- | ----------------------- | ------------------------------ |
| `session` | [`Session`]()` \| null` | Session to create a cookie for |

##### Returns

| type         | description    |
| ------------ | -------------- |
| [`Cookie`]() | Session cookie |

#### Example

```ts
import { auth } from "./lucia.js";

const sessionCookie = auth.createSessionCookie(session);
const response = new Response(null, {
	headers: {
		"Set-Cookie": sessionCookie.serialize()
	}
});
```

## `createUser()`

Creates a new user, with an option to create a key alongside the user.

```ts
const createUser: (options: {
	key: {
		providerId: string;
		providerUserId: string;
		password: string | null;
	} | null;
	attributes: Lucia.UserAttributes;
}) => Promise<User>;
```

##### Parameters

| name                          | type                            | description                                            |
| ----------------------------- | ------------------------------- | ------------------------------------------------------ |
| `options.key`                 | `null` \| `Record<string, any>` | If defined, the key will be created alongside the user |
| `options.key?.providerId`     | `string`                        | Key provider id                                        |
| `options.key?.providerUserId` | `string`                        | Key provider user id                                   |
| `options.key?.password`       | `string`                        | Key password                                           |
| `options.attributes`          | [`Lucia.UserAttributes`]()      | Database user attributes                               |

###### Returns

| type       | description |
| ---------- | ----------- |
| [`User`]() | A new user  |

##### Errors

| name                    |
| ----------------------- |
| `AUTH_DUPLICATE_KEY_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

const user = await auth.createUser({
	key: {
		providerId: "email",
		providerUserId: "user@example.com",
		password: "123456"
	},
	attributes: {
		username: "user123",
		admin: true
	}
});
```

## `deleteDeadUserSessions()`

Deletes all sessions that are expired and their idle period has passed (dead sessions). Will succeed regardless of the validity of the user id.

```ts
const deleteDeadUserSessions: (userId: string) => Promise<void>;
```

##### Parameters

| name     | type     | description |
| -------- | -------- | ----------- |
| `userId` | `string` | User id     |

#### Example

```ts
import { auth } from "./lucia.js";

await auth.deleteExpiredUserSession(userId);
```

## `deleteKey()`

Deletes a key. Will succeed regardless of the validity of the key id.

```ts
const deleteKey: (providerId: string, providerUserId: string) => Promise<void>;
```

##### Parameters

| name             | type     | description                     |
| ---------------- | -------- | ------------------------------- |
| `providerId`     | `string` | The provider id of the key      |
| `providerUserId` | `string` | The provider user id of the key |

#### Example

```ts
import { auth } from "./lucia.js";

await auth.deleteKey("username", "user@example.com");
```

## `deleteUser()`

Deletes a user. Will succeed regardless of the validity of the user id.

```ts
const deleteUser: (userId: string) => Promise<void>;
```

##### Parameters

| name     | type     | description         |
| -------- | -------- | ------------------- |
| `userId` | `string` | A user id to delete |

#### Example

```ts
import { auth } from "./lucia.js";

await auth.deleteUser(userId);
```

## `getAllUserKeys()`

Validates the user id and returns all keys of a user.

```ts
const getAllUserKeys: (userId: string) => Promise<Key[]>;
```

##### Parameters

| name     | type     | description   |
| -------- | -------- | ------------- |
| `userId` | `string` | The A user id |

##### Returns

| type          |
| ------------- |
| [`Key`]()`[]` |

##### Errors

| name                   |
| ---------------------- |
| `AUTH_INVALID_USER_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

const keys = await auth.getAllUserKeys(userId);
```

## `getAllUserSessions()`

Validate the user id and get all valid sessions of a user. Includes active and idle sessions, but not dead sessions.

```ts
const getAllUserKeys: (userId: string) => Promise<Session[]>;
```

##### Parameters

| name     | type     | description   |
| -------- | -------- | ------------- |
| `userId` | `string` | The A user id |

##### Returns

| type              |
| ----------------- |
| [`Session`]()`[]` |

##### Errors

| name                   |
| ---------------------- |
| `AUTH_INVALID_USER_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

try {
	const sessions = await auth.getAllUserSessions(userId);
} catch {
	// invalid user id
}
```

## `getKey()`

Gets a key. Use [`useKey()]() method for validating key passwords.

```ts
const getKey: (providerId: string, providerUserId: string) => Promise<Key>;
```

##### Parameters

| name             | type     | description                     |
| ---------------- | -------- | ------------------------------- |
| `providerId`     | `string` | The provider id of the key      |
| `providerUserId` | `string` | The provider user id of the key |

##### Returns

| type                                     |
| ---------------------------------------- |
| [`Key`](/reference/lucia-auth/types#key) |

##### Errors

| name                  |
| --------------------- |
| `AUTH_INVALID_KEY_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

const key = await auth.getKey("email", "user@example.com");
```

## `getSession()`

Gets a session. Returns both active and idle sessions.

```ts
const getSessionUser: (sessionId: string) => Promise<Session>;
```

##### Parameters

| name        | type     | description                  |
| ----------- | -------- | ---------------------------- |
| `sessionId` | `string` | An active or idle session id |

##### Returns

| type                                             |
| ------------------------------------------------ |
| [`Session`](/reference/lucia-auth/types#session) |

##### Errors

| name                      |
| ------------------------- |
| `AUTH_INVALID_SESSION_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

const session = await auth.getSession(sessionId);
if (session.state === "active") {
	// valid session
}
if (session.state === "idle") {
	// should be renewed
}
```

## `getUser()`

Gets a user.

```ts
const getUser: (userId: string) => Promise<User>;
```

##### Parameters

| name     | type     | description |
| -------- | -------- | ----------- |
| `userId` | `string` | A user id   |

##### Returns

| type       |
| ---------- |
| [`User`]() |

##### Errors

| name                   |
| ---------------------- |
| `AUTH_INVALID_USER_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

const user = await auth.getUser(userId);
```

## `handleRequest()`

Creates a new [`AuthRequest`]() instance.

```ts
const handleRequest: (...args: any[]) => AuthRequest;
```

##### Parameters

| type    | description                             |
| ------- | --------------------------------------- |
| `any[]` | Refer to the middleware's documentation |

##### Returns

| type                                               |
| -------------------------------------------------- |
| [`AuthRequest`](/reference/lucia-auth/authrequest) |

## `invalidateAllUserSessions()`

Invalidates all sessions of a user. Will succeed regardless of the validity of the user id.

```ts
const invalidateAllUserSessions: (userId: string) => Promise<void>;
```

##### Parameters

| name     | type     | description |
| -------- | -------- | ----------- |
| `userId` | `string` | A user id   |

#### Example

```ts
import { auth } from "./lucia.js";

await auth.invalidateAllUserSession(userId);
```

## `invalidateSession()`

Invalidates a session. Will succeed regardless of the validity of the session id.

```ts
const invalidateSession: (sessionId: string) => Promise<void>;
```

##### Parameters

| name        | type     | description  |
| ----------- | -------- | ------------ |
| `sessionId` | `string` | A session id |

#### Example

```ts
import { auth } from "./lucia.js";

await auth.invalidateSession(sessionId);
```

## `readBearerToken()`

Takes a `Authorization` request header and returns the bearer token if it exists. This _does not_ validate the session. Bearer token must be stored in the following format:

```http
Authorization: Bearer <session_id>
```

```ts
const readBearerToken: (
	authorizationHeader: string | null | undefined
) => string | null;
```

##### Parameters

| name                  | type                          | description            |
| --------------------- | ----------------------------- | ---------------------- |
| `authorizationHeader` | `string \| null \| undefined` | `Authorization` header |

##### Returns

| type     | description                 |
| -------- | --------------------------- |
| `string` | Bearer token value          |
| `null`   | Bearer token does not exist |
#### Example

```ts
import { auth } from "./lucia.js";

const sessionId = auth.readBearerToken("Bearer CAbc9LAUY3Q18f0s92Jo817dna8eDtmRrUrDuVFM")
```

## `readSessionCookie()`

Takes a `Cookie` request header and returns the session cookie value if it exists. This _does not_ validate the session.

```ts
const readSessionCookie: (
	cookieHeader: string | null | undefined
) => string | null;
```

##### Parameters

| name           | type                          | description     |
| -------------- | ----------------------------- | --------------- |
| `cookieHeader` | `string \| null \| undefined` | `Cookie` header |

##### Returns

| type     | description                   |
| -------- | ----------------------------- |
| `string` | Session cookie value          |
| `null`   | Session cookie does not exist |

#### Example

```ts
import { auth } from "./lucia.js";

const sessionId = auth.readSessionCookie("auth_session=CAbc9LAUY3Q18f0s92Jo817dna8eDtmRrUrDuVFM")
```

## `renewSession()`

Validates an active or idle session id, and renews the session if valid. The session provided is invalidated.

```ts
const renewSession: (sessionId: string) => Promise<Session>;
```

##### Parameters

| name        | type     | description                  |
| ----------- | -------- | ---------------------------- |
| `sessionId` | `string` | An active or idle session id |

##### Returns

| type          | description   |
| ------------- | ------------- |
| [`Session`]() | A new session |

##### Errors

| name                      |
| ------------------------- |
| `AUTH_INVALID_SESSION_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

const renewedSession = await auth.renewSession(session.sessionId);
```

## `updateKeyPassword()`

Updates the password of a key. Pass `null` to parameter `password` to remove the key's password.

```ts
const updateKeyPassword: (
	providerId: string,
	providerUserId: string,
	password: string | null
) => Promise<void>;
```

##### Parameters

| name             | type             | description                            |
| ---------------- | ---------------- | -------------------------------------- |
| `providerId`     | `string`         | The provider id of the target key      |
| `providerUserId` | `string`         | The provider user id of the target key |
| `password`       | `string \| null` | A new password                         |

##### Errors

| name                  |
| --------------------- |
| `AUTH_INVALID_KEY_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

await auth.updateKeyPassword("email", "user@example.com", "123456");
await auth.updateKeyPassword("email", "user@example.com", null); // remove password
```

## `updateSessionAttributes()`

Updates one or more fields of a session. Values of parameter `attributes` can be `null` but not `undefined`.

```ts
const updateSessionAttributes: (
	sessionId: string,
	attributes: Partial<Lucia.DatabaseSessionAttributes>
) => Promise<Session>;
```

##### Parameters

| name         | type                                               | description                |
| ------------ | -------------------------------------------------- | -------------------------- |
| `sessionId`  | `string`                                           | A session id               |
| `attributes` | `Partial<`[`Lucia.DatabaseSessionAttributes`]()`>` | `session` fields to update |

##### Returns

| type          | description         |
| ------------- | ------------------- |
| [`Session`]() | The updated session |

##### Errors

| name                      |
| ------------------------- |
| `AUTH_INVALID_SESSION_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

await auth.updateUserAttributes(userId, {
	username: "user123",
	profile_image: null
});
```

## `updateUserAttributes()`

Updates one or more database fields of a user. Values of parameter `attributes` can be `null` but not `undefined`.

```ts
const updateUserAttributes: (
	userId: string,
	attributes: Partial<Lucia.DatabaseUserAttributes>
) => Promise<User>;
```

##### Parameters

| name         | type                                            | description             |
| ------------ | ----------------------------------------------- | ----------------------- |
| `userId`     | `string`                                        | A user id               |
| `attributes` | `Partial<`[`Lucia.DatabaseUserAttributes`]()`>` | `user` fields to update |

##### Returns

| type       | description      |
| ---------- | ---------------- |
| [`User`]() | The updated user |

##### Errors

| name                   |
| ---------------------- |
| `AUTH_INVALID_USER_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

await auth.updateUserAttributes(userId, {
	username: "user123",
	profile_image: null
});
```

## `useKey()`

Validates a key, including the password. `null` must be passed to parameter `password` if the password does not hold a password.

```ts
const useKey: (
	providerId: string,
	providerUserId: string,
	password: string | null
) => Promise<Key>;
```

##### Parameters

| name             | type             | description                     |
| ---------------- | ---------------- | ------------------------------- |
| `providerId`     | `string`         | The provider id of the key      |
| `providerUserId` | `string`         | The provider user id of the key |
| `password`       | `string \| null` | The password of the key         |

##### Returns

| type      | description       |
| --------- | ----------------- |
| [`Key`]() | The validated key |

##### Errors

| name                     |
| ------------------------ |
| `AUTH_INVALID_KEY_ID`    |
| `AUTH_INVALID_PASSWORD`  |
| `AUTH_OUTDATED_PASSWORD` |

#### Example

```ts
import { auth } from "./lucia.js";

const key = await auth.useKey("email", "user@example.com", "123456");
const key = await auth.useKey("github", githubUserId, null);
```

## `validateRequestOrigin()`

Used for CSRF protection. Checks if the request origin is trusted for non-GET and non-HEAD requests (e.g. POST, PUT, DELETE), and throws an error if the origin is invalid. Trusted origins include the request url and those defined in [`allowedRequestOrigins`]() configuration.

```ts
const validateRequestOrigin: (request: {
	url: string | URL;
	method: string;
	originHeader: string | null;
}) => void;
```

##### Parameters

| name             | type             | description                           |
| ---------------- | ---------------- | ------------------------------------- |
| `request.url`    | `string`         | The request url                       |
| `request.method` | `string`         | The request method (case insensitive) |
| `request.origin` | `string \| null` | The request origin header             |

##### Errors

| name                   |
| ---------------------- |
| `AUTH_INVALID_REQUEST` |

#### Example

```ts
import { auth } from "./lucia.js";

auth.validateRequestOrigin({
    url: "http://localhost:3000/api",
    method: "POST",
    originHeader: "http://localhost:3000"
})
```

## `validateSession()`

Validates a session, renewing it if it's idle. As such, the returned session may be different from the one provided.

```ts
const validateSession: (sessionId: string) => Promise<Session>;
```

##### Parameters

| name        | type     | description                  |
| ----------- | -------- | ---------------------------- |
| `sessionId` | `string` | An active or idle session id |

##### Returns

| type                                             | description                      |
| ------------------------------------------------ | -------------------------------- |
| [`Session`](/reference/lucia-auth/types#session) | The validated or renewed session |

##### Errors

| name                      |
| ------------------------- |
| `AUTH_INVALID_SESSION_ID` |

#### Example

```ts
import { auth } from "./lucia.js";

const session = await auth.validateSession(sessionId);
if (session.fresh) {
	// session was renewed
	// should set the new session cookie
}
```