# Voice Settings

Fine-tune voice characteristics for your use case.

## Parameters

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| `stability` | 0.0 - 1.0 | 0.5 | Higher = more consistent, Lower = more expressive/variable |
| `similarity_boost` | 0.0 - 1.0 | 0.75 | Higher = closer to original voice, may amplify artifacts |
| `style` | 0.0 - 1.0 | 0.0 | Style exaggeration (multilingual v2 models only) |
| `use_speaker_boost` | boolean | true | Enhances voice clarity and similarity |

## Python Example

```python
from elevenlabs import ElevenLabs, VoiceSettings

client = ElevenLabs()

audio = client.text_to_speech.convert(
    text="Testing different voice settings.",
    voice_id="JBFqnCBsd6RMkjVDRZzb",
    model_id="eleven_multilingual_v2",
    voice_settings=VoiceSettings(
        stability=0.5,
        similarity_boost=0.75,
        style=0.0,
        use_speaker_boost=True
    )
)
```

## JavaScript Example

```javascript
const audio = await client.textToSpeech.convert("JBFqnCBsd6RMkjVDRZzb", {
  text: "Testing different voice settings.",
  model_id: "eleven_multilingual_v2",
  voice_settings: {
    stability: 0.5,
    similarity_boost: 0.75,
    style: 0.0,
    use_speaker_boost: true,
  },
});
```

## cURL Example

```bash
curl -X POST "https://api.elevenlabs.io/v1/text-to-speech/JBFqnCBsd6RMkjVDRZzb" \
  -H "xi-api-key: $ELEVEN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Testing different voice settings.",
    "model_id": "eleven_multilingual_v2",
    "voice_settings": {
      "stability": 0.5,
      "similarity_boost": 0.75,
      "style": 0.0,
      "use_speaker_boost": true
    }
  }' \
  --output output.mp3
```

## Use Case Recommendations

### Audiobooks / Narration
```python
voice_settings=VoiceSettings(
    stability=0.7,        # Consistent tone
    similarity_boost=0.5, # Natural variation
    style=0.0
)
```

### Conversational / Chatbots
```python
voice_settings=VoiceSettings(
    stability=0.4,        # More expressive
    similarity_boost=0.75,
    style=0.3             # Slight style emphasis
)
```

### News / Professional
```python
voice_settings=VoiceSettings(
    stability=0.8,        # Very consistent
    similarity_boost=0.6,
    style=0.0
)
```

### Character Voices / Drama
```python
voice_settings=VoiceSettings(
    stability=0.3,        # Highly expressive
    similarity_boost=0.8,
    style=0.5             # Strong style
)
```

## Tips

- **Start with defaults** and adjust incrementally
- **Lower stability** if voice sounds monotonous
- **Reduce similarity_boost** if you hear audio artifacts
- **Style only works** with multilingual v2 models
- **Test with representative text** from your actual use case
