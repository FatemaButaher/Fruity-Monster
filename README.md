# Fruity Monster

**Fruity Monster** is an interactive educational game that helps children learn fruit names, colors, and basic English vocabulary through movement, speech, and playful challenges.

**GitHub:** https://github.com/FatemaButaher/Fruity-Monster

## Problem and Motivation

Traditional vocabulary activities can feel repetitive for children. Fruity Monster combines learning and play with immediate feedback, character animation, and interactive controls to make practice more engaging.

## Game Modes

1. **Eat or Don't Eat:** Guide a monster through five levels, collect fruits, and avoid non-food objects and obstacles. Hand gestures control movement and jumping, while mouth detection supports eating.
2. **Break the Fruit:** Answer spoken questions about fruit names and colors. Correct answers break the fruit; incorrect answers allow another attempt or a skip.
3. **Find the Fruit:** The monster starts between two fruit cards. Move left or right to reach the correct card and earn a point. After choosing, the monster walks back to the center before the next cards appear.

Players can choose from **four monsters**. The game includes **ten fruits**, rewards, and a boss level in Eat or Don't Eat.

## Datasets and Sources

Two labeled datasets from Roboflow were used for model-development experiments.

| Dataset | Train | Validation | Test | Total | Classes |
|---|---:|---:|---:|---:|---|
| Mouth detection | 3,042 | 290 | 144 | 3,476 | Close, Open |
| Hand direction detection | 562 | 160 | 79 | 801 | Left, Right, Up |

- **Mouth source:** [Open Mouth — Roboflow](https://universe.roboflow.com/open-mouth/open-mouth)
- **Hand source:** [Hand Point — Roboflow](https://app.roboflow.com/fatima-laloa/hand-point/train) (access may require permission)

The mouth training data included 1,527 annotations for one class and 1,515 for the other, indicating a nearly balanced distribution.

## Model Development

Pretrained **YOLO26s** object-detection models were fine-tuned at **640 × 640** resolution. The mouth model trained for **10 epochs**; the experimental hand direction model was configured for **100 epochs** and stopped at epoch **97**.

In the final game:
- **YOLO** detects open and closed mouth states.
- **MediaPipe Hand Landmarker** supports hand-based movement and selection.
- **Speech recognition** processes spoken answers in Break the Fruit.

The separately trained YOLO hand direction model was part of experimentation and is **not** the hand tracker used in the final game.

## Model Evaluation

The following are results from the models' held-out test sets:

| Metric | Mouth YOLO | Experimental Hand YOLO |
|---|---:|---:|
| Precision | 99.87% | 99.94% |
| Recall | 99.51% | 100.00% |
| mAP@50 | 99.50% | 99.50% |
| mAP@50–95 | 77.00% | 99.10% |

The mouth detector showed very strong test-set performance, with some remaining limitations in precise bounding-box localization and background detection. The experimental hand model distinguished Left, Right, and Up well on its test set, although background false positives were visible in the normalized confusion matrix.

**No separate quantitative baseline model or baseline comparison was completed.** These results are reported as test-set evaluations of the trained YOLO models; they do not represent measured accuracy for MediaPipe or speech recognition.

## Technologies

Python, Arcade, Ultralytics YOLO, MediaPipe, OpenCV, NumPy, Pillow, SpeechRecognition, and pyttsx3.

## How to Run

1. Download or clone this repository.
2. Install the required libraries in a compatible Python environment:

   ```bash
   pip install arcade==3.3.3 ultralytics mediapipe opencv-python numpy pillow SpeechRecognition pyttsx3 pyaudio
   ```

3. Keep the game file, model files, character folders, images, and audio files in their expected locations.
4. Open **`final_game.ipynb`** in JupyterLab and select **Run All**. If using the final Python script instead, open and run that file.
5. Allow webcam and microphone access, choose a character, and start playing.

An internet connection is required for the Google-based speech recognition used by the game.

## Project Files

- `final_game.ipynb` / final `.py` file — main game (use the format included in the uploaded repository)
- `game_mouth.ipynb` — mouth detector training and evaluation
- `game_hand.ipynb` — experimental hand detector training and evaluation
- `best.pt` — mouth detector weights
- `hand_landmarker.task` — hand tracking model
- `purple/`, `green/`, `orange/`, `pink/` — character animations
- PNG and WAV files — game graphics and sounds

## Limitations and Future Work

Gameplay detection can be affected by camera lighting, hand visibility, and player position. Speech recognition can be affected by noise, pronunciation, and internet connectivity. Future improvements could include broader user testing, offline voice recognition, and additional educational activities. Educational learning outcomes have not been formally evaluated.

## Demo and Presentation

- **Demo video:** 🫠
- **Presentation:** 🫠

## Acknowledgments and License

Created as a Data Science Capstone project with **General Assembly** and **Tamkeen**. Thanks to the creators of the open-source tools and Roboflow datasets used in the project.

**TODO:** Confirm the project's license, third-party asset/model permissions and required attributions, and any AI-assistance acknowledgment required by the program.
