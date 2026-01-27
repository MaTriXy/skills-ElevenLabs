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

## Text-to-Speech Example

**Python:**
```python
from elevenlabs import ElevenLabs

client = ElevenLabs()  # Uses ELEVENLABS_API_KEY env var

audio = client.text_to_speech.convert(
    text="Hello world",
    voice_id="JBFqnCBsd6RMkjVDRZzb",
    model_id="eleven_multilingual_v2"
)
```

**JavaScript:**
```javascript
import { ElevenLabsClient } from "@elevenlabs/elevenlabs-js";

const client = new ElevenLabsClient();  // Uses ELEVENLABS_API_KEY env var

const audio = await client.textToSpeech.convert("JBFqnCBsd6RMkjVDRZzb", {
  text: "Hello world",
  modelId: "eleven_multilingual_v2",
});
```
