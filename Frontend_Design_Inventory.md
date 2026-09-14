# Frontend UI/UX Design Inventory: JOCKY Framework

## 1. Product Overview
* **Product Name:** JOCKY Framework (Central Management Web Interface)
* **Product Purpose:** A centralized dashboard for cybersecurity analysts to write custom forensic scripts in the JOCKY DSL, compile them into polymorphic payloads to bypass EDRs, and view covertly exfiltrated forensic data from target systems.
* **Target Users:** NTRO Cybersecurity Analysts, Digital Forensic Incident Response (DFIR) Teams.
* **Main User Problems:** Need to safely deploy forensic scripts without triggering AV/EDR. Need a centralized place to manage targets and view data.
* **Core Workflows:** Authentication, Script creation & compilation, Target monitoring, Forensic data analysis.
* **Primary User Journeys:** Write Script -> Compile & Download Payload -> View exfiltrated results in Dashboard.
* **Authentication:** NextAuth.js (JWT) with strict Role-Based Access Control (RBAC).
* **Data Users Provide:** JOCKY DSL source code, Target OS selections, Authentication credentials.
* **Data Users Receive:** Downloadable `.exe` payloads, Forensic JSON data (Processes, Network Connections, Filesystem artifacts).
* **Important Business Rules:** System uses dark mode, terminal-inspired aesthetics.

## 2. User Roles
1. **Analyst (Primary):** Can write scripts, compile payloads, and view target data.
2. **Admin (Implied):** Can manage analyst accounts, view system health, and audit logs.

## 3. Major User Flows

| Flow ID | User Goal | Starting Point | Steps | Screens Involved | Final Result |
| ------- | --------- | -------------- | ----- | ---------------- | ------------ |
| FLOW-01 | Login | Landing / Login Page | 1. Enter credentials<br>2. Submit<br>3. Validate<br>4. Redirect to Dashboard | Login, Dashboard | User authenticated |
| FLOW-02 | Create & Compile Script | Dashboard | 1. Navigate to Editor<br>2. Write JOCKY DSL code<br>3. Click Compile<br>4. Wait for build<br>5. Download Payload | Editor | Payload downloaded |
| FLOW-03 | View Target Analysis | Dashboard | 1. View online targets<br>2. Click on Target<br>3. View process/network lists<br>4. Filter/Sort data | Dashboard, Target Analysis | Forensic data analyzed |
| FLOW-04 | User Logout (Implied) | Any Screen | 1. Click Profile<br>2. Click Logout<br>3. Redirect to Login | All, Login | User logged out |

## 4. Complete Screen/Page Inventory

| Screen ID | Screen/Page Name | Route | User Role | Purpose | Access Condition | Source | Priority |
| --------- | ---------------- | ----- | --------- | ------- | ---------------- | ------ | -------- |
| SCR-01 | Login | `/login` | All | Authenticate users | Unauthenticated | IMPLIED | P0 |
| SCR-02 | Dashboard (Home) | `/` | Analyst, Admin | Overview of targets, extractions, health | Authenticated | EXPLICIT | P0 |
| SCR-03 | Script Editor | `/editor` | Analyst, Admin | IDE for writing and compiling JOCKY scripts | Authenticated | EXPLICIT | P0 |
| SCR-04 | Target Analysis | `/targets/[id]` | Analyst, Admin | Deep-dive into specific target's data | Authenticated | EXPLICIT | P0 |
| SCR-05 | Settings / Profile | `/settings` | Analyst, Admin | Manage user account / tokens | Authenticated | IMPLIED | P2 |
| SCR-06 | 404 / Error | `/*` | All | Handle invalid routes/errors | None | IMPLIED | P1 |

## 5. Screen-by-Screen Section Breakdown

| Screen ID | Section ID | Section Name | Purpose | Components Inside | Source |
| --------- | ---------- | ------------ | ------- | ----------------- | ------ |
| SCR-01 | 01-A | Login Form | Capture credentials | Inputs, Submit Button, Logo | IMPLIED |
| SCR-02 | 02-A | Global Navbar | App navigation | Links, Avatar, Logout | EXPLICIT |
| SCR-02 | 02-B | System Health | Display API status | Status indicators, Uptime | EXPLICIT |
| SCR-02 | 02-C | Online Targets Grid | List connected machines | Cards, Status Badges | EXPLICIT |
| SCR-02 | 02-D | Recent Extractions | Feed of incoming data | List items, Timestamps | EXPLICIT |
| SCR-03 | 03-A | Editor Toolbar | Actions for script | Compile button, Target OS dropdown | EXPLICIT |
| SCR-03 | 03-B | Monaco Editor | Code input area | Text editor component, Line numbers | EXPLICIT |
| SCR-03 | 03-C | Build Console | Show compile logs/errors | Terminal-like text area | IMPLIED |
| SCR-04 | 04-A | Target Header | Machine details | Hostname, IP, OS, Status | EXPLICIT |
| SCR-04 | 04-B | Data Tabs | Switch data views | Tabs (Processes, Network, Files) | EXPLICIT |
| SCR-04 | 04-C | Data Tables | Display forensic JSON | Table, Filters, Search bar | EXPLICIT |

