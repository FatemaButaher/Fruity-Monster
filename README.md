# Fruity Monster 

Fruity Monster is an AI-powered educational game that helps children learn fruit names, colors, and English vocabulary through interactive play.

## Problem and Motivation

Traditional learning activities can be repetitive. Fruity Monster aims to make learning more engaging through movement, voice interaction, immediate feedback, and rewards.

## Game Modes

- **Eat or Don't Eat:** Control a monster with hand gestures and mouth movements, collect fruits, and avoid non-food items across five levels.
- **Break the Fruit:** Say the color and name of a fruit to break it and continue. The game tracks correct answers and completion time.
- **Say the Fruit:** Listen to fruit names, repeat them, and receive feedback.

The game includes four selectable characters and ten fruits.

## Datasets

Both object-detection datasets were obtained through Roboflow.

| Dataset | Training | Validation | Test | Total |
|---|---:|---:|---:|---:|
| Mouth (version 1) | 3,042 | 290 | 144 | 3,476 |
| Hand (version 2) | 562 | 160 | 79 | 801 |

- Mouth classes: open and close. [Dataset source](https://universe.roboflow.com/open-mouth/open-mouth)
- Hand classes: left, right and up. [Roboflow project](https://app.roboflow.com/fatima-laloa/hand-point/train) (may require access permission).

The training label counts were nearly balanced for the mouth model (class 0: 1,527; class 1: 1,515). For the hand model, the recorded training label counts were 195, 197 and 167 for class IDs 0, 1 and 2, respectively. These are annotation counts, not necessarily image counts.

**TODO:** Confirm that the mouth source link corresponds to the version exported in the training notebook, and provide an accessible hand dataset link if necessary.

## Model Development

Pretrained YOLO26s models were fine-tuned on labeled images at 640 × 640 resolution. The mouth model was trained for 10 epochs. The experimental hand model was configured for 100 epochs with patience of 20 and stopped at epoch 97, with its best checkpoint at epoch 77. Horizontal and vertical flipping were disabled for the hand model to preserve direction labels.

The final game uses YOLO for mouth detection, MediaPipe Hand Landmarker for hand gestures, and Google speech recognition for voice interactions. The trained YOLO hand detector was an experimental approach, not the final hand controller.

## Evaluation Results

The following results are from the held-out test splits in the training notebooks.

| Metric | Mouth YOLO | Hand YOLO (experimental) |
|---|---:|---:|
| Precision | 99.87% | 99.94% |
| Recall | 99.51% | 100% |
| mAP@50 | 99.50% | 99.50% |
| mAP@50–95 | 77.00% | 99.10% |

The mouth model detects both mouth states effectively on the test split, although its lower mAP@50–95 indicates room for improvement in bounding-box localization. These results do not establish performance for every real-world player or camera condition. MediaPipe and speech recognition were not evaluated using these YOLO metrics.

### Class-wise Test Results

| Model | Class | Test images | Precision | Recall | mAP@50 | mAP@50–95 |
|---|---|---:|---:|---:|---:|---:|
| Mouth | Close | 66 | 0.997 | 1.000 | 0.995 | 0.722 |
| Mouth | Open | 78 | 1.000 | 0.990 | 0.995 | 0.819 |
| Hand | Left | 31 | 0.998 | 1.000 | 0.995 | 0.990 |
| Hand | Right | 29 | 1.000 | 1.000 | 0.995 | 0.988 |
| Hand | Up | 19 | 1.000 | 1.000 | 0.995 | 0.995 |

### Confusion Matrices and Error Analysis

**Mouth detection**

![Mouth confusion matrix](screenshots/mouth_confusion_matrix.png)

The normalized matrix shows approximately 1% of open-mouth instances classified as closed, with no observed confusion in the opposite direction. Background false positives were also present. The lower mAP@50–95 suggests that bounding-box localization can be improved.

**Hand gesture detection (experimental)**

![Hand confusion matrix](screenshots/hand_confusion_matrix.png)

No confusion between the three gesture classes was observed in the matrix. Background false positives were assigned to Left. These results indicate strong test-set performance, but do not imply error-free detection in every situation.

### Baseline Comparison

The initial proof of concept demonstrated mouth-controlled interaction. The final game extends that concept with real-time hand tracking, three game modes and speech interaction. A separate quantitative baseline evaluation is not documented in the supplied notebooks, so no numerical improvement over a baseline is claimed.

**TODO:** Add baseline metrics if available, or confirm that no separate quantitative baseline was recorded.

## How to Run

1. Download or clone the [GitHub repository](https://github.com/FatemaButaher/Fruity-Monster).
2. Install the required Python libraries: Arcade (3.3.3), Ultralytics, MediaPipe, OpenCV, NumPy, Pillow, SpeechRecognition, pyttsx3, and PyAudio. Use a Python environment compatible with these packages.
3. Connect a webcam and microphone. An internet connection is required for Google speech recognition.
4. Open the `final_game` notebook in Jupyter and select **Run All**. Keep the model files and game assets in their existing locations relative to the notebook.

Dependency installation command (in a terminal):

`pip install arcade==3.3.3 ultralytics mediapipe opencv-python numpy pillow SpeechRecognition pyttsx3 pyaudio`

## Repository Structure

- `final_game.ipynb` — main game notebook (confirm extension/location).
- `game_mouth.ipynb` — mouth model training and evaluation.
- `game_hand.ipynb` — experimental hand model training and evaluation.
- `best.pt` — trained mouth model weights.
- `hand_landmarker.task` — MediaPipe hand landmark model.
- `purple/`, `green/`, `orange/`, `pink/` — character sprites.
- PNG and WAV files — game assets and sounds.

**TODO:** Confirm paths against the final repository and upload the training notebooks after removing credentials.

## Limitations and Future Work

Detection may be affected by lighting, camera position, and occlusion. Speech recognition may be affected by background noise, pronunciation, and internet connectivity. Voice recognition sends audio to an external service; appropriate parental consent and privacy safeguards are important for children. Future work could include testing with diverse users, offline speech recognition, and more learning activities. Educational effectiveness has not yet been formally measured.

## Demo 

**TODO:** Add demo video and presentation links.
