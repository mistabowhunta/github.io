---
name: NasonNation // Command — Distributed Edge/GPU AI Security & Biometric Platform
tools: [Python, FastAPI, React, TypeScript, OpenCV, YOLOv8s, YuNet/SFace, Qwen2.5-VL, WebRTC, Docker, PostgreSQL]
image: /assets/images/nasonnation-command.png
description: A containerized, sub-second WebRTC surveillance and biometric threat-intelligence platform pairing continuous Edge CPU tracking (YOLOv8s + YuNet/SFace 128-D vectors) with asynchronous LAN GPU Vision-Language verification (RTX 3060 Ti).
permalink: /projects/nasonnation-command
---

# NasonNation // Command

A full-stack, self-hosted computer vision, biometric identification, and perimeter security platform architected to eliminate the false alarms, cloud latency, and privacy leaks of commercial NVR systems.

![NasonNation Command](/assets/images/nasonnation-command.png)

## System Architecture: Split Edge-CPU + LAN-GPU Pipeline

Running a 7-billion-parameter Vision-Language Model (VLM) 24/7 on every video frame wastes power, while lightweight edge models alone hallucinate on complex lighting and inanimate objects. **Command** solves this with a two-tier distributed architecture:

1. **Tier 1 — Always-On Edge Node (`bossman` — Debian Linux / Docker):**
   * **Sub-Second Video Fabric (`MediaMTX`):** Ingests multiple 2K RTSP camera streams over TCP and serves sub-500ms **WebRTC (`WHEP`)** streams to desktop and mobile viewports.
   * **2-Pass Kinematic Displacement Engine (`YOLOv8s` + `MOG2`):** Combines background subtraction with a custom 2-pass spatial tracker and **Parked Vehicle IoU Footprint Locking**, ensuring stationary vehicles and swaying foliage never trigger false alarms.
   * **Real-Time Biometric Identification (`YuNet` + `SFace`):** Aligns 5-point facial landmarks and extracts normalized **128-dimensional biometric embeddings** in `<3ms`, matching against PostgreSQL vector galleries via cosine similarity (`>= 0.363`).
2. **Tier 2 — On-Demand LAN GPU Co-Processor (`NVIDIA RTX 3060 Ti`):**
   * Whenever the Edge Node verifies a true moving target, it dispatches the high-resolution crop over the local gigabit LAN to an **RTX 3060 Ti** running **`Qwen2.5-VL:7b`**.
   * The VLM performs semantic verification—purging inanimate false positives, extracting vehicle paint/body types, reading **License Plates (ALPR)** and **Delivery Carrier logos** (`AMAZON`, `UPS`, `FEDEX`, `USPS`), and writing a tactical summary back to PostgreSQL.
  
![NasonNation Command Dashboard - Driveway Example](/assets/images/driveway_zoom.png)

---

## Key Engineering Highlights

* **Zero-PII Threat Intelligence & FBI/DOC Watchlist Daemon:** A background async worker paginates through the **FBI Wanted REST API** (Portland & West Coast field offices) and **Oregon DOC Most Wanted** feeds, computing 128-D SFace vectors purely in RAM (0 KB disk image retention) for instant watchlist alerting.

![NasonNation Command](/assets/images/watchlist.png)

* **Auto-Clustering "Repeat Visitor" Dossiers:** Unknown visitors with high-confidence facial landmarks (`>= 75%`) are automatically clustered into persistent visitor profiles (`VISITOR_01`, `VISITOR_02`) with sighting counters and timestamps.
* **OpSec-First OSINT Launchpad:** One-click reverse-face search integration (`PimEyes`, `Yandex`) with an automatic **Whitelist OpSec Lock** that disables external uploads for trusted family members and strips all EXIF metadata.
* **5-Second H.264 MP4 Instant Replays & 24h Thermal Heatmaps:** Rolling pre/post-trigger frame ring buffers encode 5-second H.264 clips for person and animal detections, paired with a live 16:9 SVG thermal dwell-time heatmap overlay.

![NasonNation Command](/assets/images/heat_signatures.png)

[View Code](https://github.com/mistabowhunta/nason-command)
