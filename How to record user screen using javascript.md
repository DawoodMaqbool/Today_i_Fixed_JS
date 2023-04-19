
## Problem
We need to record user screen for multiple purpose and attach video directly to input file.

## Environment
- IDE Version: Visual Studio Code

## How you fix it
To solve the problem, I am using `MDN Media recording api functions` and plain javascript. No need to install any thing.

## Solution
```html
<!DOCTYPE html>

<html>
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <title>Screen Recorder</title>
        <meta name="description" content="">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <style>
            #video {
            border: 1px solid #999;
            width: 98%;
            max-width: 860px;
            }

            .error {
            color: red;
            }

            .warn {
            color: orange;
            }

            .info {
            color: darkgreen;
            }
        </style>
    </head>
    <body>
        <p>This example shows you the contents of the selected part of your display.
            Click the Start Capture button to begin.</p>
            
        <p>
            <button id="start">Start Capture</button>
            <button id="stop">Stop Capture</button>
        </p>
        
        <video id="video" autoplay></video>
        <br>
        
        <strong>Log:</strong>
        <br>
        <pre id="log"></pre>

        <input type="file" id="file">

    </body>
</html>
```

```javascript
    let start = document.getElementById('start'),
    stop  = document.getElementById('stop'),
    mediaRecorder,
    videoElem = document.getElementById("video"),
    fileElem = document.getElementById("file"),
    mimeType = 'video/webm',
    logElem = document.getElementById("log");

    console.log = msg => logElem.innerHTML += `${msg}<br>`;
    console.error = msg => logElem.innerHTML += `<span class="error">${msg}</span><br>`;
    console.warn = msg => logElem.innerHTML += `<span class="warn">${msg}<span><br>`;
    console.info = msg => logElem.innerHTML += `<span class="info">${msg}</span><br>`;
    
    async function startCapture() {
      logElem.innerHTML = "";
    
      try {
        let displayMediaOptions = {
            audio: false, 
            video: { mediaSource: "screen", cursor: "always"}
        };

        const controller = new CaptureController(displayMediaOptions);

        // Prompt the user to share a tab, window, or screen.
        videoElem.srcObject  = await navigator.mediaDevices.getDisplayMedia({ controller });

        // Query the displaySurface value of the captured video track
        const [track] = videoElem.srcObject .getVideoTracks();
        const displaySurface = track.getSettings().displaySurface;

        if (displaySurface == "browser") {
            // Focus the captured tab.
            controller.setFocusBehavior("focus-captured-surface");
        } else if (displaySurface == "window") {
            // Do not move focus to the captured window.
            // Keep the capturing page focused.
            controller.setFocusBehavior("no-focus-change");
        }
        dumpOptionsInfo();
      } catch(err) {
        console.error("Error: " + err);
      }
    }
    
    function stopCapture(evt) {
      let tracks = videoElem.srcObject.getTracks();
    
      tracks.forEach(track => track.stop());
      videoElem.srcObject = null;
    }
    
    function dumpOptionsInfo() {
      const videoTrack = videoElem.srcObject.getVideoTracks()[0];
     
      console.info("Track settings:");
      console.info(JSON.stringify(videoTrack.getSettings(), null, 2));
      console.info("Track constraints:");
      console.info(JSON.stringify(videoTrack.getConstraints(), null, 2));
    }

    function createRecorder (stream, mimeType) {
        // the stream data is stored in this array
        let recordedChunks = []; 

        const mediaRecorder = new MediaRecorder(stream);

        mediaRecorder.ondataavailable = function (e) {
            if (e.data.size > 0) {
            recordedChunks.push(e.data);
            }  
        };
        mediaRecorder.onstop = function () {
            saveFile(recordedChunks);
            recordedChunks = [];
        };
        mediaRecorder.start(200); // For every 200ms the stream data will be stored in a separate chunk.
        return mediaRecorder;
    }

    function saveFile(recordedChunks){

        const blob = new Blob(recordedChunks, {
            type: mimeType
            });
        let filename = window.prompt('Enter file name'),
        downloadLink = document.createElement('a');
        downloadLink.href = URL.createObjectURL(blob);
        downloadLink.download = `${filename}.webm`;
        attachVideoInInput(blob,filename);

        document.body.appendChild(downloadLink);
        downloadLink.click();
        URL.revokeObjectURL(blob); // clear from memory
        document.body.removeChild(downloadLink);
    }

    function attachVideoInInput(blob,filename){
        var file = new File([blob],  `${filename}.webm`); // create new file
        // Need to use a data transfer object to get a new FileList object
        var datTran = new ClipboardEvent('').clipboardData || new DataTransfer(); 
        datTran.items.add(file);  // Add the file to the DT object
        fileElem.files = datTran.files;
    }

    start.addEventListener('click', async function(){
        await startCapture();
        mediaRecorder = createRecorder(videoElem.srcObject, mimeType);
    });

    stop.addEventListener('click', function(){
        mediaRecorder.stop();
        stopCapture();
    });
```

## Usage

Just tapping the start and stop button you can record the screen.

