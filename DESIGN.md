# DESIGN.md - JOCKY Framework Frontend

## 1. Brand Direction
* **Product personality:** Surgical, forensic, stealthy, authoritative, unembellished.
* **Visual character:** Monochromatic density with high-contrast data visualization. Industrial and precise rather than friendly or sleek. It should feel like a specialized internal government tool, not a commercial SaaS.
* **Emotional tone:** Confidence, control, and deep system visibility.
* **Design principles:** Function over form; density over padding; raw data over abstract illustrations; immediate time-to-value.

## 2. Color System
* **Primary:** `Cyber Green` (#10B981) — Used sparingly for critical active states, successes, and primary actions.
* **Secondary:** `Alert Amber` (#F59E0B) — For warnings or mid-level alerts.
* **Background:** `Deep Void` (#0A0A0B) — Almost pure black, minimizing glare during long forensic sessions.
* **Surface:** `Carbon Panel` (#121214) — For cards, modals, and tables.
* **Border:** `Milled Steel` (#27272A) — Very subtle, crisp separators. No glowing borders.
* **Text:** `Bone White` (#E4E4E7) — High contrast body.
* **Muted Text:** `Ash Gray` (#A1A1AA) — For metadata and secondary info.
* **Danger:** `Critical Red` (#EF4444) — For destructive actions or critical system alerts.

*(Note: We explicitly avoid the generic purple/black startup aesthetic. The palette relies on stark, neutral grays punctuated by highly semantic system colors.)*

## 3. Typography
* **Display / Headings:** `JetBrains Mono` or `Fira Code`. Monospaced headings immediately establish this as an engineering/forensic tool.
* **Body Type:** `IBM Plex Sans`. Highly legible, industrial, and sharp for dense data grids. Avoids the "soft" feel of Inter or Geist.
* **Labels / Metadata:** `IBM Plex Mono` (small, tracking-wide, uppercase) for table headers, tags, and system labels.

## 4. Spacing
* Utilize a rigid 4px base scale (4, 8, 12, 16, 24, 32, 48).
* **Strategy:** High-density. Avoid massive 96px or 128px whitespace gaps typical of consumer landing pages. Forensic analysts need maximum data density on screen without feeling cluttered.

## 5. Radius
* **Sharp System:** 0px (Square) or a maximum of 2px for inputs/buttons. 
* JOCKY is a technical tool. Large, soft rounded corners (e.g., 8px - 16px) are strictly forbidden to maintain the industrial, precision aesthetic.

## 6. Shadows
* **Elevation Strategy:** Flat. Do not use drop shadows to indicate elevation.
* Elevation is communicated exclusively through surface color changes (e.g., `#0A0A0B` to `#121214`) and 1px solid borders (`#27272A`). 

## 7. Motion
* **Transition Behavior:** Snappy, instantaneous, or ultra-fast (75ms - 150ms max). 
* **Entrance Animation:** None or a simple rigid slide. No bouncy springs, no floating elements, no liquid glass transitions.
* Motion must exclusively communicate state changes (e.g., a fast color transition on hover, or a strict spinner for compilation loading).

## 8. Anti-Vibecode Rules Enforced
- ❌ No harsh gradients (flat colors only).
- ❌ No pure white backgrounds (Dark mode default).
- ❌ No drop shadows.
- ❌ No emojis.
- ❌ No liquid glass / heavy backdrop-blur elements.
- ❌ No Inter / Space Grotesk (Using IBM Plex & JetBrains Mono).
- ❌ No excessive soft radius (Strict 0-2px).
- ❌ No purple/black generic tech aesthetic.
- ❌ No animated background orbs or dot grids.

---

## 9. Implementation Blueprint & Setup Instructions

The following sections define the precise environment setup and component blueprints to be executed **only when implementation begins**. 

### Environment Initialization
```bash
# 1. Initialize Next.js project
npx create-next-app@latest frontend --typescript --tailwind --eslint --app --src-dir --import-alias "@/*" --use-npm

# 2. Enter directory
cd frontend

# 3. Initialize shadcn/ui
npx shadcn-ui@latest init
# (Choose: Default style, Slate color, CSS variables: yes)
```

### Path Conventions & Structure
By default, the `shadcn-ui` CLI installs generic reusable elements into `src/components/ui/`. 
**Why this is critical:** It enforces strict separation of concerns. The `components/ui/` folder acts as an isolated design-system library representing purely functional visual atoms (Buttons, Inputs). Domain-specific features (like the `AnimatedForm` or the Forensic Data Tables) belong outside of `ui/` (e.g., in `components/blocks/` or `components/features/`) to prevent architectural bloat and keep the foundational UI library easily updateable.

### External Dependencies
Install the required animation and icon packages for the Login interface:
```bash
npm install motion lucide-react
```

---

### Configuration Integrations

**1. `tailwind.config.js`**
Extend the theme to support the required animations and color variables:
```javascript
module.exports = {
  darkMode: "class",
  theme: {
    extend: {
      colors: {
        background: "var(--background)",
        foreground: "var(--foreground)",
        skeleton: "var(--skeleton)",
        border: "var(--btn-border)",
        input: "var(--input)",
      },
      borderRadius: {
        DEFAULT: "0.5rem",
      },
      boxShadow: {
        input: [
          "0px 2px 3px -1px rgba(0, 0, 0, 0.1)",
          "0px 1px 0px 0px rgba(25, 28, 33, 0.02)",
          "0px 0px 0px 1px rgba(25, 28, 33, 0.08)",
        ].join(", "),
      },
      animation: {
        ripple: "ripple 2s ease calc(var(--i, 0) * 0.2s) infinite",
        orbit: "orbit calc(var(--duration) * 1s) linear infinite",
      },
      keyframes: {
        ripple: {
          "0%, 100%": { transform: "translate(-50%, -50%) scale(1)" },
          "50%": { transform: "translate(-50%, -50%) scale(0.9)" },
        },
        orbit: {
          "0%": {
            transform:
              "rotate(0deg) translateY(calc(var(--radius) * 1px)) rotate(0deg)",
          },
          "100%": {
            transform:
              "rotate(360deg) translateY(calc(var(--radius) * 1px)) rotate(-360deg)",
          },
        }
      },
    },
  },
};
```

**2. `src/app/globals.css`**
Inject the CSS variables:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
   :root {
    --background: hsl(0, 0%, 100%);
    --foreground: hsl(0, 0%, 0%);
    --skeleton: hsl(0, 0%, 90%);
    --border: hsl(220, 20%, 90%);
    --btn-border: hsl(214deg, 31.8%, 91.4%);
    --input: hsl(220, 20%, 90%);
    
    --radius: 0.5rem;
  }

  .dark {
    --background: hsl(222, 94%, 5%);
    --foreground: hsl(0, 0%, 100%);
    --skeleton: hsl(218, 36%, 16%);
    --border: hsl(220, 20%, 90%);
    --btn-border: hsl(217deg, 32.6%, 17.5%);
    --input: hsl(219, 63%, 16%);
    
    --radius: 0.5rem;
  }

 body {
    @apply bg-background text-foreground;
  }
}

@layer components {
  .g-button {
    @apply rounded-[var(--radius)] border;
    border-color: var(--btn-border)
  }
}
```

---

### Component Specifications: Login Page (SCR-01)

#### 1. `src/components/ui/modern-animated-sign-in.tsx`
This file contains the core atoms (Input, BoxReveal, Ripple, OrbitingCircles, TechOrbitDisplay, AnimatedForm, AuthTabs).
*(Implementation note: To align with our strict anti-vibecode policy, the large 0.5rem radius and highly saturated buttons defined inside this component will be manually adjusted to our 0-2px scale and darker palette during the build phase.)*

#### 2. `src/app/login/page.tsx` (Adapted from the provided `demo.tsx`)
This file wraps the animated sign-in components.

**Critical Build-Time Adjustments for `demo.tsx`:**
When integrating `demo.tsx`, the placeholder technology images (React, HTML5) MUST be replaced with `lucide-react` icons (e.g., `<Terminal />`, `<Shield />`, `<Activity />`, `<Lock />`, `<Cpu />`). The JOCKY Framework is a forensic tool, not a web-dev agency, so the orbiting elements must visually communicate system defense, networking, and kernel monitoring.

---

### Component Analysis & Integration Guidelines (Login Page)

**1. What data/props will be passed to this component?**
- `formFields`: Object configuring the inputs (Email, Password).
- `handleSubmit`: The async function to trigger NextAuth.js authentication on the backend.
- `goTo`: The function handling "Forgot Password" routing.

**2. Are there any specific state management requirements?**
- **Local:** The component handles local form errors, password visibility toggling, and mouse coordinates (for the radial gradient hover effect on the inputs).
- **Global:** No global state belongs inside this component. The resulting authentication session (JWT) will be managed at the Next.js `layout.tsx` / NextAuth level.

**3. Are there any required assets (images, icons, etc.)?**
- Yes. The default Google Auth icon needs to be downloaded or linked via Unsplash/CDN.
- The `iconsArray` array in `demo.tsx` must be refactored to use `<Shield />` etc. from `lucide-react` instead of `next/image` tags.

**4. What is the expected responsive behavior?**
- **Desktop:** The layout is a 50/50 split (`w-1/2` each). The left side renders the `TechOrbitDisplay` and `Ripple` animations. The right side renders the `AuthTabs` form.
- **Mobile/Tablet (max-lg):** The left-side animation container is completely hidden (`max-lg:hidden`). The right-side form expands to full width (`max-lg:w-full`) and centers with horizontal padding (`max-lg:px-[10%]`).

**5. What is the best place to use this component in the app?**
- This component represents the entry gateway to the application. It should be instantiated inside `src/app/login/page.tsx`.

---

### Component Specifications: Hero Section / Dashboard Header (SCR-02)

#### 1. `src/components/ui/button.tsx`
Standard shadcn/ui button component utilizing `class-variance-authority` and `@radix-ui/react-slot`.

#### 2. `src/components/ui/infinite-slider.tsx`
Provides the scrolling infinite marquee for the target or extraction feed.

#### 3. `src/components/ui/progressive-blur.tsx`
Provides the gradient blur masking for the edges of the infinite slider.

#### 4. `src/components/blocks/hero-section-5.tsx`
This file contains the `HeroSection` and `HeroHeader`.

#### 5. `src/app/page.tsx` (Adapted from the provided `demo.tsx`)
This file wraps the `HeroSection`.

**Critical Build-Time Adjustments for `demo.tsx` & `hero-section-5.tsx`:**
- **Text & Copy:** "Build 10x Faster with NS" must be updated to reflect JOCKY's forensic purpose (e.g., "JOCKY Framework: Deep System Forensics").
- **Video Background:** The generic `cdn.21st.dev` mp4 should be swapped with a data-dense, looping terminal or hex-grid visualization to fit the dark cyber-theme (while preserving the `opacity-50` styling).
- **Logos:** The generic tech stack logos (Stripe, Vercel, Laravel) in the `InfiniteSlider` must be replaced. In the JOCKY context, this slider will dynamically represent **Target Machines** (using Windows/Linux icons) or **Active Extractions** (using `<Server />`, `<Activity />` from `lucide-react`).
- **Gradient Logo:** The SVG logo gradient must be adjusted to use `Cyber Green` (`#10B981`) instead of purple/teal.

---

### Hero Section Analysis & Integration Guidelines

**1. What data/props will be passed to this component?**
- `HeroSection` currently accepts no props. In our implementation, `HeroHeader` should accept user session state to conditionally render "Login/Sign Up" vs. "Dashboard/Logout".
- The `InfiniteSlider` should ideally accept an array of active targets/processes rather than hardcoded logo divs.

**2. Are there any specific state management requirements?**
- **Local:** `HeroHeader` uses local state (`menuState`) for mobile menu toggling and `scrolled` (via `framer-motion`'s `useScroll`) to apply a frosted glass background (`backdrop-blur-2xl`) when the user scrolls down.
- **Global:** No global state is handled directly, though auth state will dictate header button visibility.

**3. Are there any required assets (images, icons, etc.)?**
- The default component relies heavily on external SVGs (`cdn.simpleicons.org`). To adhere to our offline-first/free constraints and thematic consistency, these will be completely replaced by native `lucide-react` icons (e.g., `<Terminal />`, `<Shield />`).

**4. What is the expected responsive behavior?**
- **Desktop (lg+):** The header navigation items are horizontally visible. The slider shows a wide array of items.
- **Mobile/Tablet:** Navigation links collapse into a hamburger menu (`<Menu />` / `<X />`). When toggled, it expands a dropdown block. The `InfiniteSlider` uses `react-use-measure` to dynamically calculate width and adjust translation values to ensure it remains seamless on small screens.

**5. What is the best place to use this component in the app?**
- As the primary entry view (`src/app/page.tsx`) for the authenticated Dashboard (SCR-02) or the public marketing landing page if the tool is exposed externally. For the internal forensic tool, the large "Hero" section may be streamlined into a tighter "System Overview" header after the initial login.

### Additional Dependencies to Install
```bash
npm install @radix-ui/react-slot class-variance-authority framer-motion react-use-measure
```

---

### Component Specifications: Editor & Forensic Analysis Views (SCR-03 & SCR-04)

Based on the provided design references, the core JOCKY workspace will implement three specialized forensic components. These components form the functional heart of the application for writing payloads and analyzing targets.

#### 1. File Viewer (The JOCKY Code Editor)
**Purpose:** Provides a full IDE-like experience for writing JOCKY DSL scripts and browsing compiled payloads.
**Structure:**
- **Sidebar (File Tree):** A collapsible left-hand rail (e.g., `src/`, `components/`) using semantic file icons. Includes selectable `TreeViewElement` structures.
- **Main Pane (Shiki/Monaco Viewer):** A syntax-highlighted code editor utilizing a dark theme (matching the `Deep Void` aesthetic).
- **Header Toolbar:** Features active file path (`src/types.ts`), language tags (`TS`), and utility icons (Copy Code, Open in New Tab).
**Implementation Guidelines:**
- Use `@monaco-editor/react` or `shiki` for robust syntax highlighting.
- The file tree should be implemented using nested accordion components or a custom headless tree library for accessibility.
- Maintain the strict 0px border radius and monochromatic layout defined in Section 5.

#### 2. Blast-Radius Measurement (Inline Diff Viewer)
**Purpose:** Displays the forensic timeline—visualizing the delta between a target's clean state and its compromised state, or showing exact script modifications.
**Structure:**
- **GitHub-Style Inline Diff:** A data grid where each row represents a line of code or a system state.
- **Color Coding:** 
  - **Additions (Green):** Represented with a `+` symbol and a muted green background (e.g., `#0f5132` mapped to `Cyber Green` opacity).
  - **Deletions/Compromises (Red):** Represented with a `-` symbol and a muted red background (e.g., `#842029` mapped to `Critical Red` opacity).
- **Interactive Annotations:** Hovering over a line reveals an "Add comment" interaction, allowing analysts to annotate forensic evidence directly on the timeline.
**Implementation Guidelines:**
- Do not use generic tables. Use a specialized diff-viewer library or a tightly controlled grid layout mapping exact line numbers (Left column: Old Line, Middle: New Line, Right: Content).

#### 3. Sandbox / Compilation Environment
**Purpose:** A rapid testing environment where analysts can compile JOCKY scripts and instantly view the terminal output.
**Structure:**
- **Tabbed Interface:** Features a top navigation bar with specific tabs: `<> Code`, `Monitor Preview`, and `>_ Console`.
- **Canvas Area:** A distinct pane where the payload executes or logs are streamed.
**Implementation Guidelines:**
- The tabs should use `lucide-react` icons and a simple bottom-border highlight for active states.
- The `Console` tab must simulate a real terminal interface, utilizing a strict monospace font (`JetBrains Mono` or `Fira Code`) for streaming compilation logs.

---

## 10. Advanced Forensic Integrations (Sandbox, AI, & TEE)

### 1. AI Code Assistant Integration (ADD-002)
- **Editor Extension:** The `shiki/monaco` viewer (SCR-03) will be wrapped with an LSP (Language Server Protocol) client. 
- **BYOK / Local Mode:** The AI assistant operates strictly on a Bring-Your-Own-Key (BYOK) model to ensure zero-cost. It communicates with OpenAI/Anthropic APIs utilizing strict zero-data-retention headers.
- **UI Treatment:** A discrete "AI Assist" panel docked to the right of the Code Editor, using a minimalistic chat interface (matching the `Bone White` on `Carbon Panel` aesthetic) to generate JOCKY syntax.

### 2. TEE Privacy Mode / Local Execution (ADD-003)
- **Constraint Resolution:** True Cloud Confidential Computing violates the ₹0 prototype cost. To provide TEE-level privacy guarantees, the UI will feature a "Local-Only AI" toggle.
- **Implementation:** When enabled, the LSP routes requests locally to `http://localhost:11434` (Ollama), running an 8B parameter model (e.g., Llama-3) on the analyst's RTX 4050. This guarantees zero sensitive code/telemetry leaks to external networks.

### 3. Ephemeral Sandbox Dry-Runs (ADD-001)
- **Workflow:** Before compiling the final polymorphic payload, the analyst hits "Preview Impact". 
- **Backend:** The Build Server spins up an ephemeral Docker container or QEMU microVM running a stripped-down OS environment. The script executes against this sandbox while an eBPF/auditd sidecar logs all syscalls, file mounts, and network attempts.
- **UX Integration:** A snappy loading state overlays the Sandbox canvas while the dry-run computes (max 5 seconds).

### 4. Blast-Radius Measurement & Immutable Timeline (ADD-004)
- **Data Rendering:** The eBPF sandbox logs and AST static analysis are combined into a `Blast-Radius JSON` object.
- **UI Presentation:** This is rendered in the **Inline Diff Viewer** (SCR-04). 
  - Simulated network requests or file modifications appear as "Additions" (`+`, Green) or "Deletions" (`-`, Red). 
  - The analyst can hover over these predicted impacts and click "Approve".
- **Immutable Ledger:** Once approved and executed on the real target, the *actual* execution logs are transmitted back via GitHub Gists and appended to an SQLite ledger using SHA-256 hash chaining, guaranteeing chain-of-custody for the forensic evidence.
