# 📋 Zplit Interactive Study Checklist

> Step-by-step, hands-on learning roadmap for mastering Flutter and Zplit.  
> Check off items (`[x]`) as you progress, experiment with the code, and build your foundation for GSoC.

---

## 🚀 Phase 0: Setup & First Run

- [ ] **0.1 Check Flutter Installation:**
  - Run `flutter doctor` in terminal.
  - Ensure Dart, Flutter SDK, and Android toolchain have green checkmarks.
- [ ] **0.2 Setup Device / Emulator:**
  - Connect a physical Android phone with USB Debugging enabled (or start an Android Virtual Device).
  - Verify detection with `flutter devices`.
- [ ] **0.3 Fetch Dependencies & Run:**
  - Run `flutter pub get` in this repository.
  - Run `flutter run` on your device.
- [ ] **0.4 Interactive App Walkthrough:**
  - [ ] Complete the onboarding flow and generate your EVM wallet keys.
  - [ ] Look at the Home Screen tabs (Friends vs Groups).
  - [ ] Try creating a dummy group in the Groups tab.
  - [ ] Click the QR code button and test the scanner UI.
  - [ ] Click the notification bell and notice that nothing happens.

---

## 🎨 Phase 1: Messing with UI & Widgets

*Learn Flutter layouts by tweaking live code and observing Hot Reload (`r` in terminal).*

- [ ] **1.1 Inspect the Theme System:**
  - Open `lib/core/theme/app_theme.dart`.
  - Find the primary color (`#00C853`).
  - *Experiment:* Change it to a test color (e.g. `#2196F3` Blue), hit `r` (Hot Reload), and watch the whole app update! Revert it when done.
- [ ] **1.2 Mess with the Splash Screen:**
  - Open `lib/ui/onboarding/widgets/Splash_screen.dart`.
  - Notice `Scaffold`, `Center`, and `Column`.
  - *Experiment:* Change the subtitle text or font size and watch hot reload immediately reflect the change.
- [ ] **1.3 Break Down the Profile Screen:**
  - Open `lib/ui/users/widgets/profile_screen.dart`.
  - Identify where the user avatar, name text field, and wallet address chips are built.
  - *Experiment:* Add a new card or `ListTile` showing a fake "Reputation Score: 100%".
- [ ] **1.4 Understand the Balance Card Layout:**
  - Open `lib/ui/transaction/widgets/balance_card.dart`.
  - Learn how `Container`, `BoxDecoration`, `Border.all`, and `LinearGradient` combine to make cards look sleek.
- [ ] **1.5 Build the Missing Screen (Hands-on Mini Project):**
  - [ ] Create a new file `lib/ui/users/widgets/notification_screen.dart`.
  - [ ] Create a `NotificationScreen` widget returning a `Scaffold` with an `AppBar(title: Text('Activity'))`.
  - [ ] Add a `ListView` with 3 dummy `ListTile` cards ("Alice requested ₹200", "Bob accepted lunch split").
  - [ ] Register the route in `lib/routing/App_router.dart`.
  - [ ] In `lib/ui/users/widgets/home_Screen.dart` (around line 415), replace `onPressed: () {}` on the bell icon with `Navigator.pushNamed(context, '/notifications')`.
  - [ ] Tap the bell icon on your phone and see your new screen load!
- [ ] **1.6 Fix Empty-State & Onboarding Images (The Dark Mode White Box Bug):**
  - *The Problem:* `assets/images/homescreen.png` (empty expense state) and `onboarding-1/2/3.png` are low-resolution (252px) and have a baked-in solid `#F9FAFB` opaque background. In Dark Mode, Flutter draws them as an ugly white square box on the dark background.
  - *The Task:* Re-export the illustrations from `design/zplit-design.fig`:
    - Ensure the frame fill is set to transparent (uncheck "Show in exports" on fill).
    - Export at high resolution (`@2x`, `@3x` or vector SVG).
    - Replace the files in `assets/images/`.
  - *Verification:* Toggle Dark Mode in the app and verify the empty-state illustration seamlessly adapts to both dark and light backgrounds without any white box.