## 6. Complete Component Inventory

| Component ID | Component Name | Type | Screen(s) | Purpose | Interactive? | Source | Reusable? |
| ------------ | -------------- | ---- | --------- | ------- | ------------ | ------ | --------- |
| CMP-01 | Primary Button | Button | All | Main actions (Compile, Login) | Yes | IMPLIED | Yes |
| CMP-02 | Code Editor | Editor | SCR-03 | Write JOCKY DSL | Yes | EXPLICIT | No |
| CMP-03 | Target Card | Card | SCR-02 | Display target summary | Yes | EXPLICIT | Yes |
| CMP-04 | Status Badge | Badge | SCR-02, SCR-04 | Show Online/Offline/Building | No | IMPLIED | Yes |
| CMP-05 | Data Table | Table | SCR-04 | Show processes/network | Yes | EXPLICIT | Yes |
| CMP-06 | Console Output | Text | SCR-03 | Show compilation logs | No | IMPLIED | No |
| CMP-07 | Navbar | Nav | All Auth | Global navigation | Yes | IMPLIED | Yes |

## 7. Complete Button Inventory

| Button ID | Button Text/Label | Screen | Location | Action | Destination/Result | Type | State Variations | Source |
| --------- | ----------------- | ------ | -------- | ------ | ------------------ | ---- | ---------------- | ------ |
| BTN-01 | "Login" | SCR-01 | Form | Submit credentials | Redirect to Dashboard | Primary | Default, Loading, Disabled | IMPLIED |
| BTN-02 | "Compile & Deploy" | SCR-03 | Toolbar | POST `/api/build` | Triggers build, shows console | Primary | Default, Compiling, Success, Error | EXPLICIT |
| BTN-03 | "Download Payload" | SCR-03 | Console | Trigger download | Downloads `.exe` | Secondary | Hidden (until build success), Default | EXPLICIT |
| BTN-04 | "View Details" | SCR-02 | Target Card | Navigate | SCR-04 (Target Analysis) | Tertiary | Default, Hover | IMPLIED |
| BTN-05 | "Logout" | All Auth | Navbar | End session | Redirect to Login | Icon/Text | Default, Hover | IMPLIED |

## 8. Form & Input Inventory

| Field ID | Field | Type | Required? | Placeholder | Validation | Error State | Default | Source |
| -------- | ----- | ---- | --------- | ----------- | ---------- | ----------- | ------- | ------ |
| FLD-01 | Username | Text | Yes | "user@ntro.gov.in" | Valid email | Red border, text | None | IMPLIED |
| FLD-02 | Password | Password | Yes | "••••••••" | Min 8 chars | Red border, text | None | IMPLIED |
| FLD-03 | OS Select | Dropdown | Yes | "Select OS" | In list (Windows/Ubuntu) | Red outline | Windows | EXPLICIT |
| FLD-04 | Search Data | Text | No | "Search processes..." | None | None | None | IMPLIED |

## 9. Interaction Inventory

| Interaction ID | Element | Trigger | User Action | System Response | UI Change | Destination | Source |
| -------------- | ------- | ------- | ----------- | --------------- | --------- | ----------- | ------ |
| INT-01 | Code Editor | Typing | Enter code | Syntax highlighting updates | Code gets colored | N/A | EXPLICIT |
| INT-02 | Compile Btn | Click | Submit code | Triggers API | Shows loading spinner | N/A | EXPLICIT |
| INT-03 | Data Tabs | Click | Switch view | Loads new dataset | Active tab styled | N/A | EXPLICIT |
| INT-04 | Target Row | Click | Select target | Routes to detail page | N/A | SCR-04 | EXPLICIT |

## 10. UI State Inventory

| Component | State | What User Sees | Available Actions |
| --------- | ----- | -------------- | ----------------- |
| Compile Button | Loading | Spinner, "Building..." text | None (disabled) |
| Compile Button | Success | Green icon, "Build Success" | Download Payload |
| Target Grid | Empty | "No targets connected yet." | Refresh |
| Target Grid | Populated | Cards with target hostnames | Click to view details |
| Data Table | Loading | Skeleton rows | None |
| Global App | Offline | Banner: "Lost connection to server" | Retry |

