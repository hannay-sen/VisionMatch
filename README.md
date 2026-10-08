# VISION MATCH

A C++ project exploring face detection and recognition with OpenCV, built around a mock "pharmaceutical secure system" that gates access to patient records behind a webcam face check.

> **Note on the name:** the current `main.cpp` app performs face *detection* (confirming a face is present via Haar Cascade), not identity *recognition* — it grants access as soon as any face is held steady in frame for ~1 second, regardless of whose face it is. There's a separate LBPH face-recognizer training pipeline (`train.cpp` → `face_model.yml`) that isn't currently wired into `main.cpp`.

## What's in this repo

- **`main.cpp`** — the main app. Prompts for a patient name, opens the webcam, uses a Haar Cascade to detect a face, and once a face is held in frame for ~30 consecutive frames, looks up and prints that patient's record from a local text file.
- **`train.cpp`** — trains an LBPH (Local Binary Patterns Histograms) face recognizer on a folder of labeled face images and saves the result to `face_model.yml`.
- **`test.cpp`** — a tiny sanity check that prints your installed OpenCV version.
- **`face_model.yml`** — a pre-trained LBPH model produced by `train.cpp`.

## Tech Stack

- **Language:** C++
- **Computer vision:** OpenCV (core + `opencv_contrib`'s `face` module for LBPH)
- **Face detection:** Haar Cascade classifier (`haarcascade_frontalface_default.xml`)
- **Face recognition (separate pipeline):** OpenCV's LBPH Face Recognizer

## Before you run it

The code currently has **hardcoded absolute Windows paths** that you'll need to update to match your own machine:

| File | Path to update | What it's for |
|---|---|---|
| `main.cpp` | `C:/Users/thelt/OneDrive/Pictures/Desktop/360/patient_data.txt` | Text file of patient records to search |
| `main.cpp` | `C:/msys64/mingw64/share/opencv4/haarcascades/haarcascade_frontalface_default.xml` | Haar Cascade model (ships with OpenCV) |
| `train.cpp` | `C:/Users/thelt/OneDrive/Pictures/Desktop/360/faces/<id>/face_<n>.jpg` | Folder of labeled training face images |
| `train.cpp` | `C:/Users/thelt/OneDrive/Pictures/Desktop/360/face_model.yml` | Where the trained model gets saved |

You'll also need to create `patient_data.txt` yourself (format: patient name, followed by their record lines, followed by a line containing just `---` as a separator between patients) since it isn't included in the repo.

## Setup & running

1. **Install OpenCV (with contrib modules, for `train.cpp`'s LBPH recognizer)**
   - Windows (MSYS2/MinGW): `pacman -S mingw-w64-x86_64-opencv`
   - macOS: `brew install opencv`
   - Linux: `sudo apt install libopencv-dev` (may need to build `opencv_contrib` from source for the `face` module, depending on your distro)

2. **Update the hardcoded paths** in `main.cpp` and `train.cpp` (see table above) to point to files on your own machine.

3. **Compile**

   Face detection app:
   ```bash
   g++ main.cpp -o main `pkg-config --cflags --libs opencv4`
   ```

   Training script (needs `opencv_contrib`'s `face` module):
   ```bash
   g++ train.cpp -o train `pkg-config --cflags --libs opencv4`
   ```

   OpenCV version check:
   ```bash
   g++ test.cpp -o test `pkg-config --cflags --libs opencv4`
   ```

   (On Windows without `pkg-config`, link directly against your OpenCV `include`/`lib` paths instead — the exact flags depend on your OpenCV install location.)

4. **Run**
   ```bash
   ./main
   ```
   Enter a patient name when prompted, then show your face to the webcam. Press **ESC** to exit.

   To (re)train the LBPH recognizer on your own face dataset:
   ```bash
   ./train
   ```
