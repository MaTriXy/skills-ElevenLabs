# Streaming Audio

Stream audio chunks as they're generated for lower latency.

## Python Streaming

```python
from elevenlabs import ElevenLabs

client = ElevenLabs()

# Generate streaming audio
audio_stream = client.text_to_speech.convert(
    text="This is a streaming example with lower latency.",
    voice_id="JBFqnCBsd6RMkjVDRZzb",
    model_id="eleven_turbo_v2_5"
)

# Write chunks to file
with open("output.mp3", "wb") as f:
    for chunk in audio_stream:
        f.write(chunk)
```

### Play Audio in Real-Time

```python
import subprocess

def play_stream(audio_stream):
    # Using ffplay (requires ffmpeg installed)
    process = subprocess.Popen(
        ["ffplay", "-nodisp", "-autoexit", "-"],
        stdin=subprocess.PIPE
    )
    for chunk in audio_stream:
        process.stdin.write(chunk)
    process.stdin.close()
    process.wait()

audio_stream = client.text_to_speech.convert(
    text="Playing this audio in real-time.",
    voice_id="JBFqnCBsd6RMkjVDRZzb",
    model_id="eleven_turbo_v2_5"
)
play_stream(audio_stream)
```

## JavaScript Streaming

```javascript
import { ElevenLabsClient } from "@elevenlabs/elevenlabs-js";
import { createWriteStream } from "fs";

const client = new ElevenLabsClient();

const audioStream = await client.textToSpeech.convert("JBFqnCBsd6RMkjVDRZzb", {
  text: "Streaming audio in JavaScript.",
  model_id: "eleven_turbo_v2_5",
});

// Write to file
const writeStream = createWriteStream("output.mp3");
audioStream.pipe(writeStream);

// Or process chunks
for await (const chunk of audioStream) {
  // Process each chunk
  console.log(`Received ${chunk.length} bytes`);
}
```

## WebSocket Streaming

For the lowest latency, use WebSocket streaming:

### Python WebSocket

```python
import asyncio
import websockets
import json

async def stream_tts():
    uri = "wss://api.elevenlabs.io/v1/text-to-speech/JBFqnCBsd6RMkjVDRZzb/stream-input"

    async with websockets.connect(
        uri,
        extra_headers={"xi-api-key": os.environ["ELEVEN_API_KEY"]}
    ) as ws:
        # Initialize stream
        await ws.send(json.dumps({
            "text": " ",
            "voice_settings": {"stability": 0.5, "similarity_boost": 0.75},
            "model_id": "eleven_turbo_v2_5"
        }))

        # Send text chunks
        await ws.send(json.dumps({"text": "Hello, "}))
        await ws.send(json.dumps({"text": "this is streaming. "}))

        # End stream
        await ws.send(json.dumps({"text": ""}))

        # Receive audio chunks
        async for message in ws:
            data = json.loads(message)
            if data.get("audio"):
                audio_chunk = base64.b64decode(data["audio"])
                # Process audio chunk

asyncio.run(stream_tts())
```

## Best Practices

1. **Use turbo models** for real-time applications:
   - `eleven_turbo_v2_5` provides lowest latency

2. **Buffer audio** before playback to prevent choppy output

3. **Handle disconnections** gracefully in WebSocket streams

4. **Choose appropriate output format**:
   - `pcm_24000` for lowest latency processing
   - `mp3_44100_128` for direct playback