## 11. Modal/Popup/Overlay Inventory

| ID | Overlay Name | Trigger | Content | Actions | Closing Methods | State |
| -- | ------------ | ------- | ------- | ------- | --------------- | ----- |
| MOD-01 | Build Error Modal | Build fails | API error logs | "Close", "Retry" | Click outside, X, Close | Error |
| MOD-02 | Profile Dropdown | Click Avatar | Settings, Logout | "Profile", "Logout" | Click outside, Select | Default |
| MOD-03 | Notification Toast | Data received | "New forensics from [Target]"| "View" | Auto-dismiss, X | Success |

## 12. Navigation Inventory

| Navigation Element | Location | Items | Destination | User Role | Mobile Behavior |
| ------------------ | -------- | ----- | ----------- | --------- | --------------- |
| Global Navbar | Top | Dashboard, Editor, Settings | Respective routes | All Auth | Hamburger menu |
| Target Tabs | SCR-04 Header | Processes, Network, Files | Sub-views in SCR-04 | All Auth | Scrollable row |
| Breadcrumbs | Top below Nav | Home > Targets > [ID] | Respective routes | All Auth | Hide on mobile |

## 13. Data Display Inventory
* **Target Grid:** Cards displaying Hostname, IP, OS Logo, Online status (green/red dot), Last Seen timestamp. Clickable.
* **Extraction Feed:** List of recent payloads executed. Shows Script Name, Target, Timestamp, Status (Success/Fail).
* **Forensic Table:** Tabular data for Processes (PID, Name, Path, Memory), Network (Proto, Local Addr, Remote Addr, State, PID). Sortable by headers. Searchable.
* **Build Console:** Read-only monospaced text area mimicking a terminal, scrolling output.

## 14. Responsive Design Requirements

| Component | Desktop | Tablet | Mobile |
| --------- | ------- | ------ | ------ |
| Navbar | Full links + Avatar | Full links | Hamburger menu |
| Target Grid | 3-4 columns | 2 columns | 1 column |
| Monaco Editor | Split screen with console | Stacked vertically | Hidden (Editing not supported) |
| Data Tables | Full columns | Hide less important cols | Card-based layout instead of table |

## 15. Accessibility Requirements
* **Theme:** Terminal aesthetic (dark mode) must maintain WCAG AA contrast (e.g., bright green/white on pure black/dark gray).
* **Keyboard Navigation:** Code editor must trap focus appropriately. Tables must be navigable via Tab/Arrow keys.
* **Screen Readers:** Badges (Status dots) must have `aria-label="Online"`. Console output needs `aria-live="polite"` for build status updates.

