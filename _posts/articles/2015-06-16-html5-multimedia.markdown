---
layout: post
title: "HTML5 Multimedia"
date: 2015-06-16T11:57:21-04:00
excerpt: "HTML5 multimedia elements and features."
categories: articles
tags: [html5, accessibility,html5 multimedia, html5 video, html5 audio]
author: pduran
share: true
image:
  <!-- feature: so-simple-sample-image-1.jpg -->
  credit: kiubmen
  creditlink: http://kiubmen.com
---

+   [HTML5](#HTML5)
    *   [getUserMedia](#getUserMedia)
    *   [CSS effects on a video element with a live webcam](#css-effects-on-a-video-element-with-a-live-webcam)
    *   [Canvas](#canvas)
    *   [Audio, the microphone](#audio-the-microphone)
    *   [Sources](#sources)


## getUserMedia

### How to stop/release the webcam

Set the `navigator.getUserMedia` method with the name that works on the current browser. As some browsers only have experimental implementations, this method needs to be prefixed by `-webkit` or `-moz` etc. Like that, in the rest of the code, we can just use navigator.getUserMedia without worrying about prefixes. In case the browser does not support this API at all, it will receive null.

<details>
    <summary>
            Code block
    </summary>
    {% render_code start-stop-webcam.html lang:javascript mark:6 range:6-9 %}
</details>

We test if `navigator.getUserMedia` is not null (supported by the current browser).

<details>
    <summary>
            Code block
    </summary>
    {% render_code start-stop-webcam.html lang:javascript mark:15 range:14-17 %}
</details>

[See example on JSBin](http://jsbin.com/nacuqa/edit?html,output)
<!-- {% jsbin nacuqa html,output %} -->

### CSS effects on a video element with a live webcam
[See example on JSBin](http://jsbin.com/gehitu/8/edit?html,js)

### Canvas

Take a snapshot code

```javascript
var canvas, ctx;

function init() {
    // Get the canvas by element id
    canvas = document.getElementById("myCanvas");
    // Obtain a context for drawing in it
    ctx = canvas.getContext('2d');
}

function snapshot() {
    // Draws current image from the video element into the canvas
    ctx.drawImage(video, 0,0, canvas.width, canvas.height);
}
```

### How to set the webcam resolution
It is possible to set "hints" for the preferred resolution during video capture. This is done by using a "constraint" object that is passed as a parameter to the `getUserMedia(...)` method. It's just the same object we passed in the basic example: `navigator.getUserMedia({video:true}, success, error)` except that this time this object is a little more complex.

```javascript
var constraint = {
    video: {
        mandatory: {
            maxWidth: 320,
            maxHeight: 200
        }
    }
};
 
function sucess(stream) {
    video.src = window.URL.createObjectURL(stream);
}
 
function error(error) {
    console.log('navigator.getUserMedia error: ', error);
}

navigator.getUserMedia(constraint, success, error);
```

#### Choose between 3 different resolutions

We use `onload="init();"` to initialize var
```html
<body onload="init();">
```


```javascript
var vgaButton, qvgaButton, hdButton, dimensions, video, stream;
 
function init() {
    vgaButton = document.querySelector('button#vga');
    qvgaButton = document.querySelector('button#qvga');
    hdButton = document.querySelector('button#hd');
    dimensions = document.querySelector('p#dimensions');
    video = document.querySelector('video');

    navigator.getUserMedia = (navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia);

    // Defines event listeners for the buttons that set the resolution
    qvgaButton.onclick = function() {
        getMedia(qvgaConstraints);
    };
    vgaButton.onclick = function() {
        getMedia(vgaConstraints);
    };
    hdButton.onclick = function() {
        getMedia(hdConstraints);
    };
 
    // Trick: check regularly the size of the video element and display it
    // When getUserMedia is called the video element changes it sizes but for
    // a while its size is zero pixels... o we check every half a second
   video.addEventListener('play', function() {
       setTimeout(function() {
           displayVideoDimensions();
       }, 500);
    });
}
```

The different values for the constraints on resolution.
```javascript
var qvgaConstraints = {
   video: {
      mandatory: {
         maxWidth: 320,
         maxHeight: 180
      }
   }
};
 
var vgaConstraints = {
   video: {
      mandatory: {
         maxWidth: 640,
         maxHeight: 360
      }
   }
};
 
var hdConstraints = {
   video: {
      mandatory: {
         minWidth: 1280,
         minHeight: 720
      }
   }
};
```

The function that is called when a button has been clicked: starts the video
```javascript
// with the preferred resolution
function getMedia(constraints) {
    if (!!stream) {
        video.src = null;
        stream.stop();
    }
    navigator.getUserMedia(constraints, successCallback, errorCallback);
}
 
// callback if the capture is a sucess or an error
function successCallback(stream) {
    windows.stream = stream; // For resetting it later if we change the resolution
    video.src = window.URL.createObjectURL(stream);
}

function errorCallback(error) {
    console.log('navigator.getUserMedia error: ', error);
}

// util function that is called by the setInterval(...) every 0.5s, for
// displaying the video dimensions
function displayVideoDimensions() {
    dimensions.innerHTML = 'Actual video dimensions: ' + video.videoWidth + 'x' + video.videoHeight + 'px.';
}
```

<details>
    <summary>
            Code block: The html view
    </summary>

```html
<!DOCTYPE html>
<html>
    <head>
        <title>getUserMedia constraints for choosing resolution</title>
    </head>

    <body onload="init();">
        <h1>Set camera resolution</h1>
        <p>Click a button to call <code>getUserMedia()</code> with appropriate resolution.</p>

        <div id="buttons">
           <button id="qvga">QVGA</button>
           <button id="vga">VGA</button>
           <button id="hd">HD</button>
        </div>

        <p id="dimensions"></p>

        <video autoplay></video>
    </body>
</html>
```
</details>



[Example at JS Bin](http://jsbin.com/jigiru/13/edit?html,js)

### Audio, the microphone
We can specify what we want to capture with the webcam stream `video: true, audio: false` also
 it's possible to use `{audio:true}` for the first parameter, in that case, only the microphone input will be captured.

```javascript
navigator.getUserMedia ({video: true, audio: false}, onSuccess, onError)
```

[Appear.In a WebRTC application](https://appear.in/)


#### Sources
* [HTML 5 Accessibility](https://dequeuniversity.com/assets/html/jquery-summit/html5/slides/html5.html)
