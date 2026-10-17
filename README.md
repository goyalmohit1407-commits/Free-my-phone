# ⚡ FreeMyPhone — Zero-Cloud Direct Wireless Storage Offloader

[![Live Web App](https://img.shields.io/badge/Live%20App-freemyphone.nexurastack.com-blue?style=flat-square&logo=google-chrome)](https://freemyphone.nexurastack.com)
[![Web APIs](https://img.shields.io/badge/APIs-File%20System%20Access%20%7C%20Wake%20Lock%20%7C%20SSE-orange?style=flat-square)](#-key-engineering-highlights)
[![Privacy](https://img.shields.io/badge/Architecture-Zero--Cloud%20Ephemeral%20Buffer-brightgreen?style=flat-square)](#-zero-cloud-security--data-lifecycle)
[![Client Transcoding](https://img.shields.io/badge/Client--Side-HEIC%20Transcoding%20%2B%20EXIF-purple?style=flat-square)](#-client-side-media-pipeline)

## 📌 Executive Summary
**FreeMyPhone** is a high-speed, zero-cloud web utility engineered to offload massive batches of photos and 4K videos directly from mobile devices (iOS & Android) to desktop workstations (Windows, macOS, Linux) without cables, native app installations, or cloud storage intermediaries.

By leveraging cutting-edge browser capabilities—such as the **File System Access API**, **Server-Sent Events (SSE)**, and **client-side Web Workers**—FreeMyPhone writes files straight to the user's local disk, auto-organizes them into timeline folders, and purges all staging buffers the instant a transfer finishes.

---

## 🏗️ System & Dataflow Architecture

┌─────────────────────────────────┐                                        ┌──────────────────────────────────┐
│     Mobile Browser (Sender)     │                                        │     Desktop Browser (Receiver)   │
│       [Safari / Chrome]         │                                        │        [Chrome / Edge]           │
├─────────────────────────────────┤                                        ├──────────────────────────────────┤
│ • QR-Code Session Pairing       │                                        │ • File System Access API Hook    │
│ • Client-Side HEIC Transcoding  │                                        │ • EXIF Timeline Directory Engine │
│ • Keep-Awake Engine (Wake Lock) │                                        │ • Zero-Popup Native Disk Writer  │
└────────────────┬────────────────┘                                        └────────────────▲─────────────────┘
│                                                                          │
│ Multi-Part Stream                                     SSE Event / Stream │
▼                                                                          │
┌───────────────────────────────────────────────────────────────────────────────────────────┴─────────────────┐
│                             PHP Real-Time Relay & Buffer Engine (Apache/Nginx)                              │
│  ├── Dynamic QR Session Handshake & State Management                                                        │
│  ├── Server-Sent Events (SSE) Signaling Pipeline                                                            │
│  └── Ephemeral Staging Buffer ───► [ Auto-Purge Trigger: Instant Wipe on Delivery Complete ]               │
└─────────────────────────────────────────────────────────────────────────────────────────────────────────────┘


---

## 🚀 Key Features

* **Zero-Cloud & Ephemeral Memory:** No third-party servers store user media. Files stream through a temporary buffer and are immediately wiped from server memory upon delivery verification.
* **100% Web-Native (Zero App Installs):** Operates entirely inside modern mobile and desktop browsers via instant dynamic QR code pairing.
* **Direct-to-Folder Disk Writing:** Utilizes the Chromium **File System Access API** to write files straight into chosen local directories—eliminating annoying multi-file download popups and huge `.zip` archive extractions.
* **Client-Side HEIC to JPEG Transcoding:** Automatically converts Apple HEIC photos into widely compatible JPEGs directly in the client browser before disk write, offloading server compute.
* **EXIF Metadata Timeline Sorting:** Extracts photo timestamp metadata (`exif-js`) on the fly, automatically generating structured `YYYY/MM/` subdirectories on the host PC.
* **Reverse Beam:** Bidirectional capability allowing desktop users to send documents, photos, or files back to mobile browsers seamlessly.
* **Screen Keep-Awake Engine:** Employs the Screen Wake Lock API and audio fallback hacks to prevent mobile devices from sleeping during large batch transfers (500+ files / 4K videos).
* **Universal Cross-Platform:** Frictionless transfer across Windows, macOS, Linux, Android, and iOS.

---

## 🛠️ Tech Stack & Dependencies

| Layer | Technologies & Libraries |
| :--- | :--- |
| **Frontend Core** | Vanilla JavaScript (ES6+), HTML5, Tailwind CSS |
| **Mobile Pairing** | `qrcodejs` (Real-time dynamic session QR generation) |
| **Client-Side Media**| `heic2any` (Client-side Apple image transcoding) |
| **Metadata Engine** | `exif-js` (Zero-latency EXIF header parsing) |
| **Archive Fallback** | `jszip` (Browser-level batch bundling for non-Chromium clients) |
| **Backend & Relay** | PHP 7.4+ (Server-Sent Events event relay, chunk handling, ephemeral memory buffer) |
| **Web Server** | Nginx / Apache HTTP Server |

---

## ⚙️ Key Engineering Highlights

### 1. Bypassing Download Throttles with File System Access API
Standard browser file downloads trigger security popups or force multi-gigabyte files into compressed `.zip` blobs that consume double disk space. FreeMyPhone leverages native directory file handles:
* The user grants folder permissions once on their desktop.
* The application streams byte chunks directly into writeable streams (`FileSystemWritableFileStream`), writing files bit-for-bit to disk as they arrive.

### 2. Zero-Load Client-Side Transcoding
Instead of consuming expensive server CPU cores for image manipulation:
* Heavy format conversion (HEIC $\rightarrow$ JPEG) is delegated to the client browser via WebAssembly/Web Workers (`heic2any`).
* The server acts purely as an ultra-fast I/O pipe, maintaining predictable sub-millisecond throughput regardless of file volume.

### 3. Asynchronous Real-Time Signaling via SSE
Pairing and transfer progress updates operate via lightweight **Server-Sent Events (SSE)** rather than heavy polling cycles or heavyweight socket clusters, ensuring maximum reliability across unstable mobile cellular or local Wi-Fi connections.

---

## 🛡️ Zero-Cloud Security & Data Lifecycle
1. **Volatile Staging:** Any file passing through the relay server lives in a temporary, randomized hash directory.
2. **Immediate Purge:** Upon acknowledgment of stream completion by the receiving client, an automated disk-level purge hook (`unlink`) wipes the file immediately.
3. **Session Expiry:** Stale sessions and uncompleted transfers are garbage-collected and destroyed automatically after 10 minutes of inactivity.

---
> **Notice on Proprietary Code:**  
> *The core server relay pipelines, automated buffer purge triggers, and production distribution assets are maintained under proprietary license. Technical architecture reviews and live code walkthroughs are available upon interview request.*

## 🌐 Live Application
* **Web App:** [freemyphone.nexurastack.com](https://freemyphone.nexurastack.com)

---

> **Notice on Proprietary Code:**
