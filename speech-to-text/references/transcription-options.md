# Transcription Options

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `file` | file | Yes | Audio file to transcribe |
| `model_id` | string | Yes | Model to use (`scribe_v1`) |
| `language_code` | string | No | Language hint (ISO 639-1 code) |
| `timestamps_granularity` | string | No | `word` for word-level timestamps |

## Python Example

```python
from elevenlabs import ElevenLabs

client = ElevenLabs()

with open("audio.mp3", "rb") as audio_file:
    result = client.speech_to_text.convert(
        file=audio_file,
        model_id="scribe_v1",
        language_code="en",  # Optional language hint
        timestamps_granularity="word"
    )
```

## JavaScript Example

```javascript
import { ElevenLabsClient } from "@elevenlabs/elevenlabs-js";
import { createReadStream } from "fs";

const client = new ElevenLabsClient();

const result = await client.speechToText.convert({
  file: createReadStream("audio.mp3"),
  model_id: "scribe_v1",
  language_code: "en",
  timestamps_granularity: "word",
});
```

## cURL Example

```bash
curl -X POST "https://api.elevenlabs.io/v1/speech-to-text" \
  -H "xi-api-key: $ELEVEN_API_KEY" \
  -F "file=@audio.mp3" \
  -F "model_id=scribe_v1" \
  -F "language_code=en" \
  -F "timestamps_granularity=word"
```

## Response Structure

```json
{
  "text": "The complete transcribed text from the audio file.",
  "language_code": "en",
  "language_probability": 0.98,
  "words": [
    {
      "text": "The",
      "start": 0.0,
      "end": 0.15
    },
    {
      "text": "complete",
      "start": 0.16,
      "end": 0.45
    }
  ]
}
```

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `text` | string | Full transcription text |
| `language_code` | string | Detected language (ISO 639-1) |
| `language_probability` | float | Confidence in language detection (0-1) |
| `words` | array | Word-level timestamps (if requested) |
| `words[].text` | string | The transcribed word |
| `words[].start` | float | Start time in seconds |
| `words[].end` | float | End time in seconds |

## Supported Languages

| Code | Language |
|------|----------|
| `en` | English |
| `es` | Spanish |
| `fr` | French |
| `de` | German |
| `it` | Italian |
| `pt` | Portuguese |
| `nl` | Dutch |
| `pl` | Polish |
| `ru` | Russian |
| `ja` | Japanese |
| `ko` | Korean |
| `zh` | Chinese |
| `ar` | Arabic |
| `hi` | Hindi |
| `tr` | Turkish |
| `sv` | Swedish |
| `da` | Danish |
| `fi` | Finnish |
| `no` | Norwegian |

## Audio Format Requirements

**Supported formats:**
- MP3
- WAV
- M4A
- FLAC
- OGG
- WebM

**Limits:**
- Maximum file size: 100MB
- Recommended: Clear audio with minimal background noise

## Use Cases

### Subtitle Generation

```python
result = client.speech_to_text.convert(
    file=audio_file,
    model_id="scribe_v1",
    timestamps_granularity="word"
)

# Generate SRT format
srt_content = ""
for i, word in enumerate(result.words, 1):
    start = format_timestamp(word.start)
    end = format_timestamp(word.end)
    srt_content += f"{i}\n{start} --> {end}\n{word.text}\n\n"
```

### Meeting Transcription

```python
# Transcribe meeting recording
with open("meeting.mp3", "rb") as f:
    result = client.speech_to_text.convert(
        file=f,
        model_id="scribe_v1"
    )

# Save transcript
with open("meeting_transcript.txt", "w") as f:
    f.write(result.text)
```
