# Installation Guide — BubbleID-Droplet

This guide assumes a fresh Windows machine, CPU-only inference, and pip for all package management.

---

## Table of Contents
1. [Prerequisites](#1-prerequisites)
2. [Clone the Repository](#2-clone-the-repository)
3. [Create a Virtual Environment](#3-create-a-virtual-environment)
4. [Install PyTorch](#4-install-pytorch)
5. [Install Detectron2](#5-install-detectron2)
6. [Install Remaining Dependencies](#6-install-remaining-dependencies)
7. [Download Model Weights](#7-download-model-weights)
8. [Launch the Notebook](#8-launch-the-notebook)
9. [Troubleshooting](#9-troubleshooting)

---

## 1. Prerequisites

### Python 3.10

1. Download Python 3.10 from https://www.python.org/downloads/release/python-31011/
2. Run the installer.
3. **Important:** On the first screen, check **"Add Python to PATH"** before clicking Install.
4. Verify the install by opening **Command Prompt** (search `cmd` in Start Menu) and running:
   ```
   python --version
   ```
   You should see `Python 3.10.x`.

### Git

1. Download from https://git-scm.com/download/win and run the installer.
2. Accept all defaults.
3. Verify:
   ```
   git --version
   ```

### Visual Studio C++ Build Tools (required to compile Detectron2)

Detectron2 must be compiled from source and requires the MSVC C++ compiler.

1. Download **Visual Studio Build Tools 2022** (free) from:
   https://visualstudio.microsoft.com/visual-cpp-build-tools/
2. Run the installer, select the **"Desktop development with C++"** workload, and click Install.
3. **Restart your computer** after installation completes.

---

## 2. Clone the Repository

Open **Command Prompt**, navigate to where you want the project folder, then clone:

```
git clone https://github.com/cldunlap73/BubbleID-Droplet.git
cd BubbleID-Droplet
```

---

## 3. Create a Virtual Environment

Create an isolated environment inside the project folder so packages don't conflict with other Python projects:

```
python -m venv venv
```

Activate it:

```
venv\Scripts\activate
```

You should see `(venv)` at the start of your prompt. **All remaining commands must be run with this environment active.**

> To reactivate the environment in a new Command Prompt session later, navigate back to the project folder and run `venv\Scripts\activate` again.

---

## 4. Install PyTorch (CPU)

```
pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
```

Verify:

```
python -c "import torch; print(torch.__version__)"
```

---

## 5. Install Detectron2

This step compiles Detectron2 from source and takes several minutes. Make sure the C++ Build Tools from step 1 are installed before running this.

```
pip install "git+https://github.com/facebookresearch/detectron2.git"
```

Verify:

```
python -c "import detectron2; print(detectron2.__version__)"
```

### If the build fails

The most common cause is that the compiler is not on the system PATH. Try the following:

1. Close Command Prompt.
2. Open **x64 Native Tools Command Prompt for VS 2022** (search for it in the Start Menu).
3. Navigate back to the project folder and reactivate the environment:
   ```
   cd path\to\BubbleID-Droplet
   venv\Scripts\activate
   ```
4. Re-run the pip install command above.

---

## 6. Install Remaining Dependencies

```
pip install opencv-python matplotlib numpy scipy scikit-image tifffile
```

Install the Jupyter kernel so VS Code can find this environment:

```
pip install ipykernel
python -m ipykernel install --user --name bubbleid --display-name "BubbleID"
```

---

## 7. Download Model Weights

The notebook requires a pre-trained weights file (`instance_segmentation_weights.pth`) from the BubbleID project.

1. Download `model_1class.pth` (334.7 MB) directly from: https://osf.io/9azdx
2. Click the **Download** button on that page.
3. Save the `.pth` file anywhere convenient — a file browser dialog will ask you to select it when you run the notebook.

---

## 8. Launch the Notebook

1. Open the `BubbleID-Droplet` folder in VS Code (**File → Open Folder**).
2. Open `BubbleID_for_Rmax_growth_rate.ipynb`.
3. In the kernel selector (top-right of the notebook), choose **BubbleID**.
4. Run the cells from top to bottom.

### What each cell does

**Cell 1 — Configuration:**
- Confirms CUDA is not available and sets inference to CPU.
- Opens two file browser dialogs:
  1. Select your `.pth` model weights file.
  2. Select the input image (`.tif`, `.png`, or `.jpg`).

**Cell 2 — Detection and Analysis:**
- Loads the model and runs 8 overlapping detection passes across the image.
- Applies watershed segmentation to separate touching droplets.
- Saves three output files in the same folder as your input image:
  - `<name>_fullmask.png` — binary mask overlay
  - `<name>_circles.png` — image with fitted circles on each droplet
  - `<name>_diameters.txt` — list of equivalent diameters in pixels

**Cell 3 — FOV (optional):**
- Only runs if you set crop coordinates (`x_min`, `y_min`, etc.) in Cell 1.
- Extracts results for a user-specified field-of-view sub-region.

---

## 9. Troubleshooting

### `ModuleNotFoundError: No module named 'detectron2'`
The `(venv)` environment is not active, or the wrong kernel is selected in VS Code. Reactivate the environment and make sure the **BubbleID** kernel is selected.

### File dialog does not appear (Cell 1 hangs)
This is a `tkinter` display issue. Reinstall tk support:
```
pip install tk
```
Then restart the kernel and rerun Cell 1.

### `cv2.error` or image fails to load
Confirm `opencv-python` is installed:
```
pip show opencv-python
```
Also check that the image path contains no special characters that could confuse the file reader.

### Inference is slow
CPU inference is expected to be significantly slower than GPU. Processing a large image with 8 detection passes may take several minutes. This is normal.

---

## Quick Reference — All Commands

```
git clone https://github.com/cldunlap73/BubbleID-Droplet.git
cd BubbleID-Droplet
python -m venv venv
venv\Scripts\activate
pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
pip install "git+https://github.com/facebookresearch/detectron2.git"
pip install opencv-python matplotlib numpy scipy scikit-image tifffile
pip install ipykernel
python -m ipykernel install --user --name bubbleid --display-name "BubbleID"
```
