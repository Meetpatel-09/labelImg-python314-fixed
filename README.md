# 🏷️ LabelImg — Python 3.14 Fixed

A compatibility-fixed version of **LabelImg 1.8.6** for **Python 3.14 + PyQt5**.

This fork fixes PyQt5 drawing crashes caused by floating-point coordinates being passed to `drawRect()` and `drawLine()` when running LabelImg on newer Python/PyQt5 environments.

> **Note:** This is a compatibility fork of the original open-source LabelImg project. I am not the original creator of LabelImg.

---

## 📌 Why This Fork Exists

When running LabelImg with **Python 3.14**, the application may start successfully but crash while drawing bounding boxes or moving the mouse over an image.

For example:

```text
TypeError: arguments did not match any overloaded call:
drawRect(self, rect: QRectF): argument 1 has unexpected type 'float'
drawRect(self, x: int, y: int, w: int, h: int): argument 1 has unexpected type 'float'
```

You may also encounter:

```text
TypeError: arguments did not match any overloaded call:
drawLine(self, x1: int, y1: int, x2: int, y2: int):
argument 1 has unexpected type 'float'
```

The problem occurs because some coordinates used by LabelImg are floating-point values, while the corresponding PyQt5 drawing methods expect integer coordinates for these overloads.

This fork fixes those crashes by explicitly converting the relevant coordinates to integers before passing them to PyQt5.

---

## 🔧 What Was Fixed

The compatibility changes are primarily in:

```text
libs/canvas.py
```

### `drawRect()` Fix

Original code:

```python
p.drawRect(left_top.x(), left_top.y(), rect_width, rect_height)
```

Fixed code:

```python
p.drawRect(
    int(left_top.x()),
    int(left_top.y()),
    int(rect_width),
    int(rect_height)
)
```

### `drawLine()` Fix

Original code:

```python
p.drawLine(
    self.prev_point.x(),
    0,
    self.prev_point.x(),
    self.pixmap.height()
)
```

Fixed code:

```python
p.drawLine(
    int(self.prev_point.x()),
    0,
    int(self.prev_point.x()),
    self.pixmap.height()
)
```

The same integer conversion is applied to the horizontal crosshair coordinates.

---

## ✅ Tested Environment

This fork was tested with:

| Component | Version |
|---|---|
| Operating System | Windows |
| Python | 3.14 |
| LabelImg | 1.8.6 |
| PyQt5 | 5.15.11 |
| PyQt5-Qt5 | 5.15.2 |
| lxml | 6.x |

The purpose of this fork is specifically to resolve the PyQt5 drawing errors encountered when using LabelImg with Python 3.14.

---

# 🚀 Installation

## 1. Clone This Repository

Open PowerShell, Command Prompt, Git Bash, or your preferred terminal:

```bash
git clone https://github.com/Meetpatel-09/labelImg-python314-fixed.git
```

Move into the project:

```bash
cd labelImg-python314-fixed
```

---

## 2. Create a Virtual Environment

Recommended:

```bash
python -m venv .venv
```

### Windows PowerShell

Activate it using:

```powershell
.\.venv\Scripts\Activate.ps1
```

### Windows Command Prompt

```cmd
.venv\Scripts\activate
```

After activation, your terminal should show something similar to:

```text
(.venv)
```

---

## 3. Install Dependencies

If the repository contains `requirements.txt`:

```bash
pip install -r requirements.txt
```

Alternatively, install the required packages directly:

```bash
pip install PyQt5==5.15.11 lxml
```

---

## 4. Start LabelImg

Run:

```bash
python labelImg.py
```

The LabelImg interface should now open.

---

# 🎯 Using LabelImg for YOLO Object Detection

LabelImg supports multiple annotation formats, including:

- Pascal VOC
- YOLO
- CreateML

For YOLO object detection, select **YOLO** as the annotation format before labeling your images.

---

## Basic Annotation Workflow

### Step 1 — Open Your Image Folder

Click:

```text
Open Dir
```

and select the folder containing your dataset images.

---

### Step 2 — Select YOLO Format

Make sure the annotation format is:

```text
YOLO
```

instead of PascalVOC.

---

### Step 3 — Create a Bounding Box

Click:

```text
Create RectBox
```

or press:

```text
W
```

Draw a bounding box around the object you want your YOLO model to detect.

---

### Step 4 — Enter the Class Name

For example:

```text
number_plate
```

Other examples could be:

```text
helmet
car
person
fire
dog
cat
```

Use class names appropriate for your dataset.

---

### Step 5 — Save the Annotation

