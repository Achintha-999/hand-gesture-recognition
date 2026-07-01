# ✋🤖 Hand Gesture Recognition

<p align="center">
  <b>Real-time hand gesture detection using MediaPipe + OpenCV</b><br/>
  Detect hand gestures from webcam input and visualize landmarks + hand skeleton.
</p>

---

## 📌 Project Overview

This project is a **Python-based real-time hand gesture recognition system**.  
It uses:

- 🧠 **MediaPipe Tasks Vision** for gesture recognition and hand landmark detection
- 📷 **OpenCV** for webcam capture and screen rendering

The program reads video from your webcam, detects the hand gesture, and displays:

- ✅ Gesture label (example: open hand, closed hand, etc.)
- ✅ Hand landmark points
- ✅ Hand skeleton connections

---

## ✨ Features

- 🎥 Real-time webcam gesture recognition
- 🖐️ Hand landmark tracking
- 🔗 Hand skeleton drawing
- 🏷️ Gesture name overlay on video frame
- 🧩 Easy to customize for your own use cases

---

## 🗂️ Project Structure

```text
hand-gesture-recognition/
├── index.py                  # Main Python script
├── gesture_recognizer.task   # Pre-trained MediaPipe gesture model
├── hand.jpg                  # Optional sample image (currently not used in runtime)

```

---

## ⚙️ Requirements

- 🐍 Python **3.9+** (recommended)
- 📦 pip package manager
- 📸 Webcam device

### Required Python Libraries

- `mediapipe`
- `opencv-python`

---

## 📥 Installation Guide

### 1) Clone the repository

```bash
git clone https://github.com/Achintha-999/hand-gesture-recognition.git
cd hand-gesture-recognition
```

### 2) Create and activate virtual environment (recommended)

#### Windows (PowerShell)
```bash
python -m venv .venv
.venv\Scripts\Activate.ps1
```

#### macOS / Linux
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3) Install dependencies

```bash
pip install mediapipe opencv-python
```

---

## 📚 How to Import Required Libraries

Use these imports (already in `index.py`):

```python
import mediapipe as mp
import cv2

BaseOptions = mp.tasks.BaseOptions
GestureRecognizer = mp.tasks.vision.GestureRecognizer
GestureRecognizerOptions = mp.tasks.vision.GestureRecognizerOptions
VisionRunningMode = mp.tasks.vision.RunningMode
```

---

## 🚀 How to Run

From the project root folder:

```bash
python index.py
```

A webcam window named **"AI Practical"** will open and start gesture recognition.

---

## 🧠 How It Works (Step-by-Step)

1. 🔧 **Load model options**
   - Uses `gesture_recognizer.task` as the model file.
   - Running mode is set to `IMAGE`.

2. 🎞️ **Capture webcam frames**
   - Opens camera with `cv2.VideoCapture(0)`.

3. 🎨 **Convert BGR to RGB**
   - OpenCV frames are BGR.
   - MediaPipe expects RGB.

4. 🖼️ **Create MediaPipe Image**
   - Frame is wrapped into `mp.Image`.

5. 🤖 **Recognize gesture**
   - `recognizer.recognize(image)` returns gesture + landmark results.

6. 🏷️ **Display gesture text**
   - Top predicted gesture is drawn with `cv2.putText`.

7. 🟢🔵 **Draw landmarks and skeleton**
   - Each hand landmark is drawn as a green circle.
   - Landmark pairs are connected with blue lines.

8. 🪟 **Show output continuously**
   - Frame is displayed in a loop.

---

## 🧾 Variable Explanation

### Core Objects

- `options` 👉 Gesture recognizer configuration (model path + mode)
- `recognizer` 👉 MediaPipe recognizer instance
- `webCam` 👉 OpenCV video capture object
- `frame` 👉 Current webcam frame (BGR)
- `rgbFrame` 👉 RGB converted frame
- `image` 👉 MediaPipe image object created from `rgbFrame`
- `result` 👉 Recognition output (gestures + landmarks)

### Gesture Variables

- `result.gestures` 👉 List of detected gestures with confidence scores
- `gestureName` 👉 Top predicted gesture name shown on screen

### Landmark & Drawing Variables