---

## ⚡ Phase 2: Messing with State Management (BLoC)

*Understand how data flows: UI Dispatches Event ➔ BLoC Processes ➔ BLoC Emits State ➔ UI Rebuilds.*

- [ ] **2.1 Read the Event & State Files:**
  - Open `lib/ui/balance/view_model/balance_event.dart` (Events: `LoadAllBalances`, `UpdateBalance`).
  - Open `lib/ui/balance/view_model/balance_state.dart` (States: `BalanceInitial`, `BalanceLoading`, `BalanceLoaded`, `BalanceError`).
- [ ] **2.2 Trace the BLoC Brain:**
  - Open `lib/ui/balance/view_model/balance_bloc.dart`.
  - See how `on<LoadAllBalances>` calls `_balanceRepository.getAllBalances()` and then calls `emit(BalanceLoaded(...))`.
- [ ] **2.3 Trace how the UI Listens to BLoC:**
  - Open `lib/ui/users/widgets/home_Screen.dart` (around line 430).
  - Find `BlocBuilder<BalanceBloc, BalanceState>`.
  - *Observation:* Notice that when the state is `BalanceLoaded`, it extracts `totalPaise` and passes it to `BalanceSummaryCard`.
- [ ] **2.4 Experiment with State Triggering:**
  - Add a temporary `FloatingActionButton` on `home_Screen.dart` that calls:
    ```dart
    context.read<BalanceBloc>().add(LoadAllBalances());
    ```
  - Put a `print()` statement inside `balance_bloc.dart` to see when it fires in the terminal.

---

## 💾 Phase 3: Database & Local Persistence (Drift / SQLite)

*Understand how Zplit stores data completely offline without an internet server.*

- [ ] **3.1 Inspect Table Schemas:**
  - Open `lib/core/database/tables/users_table.dart`.
  - Open `lib/core/database/tables/balances_table.dart`.
  - Open `lib/core/database/tables/transactions_table.dart`.
  - Open `lib/core/database/tables/groups_table.dart`.
- [ ] **3.2 Notice Schema Limitations for Groups:**
  - Look at `transactions_table.dart`: Notice there is only `fromUserPublicKey` and `toUserPublicKey`.
  - Look at `groups_table.dart`: Members are stored as a comma-separated string (`TextColumn get users`).
  - *Mental note:* Fixing this relational data model for groups is part of your GSoC goal!
- [ ] **3.3 Inspect Database DAOs:**
  - Open `lib/core/database/daos/balances_dao.dart`.
  - See how SQL queries are written in type-safe Dart (`select(balancesTable)..where(...)`).
- [ ] **3.4 Understand Code Generation:**
  - Notice files ending in `.g.dart` (e.g., `app_database.g.dart`).
  - Run the Drift code generator:
    ```bash
    dart run build_runner build --delete-conflicting-outputs
    ```
  - Verify that everything compiles cleanly.

---

## 🔐 Phase 4: Cryptography & Web3 Signatures

*Learn how Zplit verifies transactions without central server accounts.*

- [ ] **4.1 Identity Generation:**
  - Open `lib/core/services/crypto_service.dart`.
  - See how `EthPrivateKey.createRandom()` generates a private key, public key, and EVM address.
  - Notice how `FlutterSecureStorage` securely saves the private key on-device.
- [ ] **4.2 Transaction Signing & EIP-191:**
  - Read `CryptoService.signPayload()`.
  - Trace how transaction amounts and counterparty keys are hashed with `keccak256` and signed with the private key.
- [ ] **4.3 Verification via ecRecover:**
  - Read `CryptoService.verifySignature()`.
  - Understand how `ecRecover` extracts the public key from the signature to prove the sender actually signed it without revealing their private key.

---

## 🔄 Phase 5: Background Sync, Lifecycles & P2P Logic

*Study how offline transactions and asynchronous device-to-device synchronization work.*