Press:

```text
Ctrl + S
```

LabelImg will save the annotation for the image.

---

# 📁 YOLO Annotation Format

For an image such as:

```text
image_001.jpg
```

YOLO creates a corresponding label file:

```text
image_001.txt
```

A YOLO annotation may look like:

```text
0 0.512500 0.463000 0.328000 0.116000
```

The values represent:

```text
class_id x_center y_center width height
```

For example:

```text
0       → Class ID
0.5125  → X center
0.4630  → Y center
0.3280  → Bounding-box width
0.1160  → Bounding-box height
```

The bounding-box coordinates are normalized relative to the image dimensions.

---

# 🤖 YOLO Dataset Workflow

A typical custom object-detection workflow looks like:

```text
Collect Images
      ↓
Label Images with LabelImg
      ↓
Export YOLO Annotations
      ↓
Create Train / Validation / Test Sets
      ↓
Configure data.yaml
      ↓
Train YOLO
      ↓
Evaluate Model
      ↓
Test on New Images / Videos
      ↓
Deploy
```

This fork can be used for the **image annotation** stage of that workflow.

---

# 📂 Example Dataset Structure

After preparing your dataset for YOLO training, you will commonly have a structure similar to:

```text
dataset/
│
├── train/
│   ├── images/
│   │   ├── image_001.jpg
│   │   ├── image_002.jpg
│   │   └── ...
│   │
│   └── labels/
│       ├── image_001.txt
│       ├── image_002.txt
│       └── ...
│
├── val/
│   ├── images/
│   └── labels/
│
└── test/
    ├── images/
    └── labels/
```

---

# 🐛 Error Fixed by This Fork

If the original LabelImg installation gives you an error similar to:

```text
File "libs/canvas.py", line 526, in paintEvent
    p.drawRect(left_top.x(), left_top.y(), rect_width, rect_height)

TypeError: arguments did not match any overloaded call:
drawRect(self, rect: QRectF): argument 1 has unexpected type 'float'
drawRect(self, x: int, y1: int, w: int, h: int):
argument 1 has unexpected type 'float'
```

or:

```text
File "libs/canvas.py", line 530, in paintEvent
    p.drawLine(...)

TypeError: arguments did not match any overloaded call:
drawLine(self, x1: int, y1: int, x2: int, y2: int):
argument 1 has unexpected type 'float'
```

this fork contains compatibility changes for those drawing operations.

---

# ⚠️ Important Notes

This repository focuses on the **Python 3.14 / PyQt5 drawing compatibility issue** that I encountered while using LabelImg.

It does **not** represent an official continuation of LabelImg.

Other issues may still exist because the original LabelImg project is no longer actively developed.

If you encounter another Python 3.14 compatibility issue, feel free to open an issue with:

- Python version
- Operating system
- PyQt5 version
- Full traceback
- Steps to reproduce the problem

---

# 🤝 Contributing

Contributions are welcome.

If you find another compatibility issue:

1. Fork this repository.
2. Create a new branch.

```bash
git checkout -b fix/your-fix-name
```

3. Make your changes.
4. Commit them.

```bash
git commit -m "fix: describe your compatibility fix"
```

5. Push your branch.

```bash
git push origin fix/your-fix-name
```

6. Open a Pull Request.

Please keep compatibility fixes focused and explain what error the change resolves.

---

# 📺 Coding With Meet

This compatibility fork was prepared while creating computer vision and object-detection tutorials for **Coding With Meet**.

The tutorial series covers topics such as:

- Dataset collection
- Image annotation
- YOLO dataset preparation
- Custom object detection
- YOLO26 training
- Model evaluation
- Object detection on images and videos
- Real-time computer vision

YouTube:

**Coding With Meet**

https://www.youtube.com/@codingwithmeet

---

# 🙏 Credits

LabelImg was originally created by **Tzutalin** and developed with contributions from the open-source community.

This repository is based on the original LabelImg project and only provides compatibility modifications.

Original LabelImg repository:

https://github.com/HumanSignal/labelImg

Original project:

https://github.com/tzutalin/labelImg

All credit for the original LabelImg application belongs to its original creator and contributors.

---

# 📜 License

This project retains the license of the original LabelImg project.

See the [`LICENSE`](LICENSE) file for details.

---

## ⭐ Found This Fix Useful?

If this repository helped you run LabelImg on Python 3.14, consider giving the repository a ⭐.

It helps other developers who encounter the same compatibility issue find the fix.

---

**Maintained by [Meet Patel](https://github.com/Meetpatel-09)**