- `connection` 👉 Landmark index pairs used to draw hand skeleton
- `h, w, _` 👉 Height and width of current frame
- `hand` 👉 First detected hand landmarks (`result.hand_landmarks[0]`)
- `lm` 👉 Single landmark point in loop
- `x, y` 👉 Pixel coordinates of a landmark
- `start, end` 👉 Landmark indices to connect
- `p1, p2` 👉 Start/end landmarks for each skeleton line
- `x1, y1, x2, y2` 👉 Pixel coordinates of connected landmarks

---

## 🔍 Important Code Explanation

### 1) Model initialization

```python
options = GestureRecognizerOptions(
    base_options=BaseOptions(model_asset_path='gesture_recognizer.task'),
    running_mode=VisionRunningMode.IMAGE)
```

✅ Loads the local `.task` model file and prepares recognizer settings.

---

### 2) Webcam capture

```python
webCam = cv2.VideoCapture(0)
```

✅ Opens default webcam (`0`).  
💡 If you have multiple cameras, you can try `1` or `2`.

---

### 3) Gesture recognition call

```python
result = recognizer.recognize(image)
```

✅ Performs hand and gesture recognition for the current frame.

---

### 4) Gesture name rendering

```python
gestureName = result.gestures[0][0].category_name
cv2.putText(frame, gestureName, (20, 50), cv2.FONT_HERSHEY_SIMPLEX, 1, (0,0,255), 2)
```

✅ Takes the top prediction and displays it in red text.

---

### 5) Landmark + skeleton drawing

```python
for lm in hand:
    x = int(lm.x * w)
    y = int(lm.y * h)
    cv2.circle(frame, (x, y), 5, (0,255,0), -1)

for start, end in connection:
    ...
    cv2.line(frame, (x1, y1), (x2, y2), (255,0,0), 2)
```

✅ Draws all landmarks and connects them to form the hand structure.

---

## 🛠️ How to Adjust According to Your Requirements

### 🎯 1) Use another camera

Change:

```python
webCam = cv2.VideoCapture(0)
```

to:

```python
webCam = cv2.VideoCapture(1)
```

---

### 🎨 2) Change display colors

- Red text: `(0,0,255)`
- Green dots: `(0,255,0)`
- Blue skeleton: `(255,0,0)`

You can modify RGB/BGR tuples to your preferred colors.

---

### 🔠 3) Change text size and position

Edit this line:

```python
cv2.putText(frame, gestureName, (20, 50), cv2.FONT_HERSHEY_SIMPLEX, 1, (0,0,255), 2)
```

- `(20, 50)` = text position
- `1` = font scale
- `2` = text thickness

---

### 🖐️ 4) Handle multiple hands

Current code uses first hand only:

```python
hand = result.hand_landmarks[0]
```

To support multiple hands, loop through `result.hand_landmarks`.

---

### 🛑 5) Add quit key

Recommended improvement inside loop:

```python
if cv2.waitKey(1) & 0xFF == ord('q'):
    break
```

And after loop:

```python
webCam.release()
cv2.destroyAllWindows()
```

---

## 🧪 Troubleshooting

- ❌ **Camera not opening**
  - Check webcam permissions
  - Try another camera index (`0`, `1`, `2`)

- ❌ **Model file not found**
  - Ensure `gesture_recognizer.task` is in project root

- ❌ **No gestures detected**
  - Improve lighting
  - Keep hand fully visible in frame
  - Move hand closer to camera

---

## 📈 Suggested Future Improvements

- 🧾 Add a `requirements.txt`
- 🧱 Refactor into modules (`capture.py`, `recognizer.py`, `draw.py`)
- 🖼️ Add static-image recognition mode
- 📊 Show confidence score beside gesture label
- ⌨️ Add keyboard controls (pause/save/quit)

---

## 🤝 Contributing

Contributions are welcome! 🎉

1. Fork this repository
2. Create a feature branch
3. Commit your changes
4. Open a pull request

---

## 📜 License

No license file is currently included in this repository.  
If you plan to share this publicly, consider adding a license (e.g., MIT).

---

## 🙌 Acknowledgements

- [MediaPipe](https://developers.google.com/mediapipe)
- [OpenCV](https://opencv.org/)

---

<p align="center">
  Made with ❤️ using Python, MediaPipe, and OpenCV
</p>
