# 👤 Face Recognition System

[![Python](https://img.shields.io/badge/Python-3.7%2B-blue?style=flat-square&logo=python)](https://www.python.org/)
[![dlib](https://img.shields.io/badge/dlib-19.0%2B-yellow?style=flat-square)](http://dlib.net/)
[![OpenCV](https://img.shields.io/badge/OpenCV-4.0%2B-red?style=flat-square&logo=opencv)](https://opencv.org/)
[![NumPy](https://img.shields.io/badge/NumPy-Latest-013243?style=flat-square&logo=numpy)](https://numpy.org/)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Active-success?style=flat-square)](/))

> 👤 A **face recognition system** using deep learning for identifying and verifying faces. Uses dlib's pre-trained models to detect faces and compare facial features for accurate recognition.

### 🌟 Key Features
- 🎯 **Face Detection** - Accurate face localization using dlib
- 🔍 **Face Recognition** - Identify people using facial features
- 📊 **High Accuracy** - 99.38% accuracy on benchmarks
- ⚡ **Real-time Processing** - Process webcam feed in real-time
- 📁 **Image Management** - Support for image collections
- 🔐 **Face Encoding** - Convert faces to 128-D vectors
- 🎥 **Video Support** - Process video files

---

## 📋 Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Dataset Structure](#dataset-structure)
- [Usage Guide](#usage-guide)
- [Face Encoding](#face-encoding)
- [Demo & Output](#demo--output)
- [Advanced Features](#advanced-features)
- [Contributing](#contributing)

---

## 🎯 Overview

This project implements a complete face recognition pipeline using dlib and OpenCV. It can:

- **Detect** faces in images and video streams
- **Encode** faces as 128-dimensional feature vectors
- **Compare** faces to determine if they match
- **Recognize** faces against a database
- **Verify** identities with confidence scores

Perfect for:
- 🔐 Security systems
- 🚪 Access control
- 📱 Mobile authentication
- 📊 Attendance tracking
- 👥 Social media tagging
- 🔬 Research applications

---

## ✨ Features

### 1. Face Detection
Locates all faces in an image with bounding boxes.

### 2. Face Landmarks
Identifies 68 facial landmarks (eyes, nose, mouth, etc.).

### 3. Face Encoding
Converts face into a unique 128-D vector for comparison.

### 4. Face Comparison
Measures distance between face encodings to determine matches.

### 5. Confidence Scoring
Reports how confident the system is about a match.

### 6. Batch Processing
Process multiple images efficiently.

---

## 💻 Installation

### Prerequisites
- Python 3.7+
- CMake (for dlib compilation)
- 1GB free disk space
- Webcam (optional)

### Important: numpy < 2

⚠️ **NOTE**: This project requires numpy < 2.0
Numpy 2.0+ breaks dlib compatibility. Install correctly:

```bash
pip install 'numpy<2'
```

### Step 1: Clone Repository
```bash
git clone https://github.com/adammoos/Face_Recognition.git
cd Face_Recognition
```

### Step 2: Create Virtual Environment
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

### Step 3: Install Dependencies
```bash
# IMPORTANT: Install numpy first with version constraint
pip install 'numpy<2'

# Then install other dependencies
pip install opencv-python dlib face-recognition matplotlib
```

### Step 4: Download Face Models
```bash
# This happens automatically on first use, but you can pre-download:
python -c "import face_recognition; print('Models loaded')"
```

### Step 5: Verify Installation
```bash
python Face_recon.py --help
```

---

## ⚡ Quick Start

### 1. Prepare Your Dataset
```
known_faces/
├── person_1/
│   ├── image_1.jpg
│   ├── image_2.jpg
│   └── image_3.jpg
├── person_2/
│   ├── image_1.jpg
│   └── image_2.jpg
└── person_3/
    └── image_1.jpg
```

### 2. Update Image Path
```python
# Face_recon.py
known_faces_dir = 'path/to/your/known_faces'
unknown_faces_dir = 'path/to/unknown/images'
```

### 3. Run Recognition
```bash
python Face_recon.py
```

### 4. Expected Output
```
Processing known faces...
✓ person_1: 3 images encoded
✓ person_2: 2 images encoded
✓ person_3: 1 image encoded

Processing unknown faces...
✓ image_1.jpg → person_1 (0.92 confidence)
✓ image_2.jpg → person_2 (0.88 confidence)
✓ image_3.jpg → Unknown person

Complete!
```

---

## 📂 Dataset Structure

### Known Faces
```
known_faces/
└── {person_name}/
    ├── photo_1.jpg
    ├── photo_2.jpg
    └── photo_3.jpg
```

### Unknown Faces (For Recognition)
```
unknown_faces/
├── test_1.jpg
├── test_2.jpg
└── test_3.jpg
```

### Tips for Best Results
- 📸 Use high-quality images (400x400px minimum)
- 🎯 Front-facing photos work best
- 💡 Good lighting is essential
- 👥 3-5 images per person recommended
- 🚫 Avoid images with glasses/hats initially

---

## 📖 Usage Guide

### Basic Face Recognition
```python
import face_recognition
import cv2
from pathlib import Path

# Load image
image = face_recognition.load_image_file("person.jpg")

# Detect faces
face_locations = face_recognition.face_locations(image)
face_encodings = face_recognition.face_encodings(image, face_locations)

print(f"Found {len(face_locations)} face(s)")

# Face details
for (top, right, bottom, left), encoding in zip(face_locations, face_encodings):
    print(f"Face location: ({left}, {top}) to ({right}, {bottom})")
    print(f"Encoding dimensions: {len(encoding)}")
```

### Face Comparison
```python
# Load known face
known_image = face_recognition.load_image_file("known_person.jpg")
known_encoding = face_recognition.face_encodings(known_image)[0]

# Load test face
test_image = face_recognition.load_image_file("test_person.jpg")
test_encoding = face_recognition.face_encodings(test_image)[0]

# Compare faces
results = face_recognition.compare_faces([known_encoding], test_encoding)
distance = face_recognition.face_distance([known_encoding], test_encoding)

print(f"Match: {results[0]}")
print(f"Distance: {distance[0]:.4f}")  # Lower = more similar
```

### Batch Processing
```python
from pathlib import Path

known_people = {}

# Load all known faces
for person_dir in Path("known_faces").iterdir():
    if person_dir.is_dir():
        encodings = []
        for image_file in person_dir.glob("*.jpg"):
            image = face_recognition.load_image_file(image_file)
            face_encodings = face_recognition.face_encodings(image)
            if face_encodings:
                encodings.append(face_encodings[0])
        known_people[person_dir.name] = encodings

print(f"Loaded {sum(len(v) for v in known_people.values())} face encodings")
```

---

## 🧠 Face Encoding

### How It Works
```
Image Input
    ↓
[Face Detection]
    ↓
[Landmark Detection - 68 points]
    ↓
[Deep Neural Network]
    ↓
[128-D Face Encoding]
    ↓
[Comparison with Other Encodings]
```

### Technical Details
- **Model**: ResNet-based Deep Learning
- **Encoding Dimensions**: 128
- **Accuracy**: 99.38% on LFW benchmark
- **Processing Time**: ~0.1s per face

### Face Distance Interpretation
```
Distance < 0.6:   Same person (very confident)
0.6 - 0.7:        Likely same person
0.7 - 0.8:        Uncertain
Distance > 0.8:   Different people
```

---

## 🎬 Demo & Output

### Recognition Results
```
========================================
    FACE RECOGNITION SYSTEM
========================================

Known Faces Database:
├─ Alice: 5 photos encoded
├─ Bob: 4 photos encoded
├─ Charlie: 3 photos encoded
└─ Diana: 6 photos encoded

Test Results:
┌────────────────────────────────────────┐
│ Image: test_01.jpg                     │
├────────────────────────────────────────┤
│ Recognition: Alice                     │
│ Confidence: 0.94 (94%)                 │
│ Distance: 0.512                        │
│ Matches: ✓✓✓✓✓✓ (6/6 known photos)    │
└────────────────────────────────────────┘

┌────────────────────────────────────────┐
│ Image: test_02.jpg                     │
├────────────────────────────────────────┤
│ Recognition: Bob                       │
│ Confidence: 0.87 (87%)                 │
│ Distance: 0.608                        │
│ Matches: ✓✓✓✓ (4/4 known photos)      │
└────────────────────────────────────────┘

┌────────────────────────────────────────┐
│ Image: test_03.jpg                     │
├────────────────────────────────────────┤
│ Recognition: Unknown                   │
│ Reason: No confident match found       │
└────────────────────────────────────────┘

Summary:
✓ Processed: 3 images
✓ Recognized: 2 faces
✗ Unknown: 1 face
✓ Success Rate: 66.7%
```

---

## 🚀 Advanced Features

### 1. Webcam Recognition
```python
import cv2
import face_recognition

cap = cv2.VideoCapture(0)

# Load known faces
known_encodings = []
known_names = []

# ... load known faces ...

while True:
    ret, frame = cap.read()
    
    # Detect and recognize faces
    face_locations = face_recognition.face_locations(frame)
    face_encodings = face_recognition.face_encodings(frame, face_locations)
    
    for (top, right, bottom, left), encoding in zip(face_locations, face_encodings):
        matches = face_recognition.compare_faces(known_encodings, encoding)
        distances = face_recognition.face_distance(known_encodings, encoding)
        
        best_match_index = np.argmin(distances)
        if matches[best_match_index]:
            name = known_names[best_match_index]
        else:
            name = "Unknown"
        
        # Draw rectangle and name
        cv2.rectangle(frame, (left, top), (right, bottom), (0, 255, 0), 2)
        cv2.putText(frame, name, (left, top - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0, 255, 0), 2)
    
    cv2.imshow('Face Recognition', frame)
    
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

### 2. Face Landmarks
```python
# Get facial landmarks
landmarks = face_recognition.face_landmarks(image)

# Draw landmarks
for landmarks in face_recognition.face_landmarks(image):
    for landmark_name, landmark_points in landmarks.items():
        # landmark_name: 'chin', 'left_eyebrow', 'right_eyebrow', etc.
        for point in landmark_points:
            cv2.circle(image, point, 2, (0, 255, 0), 1)
```

### 3. Performance Optimization
```python
# Use smaller image for faster processing
small_image = cv2.resize(image, (0, 0), fx=0.25, fy=0.25)
face_locations = face_recognition.face_locations(small_image, model="hog")  # Faster

# Process every nth frame
if frame_count % 5 == 0:
    process_frame = True
```

---

## 🐛 Troubleshooting

### Problem: "numpy.ndarray' object is not callable"
```bash
# You likely have numpy >= 2.0
# MUST use numpy < 2.0
pip uninstall numpy
pip install 'numpy<2'
```

### Problem: dlib fails to compile
```bash
# Windows: Make sure you have Visual C++ Build Tools
# macOS: brew install cmake
# Linux: sudo apt-get install cmake

pip install dlib
```

### Problem: "No module named 'dlib'"
```bash
# Reinstall with verbose output
pip install --verbose dlib
```

### Problem: Low accuracy
```python
# Try multiple photos per person
# Ensure good lighting in photos
# Use front-facing images
# Adjust tolerance threshold
```

### Problem: Slow performance
```python
# Use smaller image size
# Process every nth frame
# Use GPU (CUDA) if available
# Use model="hog" for faster detection
```

---

## 🤝 Contributing

Contribution Ideas
- 🚀 GPU acceleration (CUDA)
- 📱 Mobile app integration
- 🎬 Video streaming support
- 📊 Database integration
- ⚡ Performance optimization
- 🌍 Support for other face recognition models

---

## 📚 Learning Resources

### Documentation
- [face_recognition library](https://github.com/ageitgey/face_recognition)
- [dlib Documentation](http://dlib.net/python/index.html)
- [OpenCV Tutorials](https://docs.opencv.org/)

### Related Techniques
- Face Detection with MTCNN
- Face Recognition with VGGFace
- ArcFace: Deep Facial Recognition

---

## 📞 Support

**Author:** Mohammed Adam Mejri  
**GitHub:** [adammoos](https://github.com/adammoos)  
**LinkedIn:** [Mohammed Adam Mejri](https://www.linkedin.com/in/mohammed-adam-mejri-259124404/)

---

## 📄 License

MIT License - See [LICENSE](LICENSE) for details

---

<div align="center">

### ⭐ If this project helped you, consider giving it a star!

**Made with ❤️ by [Mohammed Adam Mejri](https://github.com/adammoos)**

[⬆ Back to Top](#-face-recognition-system)

</div>
