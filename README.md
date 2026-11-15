<!-- Top Anchor -->
<div name="readme-top"></div>

<!-- Organization & Project Logo -->
<div align="center" style="display: flex; align-items: center; justify-content: center; gap: 20px;">
  <img alt="Stability Nexus" src="assets/stability.svg" width="160">
  <img alt="Zplit Logo" src="assets/images/zplit-logo.png" width="160">
</div>

&nbsp;

<div align="center">

[![Static Badge](https://img.shields.io/badge/Stability_Nexus-Zplit-228B22?style=for-the-badge&labelColor=FFC517)](https://stability.nexus/)
[![Branch](https://img.shields.io/badge/branch-playground-blueviolet?style=for-the-badge)](#-playground-branch-notice)
[![Flutter](https://img.shields.io/badge/Flutter-3.47.5-02569B?style=for-the-badge&logo=flutter&logoColor=white)](https://flutter.dev)

</div>

<!-- Social Links -->
<p align="center">
  <a href="https://t.me/StabilityNexus"><img src="https://img.shields.io/badge/Telegram-24A1DE?style=flat&logo=telegram&logoColor=white" alt="Telegram Badge"/></a>&nbsp;&nbsp;
  <a href="https://x.com/StabilityNexus"><img src="https://img.shields.io/badge/X-000000?style=flat&logo=x&logoColor=white" alt="X Badge"/></a>&nbsp;&nbsp;
  <a href="https://discord.gg/YzDKeEfWtS"><img src="https://img.shields.io/discord/995968619034984528?style=flat&logo=discord&logoColor=white&label=Discord&labelColor=5865F2&color=57F287" alt="Discord Badge"/></a>&nbsp;&nbsp;
  <a href="https://news.stability.nexus/"><img src="https://img.shields.io/badge/Medium-12100E?style=flat&logo=medium&logoColor=white" alt="Medium Badge"/></a>&nbsp;&nbsp;
  <a href="https://linkedin.com/company/stability-nexus"><img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=LinkedIn&logoColor=white" alt="LinkedIn Badge"/></a>
</p>

---

> [!WARNING]
> ### ⚠️ PLAYGROUND BRANCH NOTICE
> You are viewing the **`playground`** branch.
> 
> * **Purpose:** This branch is an experimental study, reverse-engineering, and prototyping workspace maintained by [@vishnunandan555](https://github.com/vishnunandan555) preparing for Google Summer of Code (GSoC).
> * **Upstream Tracking:** The `main` and `dev` branches remain identical to upstream [StabilityNexus/Zplit](https://github.com/StabilityNexus/Zplit). All upstream PRs should be based on clean feature branches cut from `dev`.
> * **Documentation included on this branch:**
>   * 🧭 [STUDY.md](STUDY.md) — Step-by-step 8-phase codebase study guide, architecture breakdown, and full GSoC task checklist.
>   * 🛠️ [SETUP.md](SETUP.md) — Verified local environment setup guide (Flutter 3.47.5, Java 17 Temurin, Android SDK 34/35).
>   * 📐 [zplit.md](zplit.md) — The original architectural proposal and decentralized vision specification.

---

# 🧩 Zplit

**Zplit** is an open-source, offline-first mobile application designed to simplify group expense tracking and debt settlement through a **decentralized, privacy-focused architecture**.

Traditional expense-sharing apps (like Splitwise) rely on centralized cloud servers that harvest transaction history and lock features behind paywalls. Zplit operates **without mandatory internet access or cloud servers**, enabling peer-to-peer data synchronization over **Wi-Fi Direct, Bluetooth Low Energy (BLE), NFC, and QR codes**.

---

## 📊 Current Project Status: ~50–55% Complete

The initial GSoC foundation established the single-user experience and bilateral friend tracking. Multi-user group coordination, debt graph simplification, and autonomous P2P synchronization are yet to be built.

### ✅ What is Working
* **Onboarding & User Profile:** Local identity creation, dark/light theme switching, and local state management.
* **1-on-1 Expense Tracking:** Bilateral expense entry between two friends, settlement logging, and balance tracking.
* **Encrypted Local Persistence:** [Drift](https://drift.simonbinder.eu/) (SQLite) with SQLCipher support and secure storage.
* **On-Device Receipt OCR:** Google ML Kit Text Recognition to parse totals and line items from camera images.
* **P2P Transport Foundations:** Integration stubs for `flutter_p2p_connection` (Wi-Fi Direct) and BLE proximity.

---

## 🚧 What is Incomplete / Left to Do (GSoC Roadmap)

These core pillars represent the primary scope for upcoming GSoC contributors:

| Area | Status | Description |
| :--- | :---: | :--- |
| **Multi-User Group Splitting** | 🔴 Incomplete | The Drift database and UI currently only support 1-to-1 friend balances. Real multi-member groups (`groups`, `group_members`, and group-scoped expenses) need schema design, split types (equal, percentage, shares, exact), and state management. |
| **Debt Simplification Algorithm** | 🔴 Incomplete | Implementation of the **Minimum Cash Flow algorithm** (using greedy graph algorithms or Ford-Fulkerson) to minimize total transactions across group participants. |
| **Robust Multi-Peer P2P Sync** | 🔴 Incomplete | CRDT (Conflict-free Replicated Data Types) or Vector Clocks to resolve conflicting edits between offline peers when connecting via Wi-Fi Direct mesh or BLE. |
| **Group Dashboard & Analytics** | 🔴 Incomplete | Rich visual charts (`fl_chart`), per-member spending breakdowns, category summaries, and PDF/CSV export. |
| **Offline Sync Queue** | 🟡 Incomplete | Durable queue with exponential backoff and Android WorkManager / iOS BackgroundTasks integration. |
| **Decentralized Settlement Layer** | ⚪ Planned | Optional Web3 stablecoin / micropayment settlement (e.g. ERC-20 / Layer-2) for settling debts without bank rails. |

For the full detailed breakdown and technical roadmap, read [STUDY.md](STUDY.md).

---

## 🛠️ Tech Stack

* **Framework:** [Flutter](https://flutter.dev) (v3.47.5 / Dart 3.7.0)
* **Architecture:** Layered Feature-First + Repository Pattern
* **State Management:** [BLoC / Cubit](https://bloclibrary.dev) (`flutter_bloc`)
* **Local Database:** [Drift](https://drift.simonbinder.eu/) (Type-safe SQLite with SQLCipher encryption)
* **Computer Vision:** Google ML Kit Text Recognition (`google_mlkit_text_recognition`)
* **P2P Communication:** `flutter_p2p_connection` (Wi-Fi Direct), Nearby Connections / BLE, QR
* **Charts & Visualizations:** `fl_chart`

---

## 🚀 Getting Started

### Prerequisites

| Component | Required Version |
| :--- | :--- |
| **Flutter SDK** | `3.47.5` (channel stable) |
| **Dart SDK** | `3.7.0` |
| **JDK** | Java 17 (Eclipse Temurin recommended via SDKMAN) |
| **Android SDK** | API Level 34 & 35 |

> [!TIP]
> For a tested step-by-step walkthrough covering SDKMAN, Gradle, and Android SDK configuration, see the [SETUP.md](SETUP.md) guide.

### Quick Start

1. **Clone the repository:**
   ```bash
   git clone -b playground https://github.com/vishnunandan555/Zplit.git
   cd Zplit
   ```

2. **Install Flutter packages:**
   ```bash
   flutter pub get
   ```

3. **Verify analyzer passes cleanly:**
   ```bash
   flutter analyze
   ```

4. **Run the test suite:**
   ```bash
   flutter test
   ```

5. **Launch on an emulator or connected device:**
   ```bash
   flutter run
   ```

---

## 🤝 Contributing to Zplit

1. Review open issues on the upstream repository: [StabilityNexus/Zplit](https://github.com/StabilityNexus/Zplit/issues).
2. Read [STUDY.md](STUDY.md) to understand the system architecture and current backlog.
3. Cut a new feature branch from `dev` (e.g. `feature/group-splitting`).
4. Ensure all changes adhere to clean architecture, pass `flutter analyze`, and include unit/bloc tests.
5. Submit a descriptive Pull Request targeting upstream's `dev` branch.

---

<div align="center">
  <sub>Maintained under the <a href="https://stability.nexus/">Stability Nexus</a> umbrella. Licensed under Apache 2.0.</sub>
</div>
