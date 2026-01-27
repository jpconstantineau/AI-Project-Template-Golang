---
description: 'Instructions for writing CLI and TUI in Go code using Bubble Tea framework.'
applyTo: '**/*.go,**/go.mod,**/go.sum'
---

# User Interface for Command Line Interface (CLI) Applications

- for CLI applications, prefer using `github.com/urfave/cli/v3` when commands and subcommands are needed
- for simple CLI applications without subcommands, the standard `flag` package is sufficient

# User Interface for Terminal User Interface (TUI) Applications

- for TUI applications, use `github.com/charmbracelet/bubbletea` for building interactive terminal UIs
- for styling terminal output, use `github.com/charmbracelet/lipgloss` to create visually appealing layouts and styles
- for handling terminal input and keybindings, use `github.com/charmbracelet/bubbles` which provides reusable components for Bubble Tea applications
- for charts in bubbletea applications, use `github.com/NimbleMarkets/ntcharts` to create dynamic and interactive terminal charts

## Bubble Tea UI Rules

- Bubble Tea code must follow `model -> update -> view` cleanly.
- No DB calls inside `View()`.
- Prefer message-driven updates; long operations should use commands (`tea.Cmd`).
- UI should interact with app/services interfaces, not raw repositories when possible.
- Bubble Tea programs are comprised of a model that describes the application state and three simple methods on that model: 
  - Init, a function that returns an initial command for the application to run.
  - Update, a function that handles incoming events and updates the model accordingly.
  - View, a function that renders the UI based on the data in the model.
- In the Bubble Tea framework, the approach for managing "sub-models" (nested components or views) is to embed them within a main model and delegate their Update and View methods. This allows complex terminal user interfaces (TUIs) to be built with clean, modular code following the Elm architecture.
- No coupling of UI code to domain code. A nice test: Can you delete the UI package entirely and keep the domain + services working? If yes, you’re not coupled.


## UI Component Instructions for Bubble Tea TUI Layout
Below is a UI component “kit” for a Bubble Tea TUI application UI, with interfaces that *expand* into drill down views. I’m describing components (panels/widgets/screens), what they show, and *when/why* they appear.

The goal is to create a coherent, discoverable, and functional UI that supports the application.

###  Persistent “chrome” (always visible)

These are the stable pieces that make the application user interface consistent.

#### A. Top status bar (always)
**Purpose:** At-a-glance state and critical information.
**Contents (typical):**
- Current date/time + Application mode or status (e.g., Paused/Running)
- Active systems summary, metrics, statuses 
- Alerts counter (e.g., 3 critical, 7 info)
**Used when:** Always. This is the “instrument panel.”

#### B. Navigation strip (always)
**Purpose:** Fast switching between major views.
**Implementation:** Tabs or hotkeys (e.g., `1-9`, `g` to open a “Go To” list). Should be on 2 lines. First line is top-level views (Run, Setup, New, Load, Save, Quit) or sub-views (Dashboard, Entities, etc.)  Second line is the breadcrumb that update based on the first line selection.
**Used when:** Always.

#### C. Main workspace (always)
**Purpose:** Where the selected view renders (dashboard/Entities List/etc.).
**Used when:** Always.

#### D. Right-side inspector drawer (contextual, but frequently visible)
**Purpose:** Details for the currently selected entity (job, order, project, etc.).
**Used when:** When you select a row/node on any screen. Collapsible with `i`.

#### E. Bottom event log + notifications (always)
**Purpose:** Explain “why numbers moved” and provide narrative.
- Rolling log
- Click/enter on an event to open details + suggested actions
**Used when:** Always; 

#### F. Help/controls footer (always)
**Purpose:** Discoverability in a TUI.
- Shows available keys for the current view (Bubble Tea `help` style)
**Used when:** Always (toggle with `?`).

### Contextual Views / Modals / Screens (appear as needed)

These views appear based on user actions or application state.
#### G. Entity Detail View (contextual)
**Purpose:** Show detailed information about a selected entity.
**Trigger:** Selecting an entity from a list or graph.
**Contents:**
- Comprehensive details (attributes, status, history)
- Action buttons (edit, delete, etc.)
**Used when:** User selects an entity.
#### H. Settings Modal (contextual)
**Purpose:** Configure application settings.
**Trigger:** User presses `s` or selects "Settings" from a menu.
**Contents:**
- Various configuration options (toggles, dropdowns, input fields)
- Save/Cancel buttons
**Used when:** User wants to change settings.
#### I. Confirmation Dialog (contextual)
**Purpose:** Confirm critical actions.
**Trigger:** User initiates a potentially destructive action (delete, exit without saving).
**Contents:**
- Warning message
- Confirm/Cancel buttons
**Used when:** User performs a critical action.
#### J. Help Overlay (contextual)
**Purpose:** Provide detailed help and documentation.
**Trigger:** User presses `?` or selects "Help" from a menu.
**Contents:**
- Comprehensive help text
- Navigation links to different help topics
**Used when:** User requests help.
#### K. Search Modal (contextual)
**Purpose:** Search for entities or information within the application.
**Trigger:** User presses `/` or selects "Search" from a menu.
**Contents:**
- Search input field
- Search results list
**Used when:** User wants to find something quickly.
#### L. Notification Pop-up (contextual)
**Purpose:** Alert the user to important events or updates.
**Trigger:** Application generates a notification (e.g., task completed, error occurred).
**Contents:**
- Notification message
- Dismiss button
**Used when:** Important events occur.
#### M. Wizard/Step-by-Step Modal (contextual)
**Purpose:** Guide the user through multi-step processes.
**Trigger:** User initiates a process that requires multiple steps (e.g., setup, onboarding).
**Contents:**
- Step indicators
- Navigation buttons (Next, Back, Finish)
**Used when:** User starts a multi-step process.
#### N. Data Visualization View (contextual)
**Purpose:** Display charts, graphs, or other visual data representations.
**Trigger:** User selects a data visualization option from a menu or dashboard.
**Contents:**
- Various charts/graphs
- Filters and customization options
**Used when:** User wants to analyze data visually.
#### O. Error Modal (contextual)
**Purpose:** Inform the user of errors or issues.
**Trigger:** Application encounters an error that requires user attention.
**Contents:**
- Error message
- Suggested actions or solutions
**Used when:** An error occurs that needs to be communicated to the user.
### General UI/UX Guidelines
- Consistent keybindings across views (e.g., `q` to quit, `h` for help).
- Clear visual hierarchy using lipgloss styles (colors, borders, spacing).  
- Responsive layout that adapts to different terminal sizes.
- Accessibility considerations (e.g., color contrast, keyboard navigation).
- Regularly test the UI with real users to gather feedback and improve usability.