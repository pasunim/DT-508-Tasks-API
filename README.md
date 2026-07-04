# Workshop 1 — MediaPipe Hand & Pose Landmark Demos

Real-time hand and body pose landmark detection using [MediaPipe](https://ai.google.dev/edge/mediapipe/solutions/guide) Tasks Vision API and OpenCV, reading live video from your webcam.

## Features

- **`hand_demo.py`** — detects up to 2 hands and draws all 21 landmarks per hand. Fingertip joints (thumb, index, middle, ring, pinky tips) are highlighted in green and drawn larger; the rest of the joints are drawn in white.
- **`pose_demo.py`** — detects a single full-body pose and draws all 33 body landmarks in yellow.

Both scripts run detection asynchronously (`LIVE_STREAM` mode) so the video feed stays smooth while MediaPipe processes frames in the background.

## Requirements

- Python 3.14+
- A webcam
- [uv](https://docs.astral.sh/uv/) (recommended) or pip

Dependencies (see [pyproject.toml](pyproject.toml)):

- [`mediapipe`](https://pypi.org/project/mediapipe/) >= 0.10.35
- [`opencv-python`](https://pypi.org/project/opencv-python/) >= 5.0.0.93

## Project structure

```
.
├── hand_demo.py                       # Hand landmark detection demo
├── pose_demo.py                       # Pose landmark detection demo
├── main.py                            # Placeholder entry point
├── models/
│   ├── hand_landmarker.task           # MediaPipe hand landmarker model
│   └── pose_landmarker_lite.task      # MediaPipe pose landmarker model (lite)
├── pyproject.toml
├── uv.lock
└── requirements.txt
```

## Setup

Clone the repository and install dependencies:

```bash
git clone https://github.com/pasunim/DT-508-Tasks-API.git
cd DT-508-Tasks-API
uv sync
```

`uv sync` creates a `.venv` and installs the locked dependencies from `uv.lock`. If you prefer plain `pip`:

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

The model files under `models/` are already included in this repo, so no separate download step is required.

## Usage

Run the hand landmark demo:

```bash
uv run python hand_demo.py
```

Run the pose landmark demo:

```bash
uv run python pose_demo.py
```

A window will open showing your webcam feed with landmarks overlaid in real time. Press **`ESC`** to close the window and stop the script.

> **Note:** Grant camera access to your terminal/IDE if prompted by the OS — otherwise `cv2.VideoCapture(0)` will fail to read frames.

## How it works

Each demo follows the same pattern:

1. Configures a `HandLandmarkerOptions` / `PoseLandmarkerOptions` object pointing at the bundled `.task` model file, running in `LIVE_STREAM` mode with an async `result_callback`.
2. Opens the default webcam with `cv2.VideoCapture(0)`.
3. On each loop iteration, converts the BGR frame from OpenCV to an RGB `mp.Image` and sends it to `detect_async` along with a timestamp.
4. The callback (`process_result` / `process_pose`) stores the latest detection result in a module-level variable.
5. The main loop reads the latest stored result (if any) and draws landmark circles onto the current frame with `cv2.circle`.
6. The frame is flipped horizontally (mirror view) and displayed with `cv2.imshow`.

## Troubleshooting

- **Black/frozen window or no landmarks detected**: make sure there's adequate lighting and your hand/body is fully in frame.
- **`FileNotFoundError` for the `.task` model**: run the scripts from the project root so the relative path `models/...` resolves correctly.
- **Camera permission errors on macOS**: go to *System Settings → Privacy & Security → Camera* and enable access for your terminal or IDE.
