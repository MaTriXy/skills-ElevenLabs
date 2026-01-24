---
name: speech-to-text
description: Transcribe audio files to text using ElevenLabs' speech recognition. Use this skill when converting audio recordings to text, generating subtitles, or processing spoken content.
license: MIT
metadata:
  author: elevenlabs
  version: "1.0"
---

# ElevenLabs Speech-to-Text

Transcribe audio to text with high accuracy and timestamp support.

## Quick Start

### Python

```python
from elevenlabs import ElevenLabs

client = ElevenLabs()

with open("audio.mp3", "rb") as audio_file:
    result = client.speech_to_text.convert(
        file=audio_file,
        model_id="scribe_v1"
    )

print(result.text)
```

### JavaScript

```javascript
import { ElevenLabsClient } from "@elevenlabs/elevenlabs-js";
import { createReadStream } from "fs";

const client = new ElevenLabsClient();

const result = await client.speechToText.convert({
  file: createReadStream("audio.mp3"),
  model_id: "scribe_v1",
});

console.log(result.text);
```

### cURL

```bash
curl -X POST "https://api.elevenlabs.io/v1/speech-to-text" \
  -H "xi-api-key: $ELEVEN_API_KEY" \
  -F "file=@audio.mp3" \
  -F "model_id=scribe_v1"
```

## Models

| Model ID | Description |
|----------|-------------|
| `scribe_v1` | High-accuracy transcription model |

## Transcription with Timestamps

Get word-level timestamps for subtitle generation:

### Python

```python
result = client.speech_to_text.convert(
    file=audio_file,
    model_id="scribe_v1",
    timestamps_granularity="word"
)

# Access word-level timestamps
for word in result.words:
    print(f"{word.text}: {word.start}s - {word.end}s")
```

### JavaScript

```javascript
const result = await client.speechToText.convert({
  file: createReadStream("audio.mp3"),
  model_id: "scribe_v1",
  timestamps_granularity: "word",
});

for (const word of result.words) {
  console.log(`${word.text}: ${word.start}s - ${word.end}s`);
}
```

## Language Detection

The model automatically detects the spoken language. You can also specify a language hint:

```python
result = client.speech_to_text.convert(
    file=audio_file,
    model_id="scribe_v1",
    language_code="en"  # Optional hint
)

print(f"Detected language: {result.language_code}")
print(f"Transcription: {result.text}")
```

## Supported Languages

ElevenLabs supports transcription in 29+ languages including:

- English (en)
- Spanish (es)
- French (fr)
- German (de)
- Italian (it)
- Portuguese (pt)
- Dutch (nl)
- Polish (pl)
- Russian (ru)
- Japanese (ja)
- Korean (ko)
- Chinese (zh)

## Supported Audio Formats

- MP3
- WAV
- M4A
- FLAC
- OGG
- WebM

Maximum file size: 100MB

## Response Format

```json
{
  "text": "The full transcription text",
  "language_code": "en",
  "words": [
    {"text": "The", "start": 0.0, "end": 0.1},
    {"text": "full", "start": 0.12, "end": 0.3},
    ...
  ]
}
```

## Error Handling

```python
from elevenlabs import ElevenLabsError

try:
    result = client.speech_to_text.convert(
        file=audio_file,
        model_id="scribe_v1"
    )
except ElevenLabsError as e:
    print(f"Transcription failed: {e.message}")
```

Common errors:
- **400**: Unsupported audio format
- **401**: Invalid API key
- **413**: File too large (max 100MB)
- **429**: Rate limit exceeded

## References

- [Installation Guide](references/installation.md)
- [Transcription Options](references/transcription-options.md)
