# Sending Data from OpenCV to TouchDesigner using OSC

[Jump to Instructions](#instructions)

## Intro

### Preface

I started working with OpenCV in TouchDesigner using the ScriptTOP. I got started by using this great tutorial by The Interactive & Immersive HQ, [Easy Feature Tracking with Script TOP and OpenCV in TouchDesigner](https://www.youtube.com/watch?v=1Uw2PWTR_XM). While this example is awesome it only goes as far to drawing on top of our image in OpenCV. In a [subsequent tutorial](https://www.youtube.com/watch?v=c-Sx1xo9sYQ) they show you how you can extract the data from OpenCV into a TableDAT in TouchDesigner. Now we have free reign to do what we please!

### The Problem

*The issue I ran into was when I wanted to use additional packages*

Instead of corner tracking I wanted to do some stuff with pose detection. Some quick Googling landed me on [this article](https://www.analyticsvidhya.com/blog/2021/05/pose-estimation-using-opencv/) that gives some example code. In their code you can see that they are using a package called `mediapipe`. While TouchDesigner [has a way to import Python packages](https://nvoid.gitbooks.io/introduction-to-touchdesigner/content/Python/9-6-External-Modules.html), I ran into an issue I could not resolve. After importing `mediapipe` into Touchdesigner, I tried importing it in one of my files and ran into this error:

`ImportError: Matplotlib requires numpy>=1.17; you have 1.16.2`

The issue I was running into is TouchDesigner has a version of `numpy` included in it. `mediapipe` also requires `numpy`, but it requires a newer version. I spent hours trying to override TouchDesigner's included `numpy` and did not make any progress.

### The Solution

The solution I came up with was I run OpenCV outside of TouchDesigner, in a regular Python environment. Then inside my code I use `python-osc` to send the OpenCv data into TouchDesigner over OSC. What I really like about this method is I can pretty much run any OpenCV code I find online as is, without having to adapt the code to work inside TouchDesigner.

## Instructions

### Requirements

- [TouchDesigner](https://derivative.ca/download)
- [Python 3](https://www.python.org/downloads/)

### Setup

1. Open `PostDetection.toe` in TouchDesigner.
1. Open this folder in Command Prompt/Terminal and run:

    `$ pip install opencv-python mediapipe python-osc`

1. (*Optional*) Change the `UDP_PORT` variable in the `pose-detection.py` to whatever port you want the OSC to be sent over.
1. Run

    `$ python pose-detection.py`

    in Command Prompt/Terminal and make sure it's Python 3.
    
1. The script should open a window showing the pose detection dots and lines on your video. This is coming from OpenCV and is purely there to show you it's running. It's also useful to debug any weird positioning values in TouchDesigner by comparing this output and the TouchDesigner one.
1. In TouchDesigner make sure the `/project1/oscin1` OSCInCHOP has the same network port that was used in the Python file.
1. You should be seeing values coming in TouchDesigner and seeing white dots over your image that match the position of the dots in the Python window.
1. Have fun!

## Troubleshooting

**My webcam does not show up in Touchdesigner**

Sometimes Touchdesigner will not detect your webcam if another program is using it. If your webcam is not showing up:

1. Quit TouchDesigner and exit out of the Python script.
1. Open TouchDesigner, confirm the webcam is working.
1. Run the Python script.

So far I haven't had an issue with the Python not reading the web cam.

**My webcam does not show up in the Python window**

Try changing the numbers in `cap = cv2.VideoCapture(0)`. Your webcam may be at another index, like `cap = cv2.VideoCapture(1)`. If still not resolved I would Google the issue.

## Todo:

1. Name the individual pose landmarks so that instead of the OSC channels being `landmark-27-x` they would be `landmark-right-wrist-x`.
1. Send through the data we can use to draw lines in between the appropriate landmarks.