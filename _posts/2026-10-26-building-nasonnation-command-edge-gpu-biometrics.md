---
title: "Engineering NasonNation // Command: Hybrid Edge/GPU Computer Vision, Kinematic Tracking & Biometric Threat Intel"
tags: [Computer Vision, Robotics, Python, FastAPI, React, YOLOv8, Biometrics, Docker]
style: fill
color: success
description: How I built a zero-cloud WebRTC security and biometric threat-intelligence platform using YOLOv8s, YuNet/SFace 128-D vectors, and an RTX 3060 Ti VLM co-processor.
---

Most DIY computer vision security projects look great in a 30-second demo and fail miserably in production. The moment the sun goes down, infrared (IR) grain kicks in, wind blows the trees, and naive detectors start spamming your phone every 12 seconds because your wife's parked white SUV looks like a "moving bus."

Here is how I engineered **NasonNation // Command**—a containerized, multi-stream surveillance and biometric identification platform running on a Debian Linux edge server (`bossman`) paired over LAN with an **NVIDIA RTX 3060 Ti**.

---

## 1. Solving the "Parked Subaru-Bus" & Track Teleportation Problem

Running `YOLOv8s` on individual frames tells you *what* is in the driveway, not whether it actually moved. Early iterations suffered from three classic failure modes:
1. **Infrared Bounding-Box Jitter:** At dawn, IR night-vision grain causes bounding boxes on parked cars to expand and contract by 3–4%, tricking simple centroid trackers into flagging parked vehicles as moving.
2. **Track Teleportation:** When a fast-moving car drives past on the street and exits the frame on the exact tick YOLO re-detects a parked vehicle in the driveway, standard greedy trackers link the orphaned street track onto the parked SUV.
3. **Waist-vs-Feet 2D Perspective Geometry:** Testing a person's bounding-box center `(cx, cy)` against a ground-level tripwire polygon misses targets completely when their waist passes *above* the polygon in 2D camera space while their shoes walk right through it.

### The Fix: 2-Pass Kinematic Displacement + IoU Footprint Locking

In `cv_pipeline.py`, I replaced standard centroid matching with a three-stage filter:
* **`ParkedVehicleRegistry` (2D IoU Lock):** Any vehicle bounding box that remains at `IoU >= 0.68` across 3+ sightings is locked into a persistent parked footprint mask. Parked footprints are stripped before track association so street traffic can never jump onto a parked car.
* **Box-Area Ratio Gate & Net Travel Threshold:** Moving tracks enforce a `<= 2.1x` bounding-box area ratio between frames and require both **active pixel motion inside the box (`>= 25%`)** and **net multi-frame spatial travel (`>= 8.0%` of screen width)**.
* **5-Anchor Polygon Ray-Casting:** Perimeter tripwires test 5 anchor points on every target—waist centroid, bottom-center feet `(x + 0.5w, y + 0.95h)`, left foot, right foot, and lower torso—catching perimeter breaches the instant a shoe touches the line.

---

## 2. Sub-Millisecond Biometrics: YuNet + SFace + FBI Watchlist Ingestion

Instead of relying on cloud facial recognition, **Command** runs two OpenCV Zoo ONNX models locally:
* **YuNet (`FaceDetectorYN`):** Locks onto 5 facial landmarks (eyes, nose tip, mouth corners) and dynamically upscales distant person crops via bicubic interpolation.
* **SFace (`FaceRecognizerSF`):** Warps and aligns the face crop (`alignCrop`) and extracts a **128-dimensional L2-normalized vector**.

### Three-Layer Identity Architecture
1. **Whitelisted Family (`role = whitelist`):** Suppresses red perimeter breach alarms and mobile push notifications when family members walk across the driveway.
2. **Auto-Clustering Repeat Visitors (`VISITOR_01`, `VISITOR_02`):** Unrecognized visitors with clean facial landmarks (`det_score >= 0.75`) are automatically clustered into PostgreSQL with live sighting counters and timestamps.
3. **Zero-Disk FBI & State Fugitive Watchlist Daemon:** A background async worker paginates through the **FBI Wanted REST API** (Portland, Seattle, SF, LA, and national pages) and **Oregon DOC Most Wanted**, computing 128-D SFace vectors purely in RAM (`sightings = 0`, 0 KB disk image usage). Every person detected on camera is dot-product matched against the watchlist in `<0.2ms`.

---

## 3. Asynchronous RTX 3060 Ti VLM Verification & ALPR

Whenever the edge node logs a verified moving target (and records a **5-second H.264 MP4 instant replay** from its rolling frame ring buffer), it dispatches the high-res crop to an **NVIDIA RTX 3060 Ti** running **`Qwen2.5-VL:7b`** via Ollama:
* Purges inanimate false alarms (garbage bins, shadows).
* Performs **Driveway ALPR (License Plate OCR)** and **Delivery Carrier Recognition** (`AMAZON`, `UPS`, `FEDEX`, `USPS`).
* Pushes instant mobile photo alerts via `ntfy.sh` for perimeter breaches, watchlist matches, and package deliveries.