- [ ] **5.1 App Lifecycle & Session Resumption:**
  - Open `lib/main.dart` (lines 68–75):
    ```dart
    SettlementTracker.resumeSessions().then((_) => SettlementTracker.checkAll());
    ```
  - *Question:* Why does Zplit need this?
  - *Answer:* On Android, when you click "Pay via Wallet", Android switches to MetaMask or Uniswap. Android may kill Zplit in the background to free RAM. When Zplit re-opens, `SettlementTracker` resumes checking whether that transaction confirmed on-chain!
- [ ] **5.2 Deep Dive into SettlementTracker:**
  - Open `lib/ui/settlement/widgets/settlement_tracker.dart`.
  - Study how pending transactions are stored in `FlutterSecureStorage` with status: `checking`, `confirmed`, or `failed`.
  - Study `checkAll()`: How it polls the EVM RPC node for USDC `Transfer` event logs.
- [ ] **5.3 Deep Links as Offline Sync Vectors:**
  - Open `lib/ui/deep_link/app_listener.dart` and `lib/ui/deep_link/view_model/deep_link_bloc.dart`.
  - Understand how opening a link (`zplit://tx?d=...`) wakes up the app, unpacks the base64 JSON payload, and routes it to `DeepLinkBloc`.
- [ ] **5.4 Transport Glare & Connection Collision Heuristics:**
  - Open `lib/core/services/wifi_direct_Service.dart` (read comments lines 1–40).
  - Understand the **scan-first, host-as-fallback heuristic**:
    - If two phones try to be the Wi-Fi Direct "Host" at the same time, neither connects.
    - Zplit adds random jitter (3–4s) so one device becomes the Host and the other joins as a Client.
- [ ] **5.5 Bluetooth BLE Transport & Bridge:**
  - Open `lib/ui/bluetooth/bluetooth_link_bridge.dart`.
  - See how incoming Bluetooth chunks are assembled into JSON, decoded, and dispatched to `DeepLinkBloc`.
