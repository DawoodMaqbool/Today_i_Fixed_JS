
## Problem
We need to capture user screen for multiple purpose.

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
        <title>Video Experiment</title>
        <meta name="description" content="">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="stylesheet" href="">
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
    </body>
</html>
```

```javascript
    const videoElem = document.getElementById("video");
    const logElem = document.getElementById("log");
    const startElem = document.getElementById("start");
    const stopElem = document.getElementById("stop");
    
    // Options for getDisplayMedia()
    
    var displayMediaOptions = {
        video: {
        cursor: "never"
        },
        audio: false
    };
    
    // Create a new CaptureController instance


    // Set event listeners for the start and stop buttons
    startElem.addEventListener("click", function(evt) {
        startCapture();
    }, false);
    
    stopElem.addEventListener("click", function(evt) {
        stopCapture();
    }, false);
    
    console.log = msg => logElem.innerHTML += `${msg}<br>`;
    console.error = msg => logElem.innerHTML += `<span class="error">${msg}</span><br>`;
    console.warn = msg => logElem.innerHTML += `<span class="warn">${msg}<span><br>`;
    console.info = msg => logElem.innerHTML += `<span class="info">${msg}</span><br>`;
    
    async function startCapture() {
        logElem.innerHTML = "";
    
        try {
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
```

## Usage

Just tapping the start and stop button you can capture the screen.

