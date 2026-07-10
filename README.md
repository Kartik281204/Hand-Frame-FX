# Hand Frame FX

Make an "L" with your thumb and index finger on **both** hands (like framing
a shot). A rectangle appears between your hands, and a visual effect
(glassmorphism, duotone, pop art, glow, grid, pixelate, invert) renders only
inside that rectangle — everything outside stays a normal camera feed.

Close your hands (breaking the pose) and re-form the L-rectangle again, and
the effect jumps to the next one in the cycle — so re-opening the "frame"
always reveals something new. You can also press space to advance without
breaking the pose.

## Setup

This needs an actual webcam, so it must run on your own machine (not in a
sandbox). Requires Python 3.9–3.11 (mediapipe doesn't yet support 3.12+ on
all platforms).

```bash
python3 -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt
python hand_frame_fx.py
```

## Controls

| Action                                   | Result                       |
|-------------------------------------------|-------------------------------|
| close hands, then re-form the L-rectangle | advances to the next effect   |
| space                                      | manually advances to the next effect |
| l                                           | toggle hand-landmark debug overlay |
| q                                           | quit                          |

## How it works

- **MediaPipe Hands** tracks 21 landmarks per hand.
- `detect_L_shape()` checks that middle/ring/pinky are curled and that
  thumb + index are extended at roughly a 45–135° angle — i.e. an "L".
- When both hands are making an "L", the bounding box of the four
  fingertip points (2 index tips + 2 thumb tips) becomes the effect
  rectangle. It's smoothed frame-to-frame so it doesn't jitter.
- The current effect (`EFFECTS` list in the script) is applied only to the
  pixels inside that rectangle; pressing **space** advances to the next
  effect in the list.

## Tweaking

- Add your own look by writing a function `fx_yourname(patch) -> patch`
  (patch is a BGR numpy array) and appending it (plus a name) to `EFFECTS`
  / `EFFECT_NAMES` near the top of `hand_frame_fx.py`.
- If gesture detection feels too strict/loose, adjust the thresholds in
  `detect_L_shape()` (the `1.2`, `1.1`, and `45 <= ang <= 135` values).
- If your camera isn't index `0`, change `cv2.VideoCapture(0)`.