- [ ] **5.6 Group Sync Challenge (GSoC Architectural Focus):**
  - *Understand the missing link:* In 1-on-1, device A sends directly to device B. But in a 5-person group, device A might only be near device B today, and device B sees device C tomorrow.
  - *Concept to explore:* Gossip protocol / anti-entropy synchronization (device B carries and relays A's signed expense to C).

---

## 🔨 Phase 6: The "Build-It" Phase (Hands-On Code Additions)

*Start modifying and adding real functionality.*

- [ ] **6.1 Fix the Custom Split Hardcoding:**
  - Open `lib/ui/transaction/widgets/expense.dart` (around line 214).
  - Notice that `case 'Custom': return -(totalAmount / 2);` is hardcoded.
  - Add a simple dialog or slider allowing the user to enter their exact share.
- [ ] **6.2 Tap-to-Open Group Details:**
  - In `lib/ui/users/widgets/home_Screen.dart` (line 659), find the group `ListTile`.
  - Add an `onTap:` callback to navigate to a group details view.
- [ ] **6.3 Clean Up Legacy Dead Folders:**
  - Verify that `lib/data/transactions/` and `lib/ui/transaction/view_models/` can be safely deleted once all references point to `view_model/`.
  - Remove their exclusions in `analysis_options.yaml` and run `flutter analyze`.

---

## 🏆 Phase 7: GSoC Proposal & Community Phase

- [ ] **7.1 Join Community Channels:**
  - Join the Stability Nexus Discord and AOSSIE Telegram (links in `maintainers.md`).
  - Introduce yourself in the `#introductions` or `#zplit` channels.
- [ ] **7.2 Submit Your First Small PR:**
  - Fix a small UI overflow, replace opaque empty-state images with high-res transparent assets, add the notification screen, or clean up lints.
  - Mentors will immediately notice your name and PR quality.
- [ ] **7.3 Draft Your GSoC Proposal (Feb–March):**
  - Title: *Multi-Party Group Splitting Engine & P2P Sync Protocol for Zplit*.
  - Include:
    - Relational schema redesign for multi-user splits.
    - Multi-party ECDSA cryptographic signature collection.
    - Debt simplification graph algorithm (Min-Cash-Flow).
    - Offline background relay / gossip sync model.

---

## 📌 STUFF TO DO (The Master Incomplete Backlog)

> Everything that is currently broken, stubbed, missing, or needs heavy refinement in Zplit.  
> This is your master checklist of contribution and GSoC project ideas.

### 🔴 1. The Core Group Engine (Missing & Stubbed)
- [ ] **Multi-Party Database Schema:**
  - Redesign `lib/core/database/tables/transactions_table.dart` to support groups (`groupId`).
  - Create a relational `group_expenses` table and `split_participants` table so expenses can split across $N$ members.
- [ ] **Group "Add Expense" UI Flow:**
  - Build UI to pick multiple friends/participants in an expense.
  - Implement **Equal Split** ($Total \div N$).
  - Implement **Exact Amount Split** (custom amounts per person with total sum validation).
  - Implement **Percentage Split** (percentage sliders summing to 100%).
  - Replace the hardcoded `case 'Custom': return -(total / 2);` stub in `expense.dart`.
- [ ] **Group Detail Screen (`/group-detail`):**
  - Make tapping a group on the Home Screen navigate to a full group dashboard.
  - Show group total spend, individual member net balances ("You owe Bob ₹300, Alice owes you ₹150"), and group expense feed.
- [ ] **Debt Simplification Engine (Min-Cash-Flow Algorithm):**
  - Implement a greedy/graph algorithm to minimize the number of transactions required to settle up within a group ($A \to B \to C \implies A \to C$).
- [ ] **"Record as Cash / Settle Outside App" Button:**
  - Add a normal settlement button to record payments made via Cash, UPI, Venmo, etc., without requiring Ethereum/USDC wallet transactions.

### 🟡 2. P2P Transport & Multi-Peer Synchronization
- [ ] **Multi-Peer Gossip / Relay Sync:**
  - Design how a group transaction syncs across members who aren't in the same room at the same time (e.g., Alice $\to$ Bob via Bluetooth, Bob $\to$ Charlie tomorrow).
- [ ] **P2P Transport Audit & Hardening:**
  - Test and fix the fragile BLE, Wi-Fi Direct, and NFC channels on real physical Android devices (handle socket timeouts, host collision glare, and disconnects gracefully).
  - Make QR Code & Deep Linking the primary, rock-solid fallback on all platforms.
- [ ] **Cross-Platform / iOS Parity:**
  - Note: Wi-Fi Direct and NFC HCE are Android-only. Add graceful fallbacks or Multipeer Connectivity for iOS devices so they aren't completely blocked from offline sync.

### 🟢 3. UI/UX Polish & Visual Bugs
- [ ] **Fix Dark Mode Empty State & Onboarding Images:**
  - Re-export `homescreen.png` and `onboarding-1/2/3.png` from `design/zplit-design.fig` with transparent backgrounds (alpha = 0) and high resolution (`@2x`, `@3x` or vector SVG) to remove the ugly white box in Dark Mode.
- [ ] **Fix Android App Icon:**
  - Run `dart run flutter_launcher_icons` so Android stops showing the default blue Flutter icon and uses the green Zplit logo.
- [ ] **Activity / Notification Feed Screen:**
  - Build a real notification & transaction log screen and wire it up to the dead notification bell on the Home screen.

### 🧹 4. Codebase Cleanup & Tech Debt
- [ ] **Delete Duplicate Legacy Folders:**
  - Safely remove `lib/data/transactions/` and `lib/ui/transaction/view_models/` (which are leftovers from earlier milestone PRs).
  - Remove their exclusions in `analysis_options.yaml` so `flutter analyze` runs cleanly across the entire codebase.
- [ ] **Add End-to-End Tests:**
  - Write widget and integration tests for the expense adding, split calculation, and settlement flows.

