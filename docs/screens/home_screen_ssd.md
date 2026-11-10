# Screen Specification Document (SSD): Home Screen

**Target Screen:** `home_screen.html`  
**File Path:** [`docs/screens/home_screen.html`](file:///c:/Users/niko4/OneDrive/Documentos/Work/ICIS/Proyects/Simulador/docs/screens/home_screen.html)  
**System Context:** Protection & Control Single Line Diagram (P&C) Simulator  
**Architecture:** Electron (Desktop UI) + Node.js (IPC & Main Process) + Python 3.11 (PyPower / SPICE Calculation Kernel) + PostgreSQL (Persistent Storage)

---

## 1. Screen Overview & Metadata

* **Screen Label:** `home_screen`
* **Window Type:** Main Application Dashboard (Frameless/Custom Titlebar Desktop Window)
* **Preceding Screen:** `splash_screen` (triggered post-initialization)
* **Purpose:** Serves as the central mission control for electrical protection engineers to manage projects, launch specialized single-line editors and calculation toolkits, monitor real-time system logs, and inspect database connectivity.

---

## 2. Page Segments Architecture

The layout consists of six distinct structural segments designed for desktop workflows:

```
+-------------------------------------------------------------------------------------------------+
| Segment 1 & 2: Electron Custom Titlebar & Top Menu Bar (File, Edit, View, Toolkits...)           |
+-------------------------------------------------------------------------------------------------+
| Segment 3: Quick Action Toolbar (New, Open, DB Sync, SPICE, TCC Curves, Search, User/License)   |
+-----------------------+-------------------------------------------------+-----------------------+
| Segment 4:            | Segment 5:                                      | Segment 6:            |
| Left Sidebar          | Center Workspace View                           | Right Sidebar         |
| (Navigation &         | - Hero Welcome Banner                           | (Activity Log History |
| Designer Toolkits)    | - Recent Projects Grid & Category Filters       | & System Diagnostics) |
|                       | - Engineering Templates (IEEE 9-Bus, P&C, Gen)  |                       |
+-----------------------+-------------------------------------------------+-----------------------+
| Segment 7: Bottom System Status Bar (DB status, Python Kernel, Memory, Auto-Save)                |
+-------------------------------------------------------------------------------------------------+
```

### Segment Breakdown:
1. **Titlebar & Main Menu:** Native-style desktop window management with application drop-down menus (`File`, `Edit`, `View`, `Toolkits`, `Simulation`, `Relay Libraries`, `Window`, `Help`).
2. **Top Action Toolbar:** Primary operational shortcuts: project creation, file import, fast toolkit activation, and global search filter.
3. **Left Sidebar (Designer Toolkits):** Navigation hub linking to dedicated engineering modules (SLD Editor, ANSI 50/51 Relay Coordination, Short Circuit Engine, Load Flow, CT/PT Device Libraries).
4. **Center Workspace:** Primary content pane hosting recent `.sld` and `.cct` files, status tags (`DRC: OK`, `ANSI 51 Coordinated`), and one-click starter templates.
5. **Right Sidebar (Log History & Diagnostics):** Real-time event log viewer documenting backend processes (PostgreSQL queries, SPICE convergence, auto-saves).
6. **Bottom Status Bar:** Global footer displaying engine connectivity, active project name, and system health metrics.

---

## 3. Data Models & Classes (TypeScript / Domain Definitions)

```typescript
// ==========================================
// 1. Project & File Management
// ==========================================
export interface ProjectFile {
  id: string;
  name: string;
  filePath: string;
  extension: '.sld' | '.cct';
  voltageLevel: string;          // e.g., "115kV", "13.8kV"
  description: string;
  lastSaved: Date;
  drcStatus: 'PASSED' | 'WARNING' | 'ERROR';
  protectionStatus?: 'COORDINATED' | 'UNVERIFIED' | 'MISMATCH';
  previewSvgData?: string;
  starred: boolean;
  category: 'substation' | 'distribution' | 'industrial' | 'generation';
}

// ==========================================
// 2. Logging & System Events
// ==========================================
export type LogCategory = 'PostgreSQL' | 'Engine' | 'FileIO' | 'Library' | 'System';
export type LogLevel = 'INFO' | 'WARN' | 'ERROR' | 'DEBUG';

export interface LogEntry {
  id: string;
  timestamp: string;             // ISO format or HH:mm:ss
  category: LogCategory;
  level: LogLevel;
  message: string;
  details?: Record<string, unknown>;
}

// ==========================================
// 3. System & Hardware State
// ==========================================
export interface SystemHealthState {
  isDbConnected: boolean;
  dbHost: string;                // e.g., "localhost:5432"
  dbName: string;                // e.g., "protection_db"
  kernelStatus: 'ONLINE' | 'OFFLINE' | 'BUSY';
  kernelVersion: string;         // e.g., "Python 3.11 (PyPower/SPICE)"
  memoryUsageMb: number;
  autoSaveEnabled: boolean;
  activeLicense: 'Community' | 'Professional' | 'Enterprise';
}

// ==========================================
// 4. Engineering Templates
// ==========================================
export interface EngineeringTemplate {
  id: string;
  code: string;                  // e.g., "IEEE", "P&C", "GEN"
  title: string;
  description: string;
  presetTopologyPath: string;
  busCount: number;
}
```

---

## 4. Business Logic

1. **Project Loading & Verification:**
   * When opening a `.sld` or `.cct` file, the Electron main process passes the file path to the Node.js/Python kernel.
   * The file's single-line schema is parsed, cross-referenced with PostgreSQL for device definitions (CT ratios, breaker ratings, relay curves), and checked against IEEE/IEC design rules (DRC).
2. **PostgreSQL Synchronization:**
   * Persistent storage holds ANSI relay curve catalogs (SEL, ABB, Schneider, GE), breaker interruption ratings, and equipment parameters.
   * The home screen polls the connection pool on initialization to display live database status.
3. **Calculation Kernel Handshake (NodeJS <-> Python):**
   * High-performance load flow and short-circuit algorithms run in Python worker subprocesses managed via IPC.
   * The home screen verifies that the Python environment and dependencies (`numpy`, `scipy`, `pypower`) are ready.
4. **Auto-Save & Version Tracking:**
   * An internal timer checks for dirty state flags on active projects, auto-saving recovery checkpoints every 5 minutes and pushing an entry to the Log History.

---

## 5. Lifecycle Hooks & Events Specification

### 5.1 Lifecycle Hooks
* **`onInit`:**
  * `FetchRecentProjects()`: Reads recent projects from local config cache and PostgreSQL.
  * `CheckSystemHealth()`: Verifies Python kernel and PostgreSQL daemon reachability.
  * `SubscribeToLogStream()`: Subscribes to Node.js IPC log dispatcher for real-time log rendering.
  * `LoadTemplates()`: Preloads starter circuit presets.
* **`onDestroy`:**
  * `UnsubscribeLogStream()`: Cleans up listeners to prevent memory leaks.
  * `FlushPendingTelemetry()`: Ensures recent project access timestamps are persisted.

---

### 5.2 Interactive Inputs & Event Handlers

| UI Element / Selector | Event Trigger | Handler Function | Business / UI Action |
| :--- | :--- | :--- | :--- |
| **Window Minimize Button (`−`)** | `click` | `handleWindowMinimize()` | Sends IPC message `window:minimize` to Electron main process. |
| **Window Maximize Button (`□`)** | `click` | `handleWindowMaximize()` | Sends IPC message `window:toggleMaximize` to Electron main process. |
| **Window Close Button (`✕`)** | `click` | `handleWindowClose()` | Prompts save confirmation if background tasks run, then exits. |
| **Top Menu Items (`File`, `Edit`, etc.)** | `click` | `handleOpenNativeMenu(menuId)` | Spawns Electron native application popup menu at anchor coordinates. |
| **"New SLD Diagram" Button** | `click` | `handleCreateNewProject()` | Opens New Project modal dialog; navigates to `sld_editor_screen`. |
| **"Open File (.sld, .cct)" Button** | `click` | `handleBrowseProjectFile()` | Triggers native `dialog.showOpenDialog()`; validates extension and loads workspace. |
| **Quick Action: DB Sync Icon** | `click` | `handleSyncDatabase()` | Manually pings PostgreSQL; re-synchronizes custom relay library models. |
| **Quick Action: SPICE Engine Icon** | `click` | `handleLaunchSpiceConsole()` | Opens SPICE simulation panel / workspace configuration. |
| **Quick Action: TCC Curves Icon** | `click` | `handleOpenTCCPlotter()` | Launches ANSI 50/51 Time-Current Characteristic curve viewer. |
| **Global Search Bar** | `input` (debounced 200ms) | `handleSearchQuery(query)` | Filters recent projects list and templates by name, bus, or relay tag. |
| **Sidebar Toolkit: SLD Editor** | `click` | `navigateTo('sld_editor')` | Transitions Electron view to Single Line Diagram graphic canvas. |
| **Sidebar Toolkit: Relay Coordination** | `click` | `navigateTo('relay_coord')`| Transitions Electron view to ANSI 50/51/87 protection coordination tool. |
| **Sidebar Toolkit: Short Circuit Engine**| `click` | `navigateTo('short_circuit')`| Transitions Electron view to Fault Calculation & Analysis engine. |
| **Sidebar Toolkit: Load Flow** | `click` | `navigateTo('load_flow')` | Transitions Electron view to Newton-Raphson Load Flow dashboard. |
| **Sidebar Toolkit: Device Library** | `click` | `navigateTo('device_lib')` | Opens CT/PT, Transformer, and Breaker catalogue manager. |
| **Recent Project Category Tabs** | `click` | `handleFilterCategory(cat)` | Filters recent files by `All`, `Substations`, `Distribution`, or `Starred`. |
| **Project Card** | `click` / `dblclick` | `handleOpenProject(projectId)`| Reads project JSON/XML, verifies dependencies, transitions to SLD editor. |
| **Template Card (e.g. IEEE 9-Bus)** | `click` | `handleInstantiateTemplate(id)`| Clones template into a new working project and opens editor. |
| **Log History: "Clear" Button** | `click` | `handleClearLogs()` | Clears current in-memory log display buffer. |

---

## 6. Error Handling & Edge Cases

1. **Database Disconnected:**
   * If PostgreSQL is down on launch, the status indicator changes from green to red (`● DB Disconnected`). Recent projects load from offline local JSON cache, and a warning log is recorded.
2. **Python Calculation Kernel Failure:**
   * If Python 3.11 or required numerical packages are missing, the status displays `Kernel Offline`, and simulation action buttons are disabled with tooltip instructions.
3. **Corrupted Project File:**
   * If opening a `.sld` file fails JSON/XML schema validation, an error toast is surfaced, and the exception is logged to the Log History panel without crashing the application.
