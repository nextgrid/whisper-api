# Whisper
Whisper is a general-purpose speech recognition model. It is trained on a large dataset of diverse audio and is also a multi-task model that can perform multilingual speech recognition as well as speech translation and language identification. For more details: [github.com/openai/whisper](https://github.com/openai/whisper/)

## Run (Docker Hub)
For CPU: https://hub.docker.com/r/onerahmet/openai-whisper-asr-webservice
```sh
docker run -d -p 9000:9000 -e ASR_MODEL=base onerahmet/openai-whisper-asr-webservice
```

For GPU: https://hub.docker.com/r/onerahmet/openai-whisper-asr-webservice-gpu
```sh
docker run -d --gpus all -p 9000:9000 -e ASR_MODEL=base onerahmet/openai-whisper-asr-webservice-gpu
```

You can access the Swagger via `http://localhost:9000/docs`.

Available ASR_MODELs are `tiny`, `base`, `small`, `medium` and `large`

For English-only applications, the `.en` models tend to perform better, especially for the `tiny.en` and `base.en` models. We observed that the difference becomes less significant for the `small.en` and `medium.en` models.



## Docker Build
### For CPU
```sh
# Build Image
docker build -t whisper .

# Run Container
docker run -p 8000:8000 whisper
# or
docker run -p 8000:8000 -e ASR_MODEL=base whisper
```

### For GPU
```sh
# Build Image
docker build -f Dockerfile.gpu -t whisper-gpu .

# Run Container
docker run -gpus all -p 8000:8000 whisper-gpu
# or
docker run --gpus all -p 8000:8000 -e ASR_MODEL=base whisper-gpu

```
## Automatic Speech recognition service /asr

If you choose the **transcribe** task, transcribes the uploaded file. Both audio and video files are supported (as long as ffmpeg supports it).

You can provide the language or it will be automatically recognized. 

If you choose the **translate** task it will provide an English transcript no matter which language was spoken.

Returns a json with following fields:
- **text**: Contains the full transcript
- **segments**: Contains an entry per segment. Each entry  provides time stamps, transcript, token ids and other metadata
- **language**: Detected or provided language (as a language code)

## Subtitle generating services /get-srt and /get-vtt

These two POST endpoints have the same interface as /asr but they return a subtitle file (either srt or vtt).

Note that you can also upload video formats directly as long as they are supported by ffmpeg.

## Language detection service /detect-language

Detects the language spoken in the uploaded file. For longer files it only processes first 30 seconds.

Returns a json with following fields:
- **detected_language**
- **langauge_code**
