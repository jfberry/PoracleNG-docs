# Humans API

The humans API manages registered users (referred to as "humans" in the API).

## Endpoints

### GET /api/humans/{id}

Get a specific user's data.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/humans/123456789
```

### GET /api/humans

List all registered users.

### POST /api/humans

Register a new user.

### PATCH /api/humans/{id}

Update a user's settings (location, areas, language, etc.).

### DELETE /api/humans/{id}

Remove a user registration.

## Tracking Endpoints

### GET /api/tracking/{id}

Get all tracking subscriptions for a user.

```sh
curl -H "X-Poracle-Secret: your-secret" http://localhost:3030/api/tracking/123456789
```

### POST /api/tracking/{id}

Add tracking subscriptions for a user.

### DELETE /api/tracking/{id}/{type}/{uid}

Remove a specific tracking subscription.

## Profile Endpoints

### GET /api/profiles/{id}

List profiles for a user.

### POST /api/profiles/{id}

Create or update a profile.

### DELETE /api/profiles/{id}/{profile_no}

Delete a profile.

## Direct Messaging

### POST /api/postMessage

Send a direct message to a user or channel:

```sh
curl -X POST -H "X-Poracle-Secret: your-secret" \
  -H "Content-Type: application/json" \
  -d '{"target": "123456789", "type": "discord:user", "message": "Hello!"}' \
  http://localhost:3030/api/postMessage
```
