# Python SDK Reference

Use the async ElevenLabs SDK for Speech Engine servers.

```python
from elevenlabs import AsyncElevenLabs

elevenlabs = AsyncElevenLabs()
```

## Resource Methods

### Create

Only `speech_engine.ws_url` is required. Add optional config blocks when the Speech Engine needs custom voice, transcription, turn-taking, headers, or privacy behavior.

```python
engine = await elevenlabs.speech_engine.create(
    name="My Speech Engine",
    speech_engine={
        "ws_url": "https://example.com/ws",
        "request_headers": {
            "x-agent-runtime": "openclaw",
        },
    },
    tts={
        "model_id": "eleven_flash_v2_5",
        "voice_id": "cjVigY5qzO86Huf0OWal",
        "optimize_streaming_latency": "2",
    },
    asr={
        "provider": "scribe_realtime",
        "keywords": ["OpenClaw", "Acme Cloud"],
    },
    turn={
        "turn_eagerness": "normal",
        "speculative_turn": True,
    },
    privacy={
        "record_voice": False,
    },
)

print(engine.engine_id)
```

### Get

```python
engine = await elevenlabs.speech_engine.get("seng_...")
```

The returned `SpeechEngineResource` has `engine_id` plus methods for serving, request verification, and manual session creation.

### serve

Start a standalone WebSocket server and block until stopped:

```python
await engine.serve(
    port=3001,
    path="/ws",
    debug=True,
    on_transcript=handle_transcript,
)
```

Key parameters:

| Parameter | Default | Purpose |
| --- | --- | --- |
| `port` | `3001` | Port to listen on |
| `path` | `None` | Restrict WebSocket connections to one path |
| `debug` | `False` | Log protocol details to stdout |
| `on_init` | | Session initialized callback |
| `on_transcript` | | User transcript callback |
| `on_close` | | Clean disconnect callback |
| `on_disconnect` | | Unexpected drop callback |
| `on_error` | | Error callback |

### verify_request

Use only when managing WebSocket upgrades manually:

```python
is_valid = engine.verify_request(headers)
```

It checks `X-Elevenlabs-Speech-Engine-Authorization` against a JWT signed with the SHA-256 hash of the ElevenLabs API key.

### create_session

Wrap an accepted WebSocket for custom integrations such as FastAPI or Starlette:

```python
session = engine.create_session(websocket, debug=True)
session.on("user_transcript", handle_transcript)
await session.run()
```

## Session API

Each `SpeechEngineSession` represents one conversation.

| Member | Purpose |
| --- | --- |
| `conversation_id` | Assigned after `init` |
| `is_open` | Whether the WebSocket is open |
| `on(event, handler)` | Register an event handler |
| `off(event, handler)` | Remove an event handler |
| `once(event, handler)` | Register a one-time handler |
| `send_response(response)` | Send LLM text or stream back for TTS |
| `run()` | Run the receive loop for manual sessions |
| `close()` | Close the WebSocket |

`send_response()` must be called from an `on_transcript` flow. It accepts a string or async iterable and can extract text from OpenAI Responses, OpenAI Chat Completions, Anthropic Messages, and Google Gemini stream events.

When a new transcript arrives, the SDK cancels the previous transcript handler so interrupted responses stop naturally.

## Callbacks

Handlers can be synchronous functions or async coroutine functions.

| Callback | Signature | Purpose |
| --- | --- | --- |
| `on_init` | `(conversation_id, session) -> None` | Session initialized |
| `on_transcript` | `(transcript, session) -> None` | User speech transcribed |
| `on_close` | `(session) -> None` | Clean disconnect |
| `on_disconnect` | `(session) -> None` | Unexpected drop |
| `on_error` | `(error, session) -> None` | Protocol or WebSocket error |

## Events

When using `session.on()` directly:

| Event | Handler |
| --- | --- |
| `user_transcript` | `(transcript) -> None` |
| `init` | `(conversation_id) -> None` |
| `close` | `() -> None` |
| `disconnected` | `() -> None` |
| `error` | `(error) -> None` |

Event constants are available from `elevenlabs.speech_engine`.

Transcript messages have:

| Property | Type | Notes |
| --- | --- | --- |
| `role` | `"user"` or `"agent"` | Convert `"agent"` to `"assistant"` for OpenAI-style APIs |
| `content` | `str` | Message text |

## Wire Protocol

The SDK handles protocol details automatically, but manual integrations should know these message types:

Incoming from ElevenLabs: `init`, `user_transcript`, `ping`, `close`, `error`.

Outgoing from your server: `agent_response` chunks and `pong`.
