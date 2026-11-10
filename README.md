# ICIS Power Systems Protection & Control Simulator

Desktop application designed for engineering, modeling, and simulating electrical protection and control Single Line Diagrams (SLD).

---

## ⚡ Overview

**ICIS Simulator** is an advanced electrical CAD and protection coordination tool built for electrical engineers, relay technicians, and substation automation specialists. It provides an intuitive canvas to assemble single-line diagrams, configure protective relay parameters (ANSI 50/51/87/21), map instrument transformers (CT/VT), and validate operational coordination and trip sequences.

---

## 🛠️ Technology Stack

* **Frontend**: Electron, HTML5, TailwindCSS, Vector CAD SVG Engine.
* **Backend**: Node.js & Python (analytical computation and power systems calculation engine).
* **Database**: PostgreSQL (relational catalog for calibrated protective relays and electrical equipment).

---

## 🚀 Key Features

* **Technical Engineering CAD Canvas**:
  * Multi-format sheet boundaries: **ANSI** (A, B, C, D) and **ISO** (A1, A2, A3, A4).
  * Direct pan and smooth zoom controls (25% to 400%) with mouse wheel shortcuts (`Ctrl + Wheel` for vertical scroll, `Shift + Wheel` for horizontal scroll, plain wheel for zooming).
  * Center-oriented scaling with grid alignment.

* **Calibrated Device Catalog**:
  * Rich equipment library:
    * **Protection Relays**: Overcurrent & Directional (ANSI 50/51/67), Differential (ANSI 87B/87T), Distance (ANSI 21).
    * **Circuit Breakers**: Vacuum & SF6 circuit breakers (ANSI 52).
    * **Instrument Transformers**: Bushing CTs, indoor/outdoor VTs with standard burden ratings.
    * **Power Transformers**: Two-winding and three-winding substation units.
  * Prevention of duplicate components in the active palette.
  * Real-time validation and locking of library items already inserted.

* **Interactive Drag & Drop with Numbered Terminals**:
  * Drag items from the sidebar onto the engineering workspace.
  * Floating translucent preview (`opacity-70`) displaying the exact CAD schematic symbol and connection terminals.
  * Numbered terminals according to standard electrical engineering conventions:
    * **Breakers (ANSI 52)**: Terminals `1` and `2`.
    * **Current Transformers (CT)**: Primary `P1`, `P2` and secondary `S1`, `S2`.
    * **Voltage Transformers (VT)**: Primary `H1`, `H2` and secondary `X1`, `X2`.
    * **Power Transformers (XFMR)**: High voltage `H1`, `H2` and low voltage `X1`, `X2`.
    * **Relays (ANSI 50/51/87)**: Bornes `1`, `2`, `3`, `4`.

* **Workspace Component Mobility**:
  * Position CAD elements anywhere across the sheet boundary.
  * Drag-to-reposition components with active selection ring highlights.
  * Coordinates automatically adjusted for canvas pan and zoom scale factor.

* **Multi-Project Tab Management**:
  * Browser-style tab bar supporting multiple open diagrams (`.pcd`).
  * Dirty state indicators (`●`) tracking unsaved modifications.
  * Close confirmation dialog protecting unsaved work.

---

## 📁 Repository Structure

```text
├── docs/
│   ├── requeriments.md            # Functional & technical requirements
│   └── screens/
│       ├── home_screen.html       # Primary workspace CAD interface
│       ├── home_screen_ssd.md     # System Sequence Diagrams & specs
│       ├── modal_device_library.html # Standalone device modal mockup
│       ├── new_project_modal.html # Project creation dialog mockup
│       └── splash_screen.html     # Application startup splash screen
├── mod/                           # Active modification tasks
├── mod_done/                      # Completed & archived modification tasks
│   ├── 01_project_nature_types_tooltip.md
│   ├── 02_browser_tabs_workspace_view.md
│   ├── 03_cleanup_kernel_db_and_title_color.md
│   ├── 04_empty_file_tab_scroll_and_save_modal.md
│   ├── 05_canvas_sheet_zoom_controls_and_sidebar_tools.md
│   ├── 06_canvas_scroll_shortcuts_transparent_sheet_and_device_grid.md
│   ├── 07_sidebar_device_list_empty_init_and_tooltip_fix.md
│   └── 08_drag_and_drop_cad_symbols_and_duplicate_prevention.md
├── .gitignore                     # Git ignore configuration
└── README.md                      # Project documentation
```

---

## 💻 Getting Started

### Prerequisites

* [Node.js](https://nodejs.org/) (v18+)
* [Python](https://www.python.org/) (v3.10+)
* [PostgreSQL](https://www.postgresql.org/) (v14+)

### Previewing the Screens

Open any of the screen prototypes directly in a modern web browser:
* [`docs/screens/home_screen.html`](docs/screens/home_screen.html) - Main interactive CAD simulator.
* [`docs/screens/splash_screen.html`](docs/screens/splash_screen.html) - Application loader.

---

## 📄 License

Internal development project for **ICIS Protection & Control Systems**. All rights reserved.
