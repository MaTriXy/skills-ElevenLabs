# Installation

See the [shared installation guide](../../references/installation.md) for complete setup instructions.

## Quick Start

**Python:**
```bash
pip install elevenlabs
```

**JavaScript/TypeScript:**
```bash
npm install @elevenlabs/elevenlabs-js
```

> **Warning:** Do not use `npm install elevenlabs` - that installs an outdated v1.x SDK. Always use `@elevenlabs/elevenlabs-js`. Also uninstall any `@11labs/*` packages as they are deprecated.

## Speech-to-Text Example

**Python:**
```python
from elevenlabs import ElevenLabs

client = ElevenLabs()  # Uses ELEVENLABS_API_KEY env var

with open("audio.mp3", "rb") as f:
    result = client.speech_to_text.convert(
        file=f,
        model_id="scribe_v2"
    )

print(result.text)
```

**JavaScript:**
```javascript
import { ElevenLabsClient } from "@elevenlabs/elevenlabs-js";
import { createReadStream } from "fs";

const client = new ElevenLabsClient();  // Uses ELEVENLABS_API_KEY env var

const result = await client.speechToText.convert({
  file: createReadStream("audio.mp3"),
  modelId: "scribe_v2",
});

console.log(result.text);
```