## 16. Design System Inventory
### Foundations
* **Typography:** Monospace (e.g., Fira Code, JetBrains Mono) for editor and data; Sans-serif (e.g., Inter, Roboto) for UI.
* **Colors:** Dark Backgrounds (#0F172A), Cyber Green (#10B981) for success/accents, Danger Red (#EF4444) for alerts, Slate for borders.
* **Radius:** Sharp or slight rounding (2px-4px) to fit terminal aesthetic.
### Components
* Button, Card, StatusIndicator, DataTable, CodeEditor, TerminalWindow, ToastNotification.

## 17. Complete Component Tree
```text
JOCKY Framework
│
├── AuthLayout
│   └── LoginForm
│
├── MainLayout
│   ├── TopNavbar (Logo, Links, UserDropdown)
│   ├── ToastContainer
│   │
│   ├── Dashboard (SCR-02)
│   │   ├── SystemHealthBar
│   │   ├── TargetGrid -> TargetCard
│   │   └── ExtractionFeed -> FeedItem
│   │
│   ├── Editor (SCR-03)
│   │   ├── EditorToolbar (OS Select, CompileBtn)
│   │   ├── MonacoWrapper
│   │   └── TerminalConsole
│   │
│   └── TargetAnalysis (SCR-04)
│       ├── TargetHeader
│       ├── DataTabs
│       └── DataTable (Search, Filters, Pagination)
```

## 18. Design Reference/Search Keywords
### Dashboard & Editor
* "Dark mode cybersecurity dashboard UI"
* "Terminal-inspired SaaS dashboard"
* "Web-based IDE UI Next.js"
* "Threat hunting dashboard design"
* "Code editor panel UI"

### Target Analysis
* "Server monitoring drill-down UI"
* "Data table dark mode tailwind"
* "System processes list UI"

## 19. UI Pattern Mapping

| Component | Common UI Pattern | Search Term |
| --------- | ----------------- | ----------- |
| Editor + Console | Web IDE / Split Pane | CodeSandbox UI, Replit UI clone |
| Target Grid | Server/Node Management | Fleet management UI, AWS EC2 dashboard |
| Data Tables | Admin Data Grid | React data table dark theme |

## 20. Frontend Design Checklist
- [ ] Login Screen
- [ ] Global Navbar (Desktop & Mobile)
- [ ] Dashboard: System Health Bar
- [ ] Dashboard: Target Card
- [ ] Dashboard: Empty States (No targets)
- [ ] Editor: Toolbar & Dropdowns
- [ ] Editor: Monaco Theme Configuration
- [ ] Editor: Terminal Output Component
- [ ] Target Analysis: Header & Breadcrumbs
- [ ] Target Analysis: Tabs
- [ ] Target Analysis: Data Tables with Search/Sort
- [ ] Modals: Build Error
- [ ] Notifications: Toast Alerts
- [ ] UI States: Loading Spinners & Skeletons

## 21. Master Frontend Inventory

| ID | Category | Element | Screen | Purpose | Interaction | States | Responsive? | Source | Priority | Design Search Terms |
| -- | -------- | ------- | ------ | ------- | ----------- | ------ | ----------- | ------ | -------- | ------------------- |
| M-01 | Page | Login | SCR-01 | Auth | Form submit | Loading, Error | Yes | IMPLIED | P0 | "Dark mode login UI" |
| M-02 | Page | Dashboard | SCR-02 | Overview | Click cards | Empty, Loading | Yes | EXPLICIT | P0 | "Cybersecurity dashboard" |
| M-03 | Component| Monaco Editor | SCR-03 | Write code | Typing | Default | No | EXPLICIT | P0 | "Web IDE editor UI" |
| M-04 | Component| Terminal | SCR-03 | View logs | Scroll | Streaming | Yes | IMPLIED | P0 | "Terminal UI component" |
| M-05 | Data Disp| Target Table | SCR-04 | View JSON | Sort, Search | Empty, Populated| Yes | EXPLICIT | P0 | "Dark data table UI" |
| M-06 | Button | Compile Btn | SCR-03 | Trigger API | Click | Default, Loading| Yes | EXPLICIT | P0 | "Primary action button" |

## 22. PRD Gaps / Missing Frontend Requirements

| Missing Requirement | Why Needed | Impact | Recommendation |
| ------------------- | ---------- | ------ | -------------- |
| No Target Deletion | Old offline targets will clutter the dashboard over time. | UI Clutter | Add "Remove Target" button on SCR-04. |
| No Pagination details | The PRD mentions tables but doesn't specify if data is paginated or infinite scroll. | Performance | Implement pagination for Target Tables (IMPLIED). |
| Empty States Definition | What shows when there are no active networks/processes returned? | UX | Design specific empty states ("No active connections found"). |
| Payload Architecture select | PRD mentions x64 Windows/Ubuntu, but UI dropdown only specifies OS. | Functionality | Add an Architecture dropdown (x86/x64) in the Editor Toolbar. |

## 23. Final Frontend Scope Summary
The frontend scope encompasses a highly specialized, authenticated Next.js web application with three primary views: a Dashboard for fleet management, an integrated IDE (Monaco) with a simulated terminal output for compiling LLVM payloads, and a detailed data-grid view for analyzing exfiltrated JSON forensic data. The aesthetic requires a rigorous dark-mode, terminal-inspired design system with complex tabular data handling and real-time asynchronous state management for the compilation and C2 reporting workflows.

---

### FINAL QUESTION TO ANSWER
> **"If a UI/UX designer had to design the entire frontend from scratch based only on this inventory, would anything still be missing?"**

**YES.**
While the inventory is highly comprehensive, the following specific details would still be missing for a designer to achieve pixel-perfection without further clarification:
1. **Logo / Branding:** The PRD mentions a "terminal-inspired aesthetic," but there are no specific brand guidelines, logo assets, or exact color palettes provided. The designer will need to invent the brand identity for "JOCKY".
2. **Specific JSON Schema for Tables:** The PRD specifies "Processes, Network, Files", but the designer needs the exact column names (e.g., does "Files" mean File Path, Creation Date, Size, or Hashes?) to design accurate table columns and widths.
3. **Data Refresh Triggers:** It is unspecified if the Dashboard auto-polls the GitHub API for new results or relies on manual refresh. The designer needs to know whether to design a "Live Syncing" indicator or a "Refresh Data" button.
