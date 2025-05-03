

---

# 🚦 Traffic Homography and Tracking 

## 📌 Project Overview

This project uses **YOLOv8** for real-time traffic detection and applies **homography transformation** to generate a **bird’s-eye view**. The main goal is to **detect traffic**, **track them**, and **project their movement** onto a **top-down view** of the road, distinguishing between those in a **bike lane** and a **non-bike lane**. It also **counts** how many unique vehicles passed through each lane.

---

## 🎯 Project Objectives

1. Detect **people/vehicles** in each frame of a traffic video.
2. Identify whether they are in the **bike lane** or **non-bike lane**.
3. Track them across multiple frames using a simple **Centroid Tracker**.
4. Apply **homography** to map real-world road areas from angled camera footage to a flat **bird’s-eye view**.
5. Generate two output videos:

   * Original video with bounding boxes and counts.
   * Bird’s-eye view showing positions of traffic in both lanes.

---

## 🧠 Project Methodology (Step-by-Step)

### 1. **Set Up Parameters and Input/Output**

* The input is a folder of video frames (`FRAME_DIR`).
* The output is a folder where annotated videos will be saved (`OUTPUT_DIR`).
* We also define:

  * Two polygons in the image representing the **bike lane** and **non-bike lane**.
  * Real-world sizes for each lane, which are used to calculate homography transformations.

---

### 2. **Load YOLOv8 Person,Bikes and Car Detection Model**

* We use the `yolov8l.pt` pretrained model from **Ultralytics** to detect **persons (class 0)(in case motorbike is not easy to be detected like in bird eye or rear view) and  motorbikes (class 2) ** in each frame.
* Bounding boxes are extracted around detected persons/vehicles, and their **centroids** are calculated to track them.

---

### 3. **Apply Homography Transformation**

Homography lets us **transform angled views into a flat, top-down (bird’s-eye) perspective**.

* Using 3 pairs of matching points on the image and in the real world:

  * One set (`H2`) maps the **bike lane** to its top-down equivalent.
  * Another set (`H1`) maps the **non-bike lane**.
* This transformation helps us **project centroids** into the bird's-eye view correctly.

---

### 4. **Track Detected People**

* A custom **KalmanCentroidTracker** tracks person/vehicles across frames using their centroid positions.
* It maintains:

  * `objects`: current tracked centroids.
  * `disappeared`: how many frames a person/vehicle has been missing.
  * `framesSeen`: how many frames a person/vehicle has been seen.
* People/vehicles are **registered** when they appear and **deregistered** when they disappear for too long.

---

### 5. **Draw Output Frames**

* In each frame:

  * **Draw bounding boxes** around detected people/vehicle in original view.
  * **Draw circles** at transformed positions on bird’s-eye view.
  * **Count** unique people/vehicle for bike and non-bike lanes (after a few frames to avoid duplicates).
  * Show counts on the original frame using `cv2.putText()`.

---

### 6. **Save the Videos**

* Two videos are written using `cv2.VideoWriter()`:

  1. `orig.mp4` — Annotated original video with counts and boxes.
  2. `bird.mp4` — Bird’s-eye view with positions of all people/vehicles shown as colored dots.

---

## 🔧 Folder Structure

```
📁 TrafficHomographyProject/
│       
├── /results   ← Folder where output videos will be saved
├── /code/Traffic_homography.pynb            ← code
└── README.md               
```

---

## 📈 Output Example

| View Type  | Description                                                         |
| ---------- | ------------------------------------------------------------------- |
| `orig.mp4` | Original video with persons/vehicles tracked and lane-based counts displayed |
| `bird.mp4` | Top-down map showing real-world positions of persons in each lane   |

---

## 📌 Key Concepts Explained

| Concept              | Description                                                                               |
| -------------------- | ----------------------------------------------------------------------------------------- |
| **YOLOv8**           | Fast object detection model used to detect people.                                        |
| **Homography**       | Mathematical transformation to "flatten" angled images into top-down views.               |
| **Centroid Tracker** | Simple tracker to assign consistent IDs to people/vehicles across frames.                          |
| **Polygon Check**    | Ensures detected people/vehicles are inside the defined lanes using OpenCV’s `pointPolygonTest()`. |
| **Bird’s-eye View**  | Visualization that helps understand real-world spatial distribution.                      |

---

## 📦 Dependencies

* Python 3.7+
* OpenCV
* NumPy
* Ultralytics (YOLOv8)
* SciPy

Install via:

```bash
pip install opencv-python numpy ultralytics scipy
```

---

## 🚀 How to Run

1. Place your input video frames in `input_frames/`.
2. Run the script (`python your_code.py`).
3. Check `output_frames_folder/` for the result videos: `orig.mp4` and `bird.mp4`.

---

## 🙋‍♂️ Why Use Homography in Traffic Projects?

Homography helps convert traffic footage taken from an angle into **measurable, real-world coordinates**. This is extremely helpful for:

* **Accurate lane-based analytics**
* **Collision detection**
* **Traffic flow monitoring**
* **Violation detection**

---

