# JavaScript SDK Reference

Use `@elevenlabs/elevenlabs-js` for server-side Speech Engine work.

```typescript
import { ElevenLabsClient } from "@elevenlabs/elevenlabs-js";

const elevenlabs = new ElevenLabsClient();
```

## Resource Methods

### Create

```typescript
const engine = await elevenlabs.speechEngine.create({
  name: "My Speech Engine",
  speechEngine: { wsUrl: "https://example.com/ws" },
});

console.log(engine.speechEngineId);
```

### Get

```typescript
const engine = await elevenlabs.speechEngine.get("seng_...");
```

The returned `SpeechEngineResource` has `engineId` plus methods for attaching to servers, verifying requests, and creating sessions.

### Attach

Attach Speech Engine WebSocket handling to an existing Node HTTP server:

```typescript
const attachment = engine.attach(httpServer, "/ws", {
  debug: true,
  onTranscript(transcript, signal, session) {
    session.sendResponse(stream);
  },
});
```

Shortcut:

```typescript
await elevenlabs.speechEngine.attach("seng_...", httpServer, "/ws", callbacks);
```

`attach()` handles WebSocket upgrades, path routing, and request verification. Call `await attachment.close()` to stop accepting Speech Engine connections without shutting down the HTTP server.

### verifyRequest

Use only when managing the WebSocket upgrade manually:

```typescript
const isValid = await engine.verifyRequest(req);
```

It checks `X-Elevenlabs-Speech-Engine-Authorization` against a JWT signed with the SHA-256 hash of the ElevenLabs API key.

### createSession

Wrap an already accepted WebSocket:

```typescript
const session = engine.createSession(ws, { debug: true });
session.on("user_transcript", (transcript, signal) => {
  // call LLM, then session.sendResponse(...)
});
```

## Standalone Server

Use a standalone Speech Engine server when the process only handles Speech Engine connections. Use `attach()` when integrating with Express, Fastify, or an existing Node HTTP server.

Core options are:

| Option | Default | Purpose |
| --- | --- | --- |
| `port` | `3001` | Port to listen on |
| `apiKey` | `ELEVENLABS_API_KEY` | API key for connection verification |
| `engineId` | | Speech Engine ID |
| callbacks | | `onInit`, `onTranscript`, `onClose`, `onDisconnect`, `onError`, `debug` |

## Session API

Each `SpeechEngineSession` represents one conversation.

| Member | Purpose |
| --- | --- |
| `conversationId` | Assigned after `init` |
| `isOpen` | Whether the WebSocket is open |
| `on(event, handler)` | Register an event handler |
| `off(event, handler)` | Remove an event handler |
| `once(event, handler)` | Register a one-time handler |
| `sendResponse(response)` | Send LLM text or stream back for TTS |
| `close()` | Close the WebSocket |

`sendResponse()` must be called from an `onTranscript` flow. It accepts a string or async iterable and can extract text from OpenAI Responses, OpenAI Chat Completions, Anthropic Messages, and Google Gemini stream events.

## Callbacks

| Callback | Signature | Purpose |
| --- | --- | --- |
| `onInit` | `(conversationId, session) => void` | Session initialized |
| `onTranscript` | `(transcript, signal, session) => void` | User speech transcribed |
| `onClose` | `(session) => void` | Clean disconnect |
| `onDisconnect` | `(session) => void` | Unexpected drop |
| `onError` | `(error, session) => void` | Protocol or WebSocket error |
| `debug` | `boolean` | Enable debug logs |

Pass the `AbortSignal` from `onTranscript` to the LLM call when the provider supports cancellation. It fires when the user interrupts a streaming response.

## Events

When using `session.on()` directly:

| Event | Handler |
| --- | --- |
| `user_transcript` | `(transcript, signal) => void` |
| `init` | `(conversationId) => void` |
| `close` | `() => void` |
| `disconnected` | `() => void` |
| `error` | `(error) => void` |

Transcript messages have:

| Property | Type | Notes |
| --- | --- | --- |
| `role` | `"user"` or `"agent"` | Convert `"agent"` to `"assistant"` for OpenAI-style APIs |
| `content` | `string` | Message text |

## Wire Protocol

The SDK handles protocol details automatically, but manual integrations should know these message types:

Incoming from ElevenLabs: `init`, `user_transcript`, `ping`, `close`, `error`.

Outgoing from your server: `agent_response` chunks and `pong`.
