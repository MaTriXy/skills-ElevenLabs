---
name: text-to-speech
description: Convert text to lifelike speech using ElevenLabs' AI voice synthesis. Use this skill when generating audio from text, creating voiceovers, or building voice-enabled applications.
license: MIT
metadata:
  author: elevenlabs
  version: "1.0"
---

# ElevenLabs Text-to-Speech

Generate natural-sounding speech from text using ElevenLabs' voice AI.

## Quick Start

### Python

```python
from elevenlabs import ElevenLabs

client = ElevenLabs()

audio = client.text_to_speech.convert(
    text="Hello, welcome to ElevenLabs!",
    voice_id="JBFqnCBsd6RMkjVDRZzb",  # George
    model_id="eleven_multilingual_v2"
)

with open("output.mp3", "wb") as f:
    for chunk in audio:
        f.write(chunk)
```

### JavaScript

```javascript
import { ElevenLabsClient } from "@elevenlabs/elevenlabs-js";
import { createWriteStream } from "fs";

const client = new ElevenLabsClient();

const audio = await client.textToSpeech.convert("JBFqnCBsd6RMkjVDRZzb", {
  text: "Hello, welcome to ElevenLabs!",
  model_id: "eleven_multilingual_v2",
});

const writeStream = createWriteStream("output.mp3");
audio.pipe(writeStream);
```

### cURL

```bash
curl -X POST "https://api.elevenlabs.io/v1/text-to-speech/JBFqnCBsd6RMkjVDRZzb" \
  -H "xi-api-key: $ELEVEN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Hello, welcome to ElevenLabs!",
    "model_id": "eleven_multilingual_v2"
  }' \
  --output output.mp3
```

## Voice IDs

Use pre-made voices or create custom voices in the ElevenLabs dashboard.

**Popular pre-made voices:**
- `JBFqnCBsd6RMkjVDRZzb` - George (male, narrative)
- `EXAVITQu4vr4xnSDxMaL` - Sarah (female, soft)
- `onwK4e9ZLuTAKqWW03F9` - Daniel (male, authoritative)
- `XB0fDUnXU5powFXDhCwa` - Charlotte (female, conversational)

List all available voices:

```python
voices = client.voices.get_all()
for voice in voices.voices:
    print(f"{voice.voice_id}: {voice.name}")
```

## Models

| Model ID | Description | Best For |
|----------|-------------|----------|
| `eleven_multilingual_v2` | Latest multilingual model | Most use cases, 29 languages |
| `eleven_turbo_v2_5` | Low-latency model | Real-time applications |
| `eleven_monolingual_v1` | English-only model | English content |

## Voice Settings

Control voice characteristics:

```python
from elevenlabs import VoiceSettings

audio = client.text_to_speech.convert(
    text="Customize my voice settings.",
    voice_id="JBFqnCBsd6RMkjVDRZzb",
    voice_settings=VoiceSettings(
        stability=0.5,        # 0-1: Lower = more expressive
        similarity_boost=0.75, # 0-1: Higher = closer to original voice
        style=0.5,            # 0-1: Style exaggeration (v2 models)
        use_speaker_boost=True
    )
)
```

## Output Formats

Specify format with `output_format` parameter:

| Format | Description |
|--------|-------------|
| `mp3_44100_128` | MP3 at 44.1kHz, 128kbps (default) |
| `mp3_22050_32` | MP3 at 22.05kHz, 32kbps |
| `pcm_16000` | PCM at 16kHz |
| `pcm_22050` | PCM at 22.05kHz |
| `pcm_24000` | PCM at 24kHz |
| `ulaw_8000` | μ-law at 8kHz (telephony) |

```python
audio = client.text_to_speech.convert(
    text="High quality audio output.",
    voice_id="JBFqnCBsd6RMkjVDRZzb",
    output_format="mp3_44100_128"
)
```

## Streaming

For real-time applications, stream audio as it's generated:

```python
audio_stream = client.text_to_speech.convert(
    text="This text will be streamed as audio.",
    voice_id="JBFqnCBsd6RMkjVDRZzb",
    model_id="eleven_turbo_v2_5"  # Low latency model
)

for chunk in audio_stream:
    # Process each chunk as it arrives
    play_audio(chunk)
```

See [references/streaming.md](references/streaming.md) for detailed streaming examples.

## Error Handling

```python
from elevenlabs import ElevenLabsError

try:
    audio = client.text_to_speech.convert(
        text="Generate speech",
        voice_id="invalid-voice-id"
    )
except ElevenLabsError as e:
    print(f"API error: {e.message}")
```

Common errors:
- **401**: Invalid API key
- **422**: Invalid parameters (check voice_id, model_id)
- **429**: Rate limit exceeded

## References

- [Installation Guide](references/installation.md)
- [Streaming Audio](references/streaming.md)
- [Voice Settings](references/voice-settings.md)
