[![Artificial Intelligence Hackathons, tutorials and Boilerplates](https://storage.googleapis.com/lablab-static-eu/images/github/lablab-banner.jpg)](https://lablab.ai)
This is a free to use hosted version of OpenAI Whisper model.   

# Whisper
### Whisper is a general-purpose speech recognition model. It is trained on a large dataset of diverse audio and is also a multi-task model that can perform multilingual speech recognition as well as speech translation and language identification. For more details: [github.com/openai/whisper](https://github.com/openai/whisper/)

---

### Table of content
1. [How to use our API](https://github.com/nextgrid/whisper-api/edit/main/README.md#how-to-deploy-your-own-api)
2. [How to deploy your own API](https://github.com/nextgrid/whisper-api/edit/main/README.md#how-to-deploy-your-own-api)


## How to use our Whisper API
Access swagger documentation at https://whisper.lablab.ai/docs and https://whisper.lablab.ai/redoc


### Python:
```
import requests
url = "https://whisper.lablab.ai/asr"
payload={}
files=[
  ('audio_file',('test1.mp3',open('/C:/Users/pc/Desktop/test1.mp3','rb'),'audio/mpeg'))
]
response = requests.request("POST", url, data=payload, files=files)
print(response.text)
```

### JS/Node:
```
var formdata = new FormData();formdata.append("audio_file", fileInput.files[0], "/C:/Users/pc/Desktop/test1.mp3");
var requestOptions = {
  method: 'POST',
  body: formdata,
  redirect: 'follow'
};
fetch("https://whisper.lablab.ai/asr", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
``` 
 
### C#:
```
var client = new RestClient("https://whisper.lablab.ai/asr");
client.Timeout = -1;
var request = new RestRequest(Method.POST);
request.AddFile("audio_file", "/C:/Users/pc/Desktop/test1.mp3");
IRestResponse response = client.Execute(request);
Console.WriteLine(response.Content);
```
 
### Dart:
```
var request = http.MultipartRequest('POST', Uri.parse('https://whisper.lablab.ai/asr'));
request.files.add(await http.MultipartFile.fromPath('audio_file', '/C:/Users/pc/Desktop/test1.mp3'));
 
http.StreamedResponse response = await request.send();
 
if (response.statusCode == 200) {
  print(await response.stream.bytesToString());
}
else {
  print(response.reasonPhrase);
}
```

## How to deploy your own API 

### Run (Docker Hub)
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


### Docker Build
#### For CPU
```sh
# Build Image
docker build -t whisper .

# Run Container
docker run -p 8000:8000 whisper
# or
docker run -p 8000:8000 -e ASR_MODEL=base whisper
# or
docker run \
  --volume /var/lib/nvidia/lib64:/usr/local/nvidia/lib64 \
  --volume /var/lib/nvidia/bin:/usr/local/nvidia/bin \
  --device /dev/nvidia0:/dev/nvidia0 \
  --device /dev/nvidia-uvm:/dev/nvidia-uvm \
  --device /dev/nvidiactl:/dev/nvidiactl \
-p 80:8000 -d whisper-gpu
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
