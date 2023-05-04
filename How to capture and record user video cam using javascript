
## Problem
We need to capture and record user web cam for multiple purpose Download video as well.

## Environment
- IDE Version: Visual Studio Code

## How you fix it
To solve the problem, I am using `MDN Media streaming api functions` and plain javascript. No need to install any thing.

## Solution
```html
<!DOCTYPE html>

<html lang="en">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <title>Video Recording</title>
        <meta name="description" content="">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="stylesheet" href="">
    </head>
    <body>
        <!--[if lt IE 7]>
            <p class="browsehappy">You are using an <strong>outdated</strong> browser. Please <a href="#">upgrade your browser</a> to improve your experience.</p>
        <![endif]-->
        <div class="left">
            <div id="startButton" class="button">Start Recording</div>
            <h2>Preview</h2>
            <video id="preview" width="160" height="120" autoplay muted></video>
        </div>
        <div class="right">
            <div id="stopButton" class="button">Stop Recording</div>
            <h2>Recording</h2>
            <video id="recording" width="160" height="120" controls></video>
            <a id="downloadButton" class="button"> Download </a>
        </div>
        <div id="log"></div>  
    </body>
</html>
```

```javascript
    
    let preview = document.getElementById("preview");
    let recording = document.getElementById("recording");
    let startButton = document.getElementById("startButton");
    let stopButton = document.getElementById("stopButton");
    let downloadButton = document.getElementById("downloadButton");
    let logElement = document.getElementById("log");

    let recordingTimeMS = 5000;
    function log(msg) {
        logElement.innerHTML += `${msg}\n`;
    }

    function wait(delayInMS) {
        return new Promise((resolve) => setTimeout(resolve, delayInMS));
    }

    function startRecording(stream, lengthInMS) {
        let recorder = new MediaRecorder(stream);
        let data = [];

        recorder.ondataavailable = (event) => data.push(event.data);
        recorder.start();
        log(`${recorder.state} for ${lengthInMS / 1000} seconds…`);

        let stopped = new Promise((resolve, reject) => {
            recorder.onstop = resolve;
            recorder.onerror = (event) => reject(event.name);
        });

        let recorded = wait(lengthInMS).then(() => {
            if (recorder.state === "recording") {
            recorder.stop();
            }
        });

        return Promise.all([stopped, recorded]).then(() => data);
    }

    function stop(stream) {
        stream.getTracks().forEach((track) => track.stop());
    }

    startButton.addEventListener("click",() => {
        navigator.mediaDevices
        .getUserMedia({
            video: true,
            audio: true,
        })
        .then((stream) => {
            preview.srcObject = stream;
            downloadButton.href = stream;
            preview.captureStream =
            preview.captureStream || preview.mozCaptureStream;
            return new Promise((resolve) => (preview.onplaying = resolve));
        })
        .then(() => startRecording(preview.captureStream(), recordingTimeMS))
        .then((recordedChunks) => {
            let recordedBlob = new Blob(recordedChunks, { type: "video/webm" });
            recording.src = URL.createObjectURL(recordedBlob);
            downloadButton.href = recording.src;
            downloadButton.download = "RecordedVideo.webm";

            log(
            `Successfully recorded ${recordedBlob.size} bytes of ${recordedBlob.type} media.`
            );
        })
        .catch((error) => {
            if (error.name === "NotFoundError") {
            log("Camera or microphone not found. Can't record.");
            } else {
            log(error);
            }
        });
    },false);

    stopButton.addEventListener("click",() => {
        stop(preview.srcObject);
    },false);

```

## Usage

Just tapping the start and stop button you can capture and record the cam.

