# JOCKY Framework: Prototype Specification

## A. Executive Summary
The proposed solution is the **JOCKY Framework**, an advanced, cross-platform forensic and threat-hunting toolkit designed to perform deep system analysis without triggering modern EDR/AV solutions. It leverages a custom programming language (JOCKY) built on an LLVM frontend, which trans-compiles forensic logic into polymorphic payloads using BYOVD methodologies and Direct Syscalls. 
**New Additions:** To ensure safe deployment and rapid script creation, JOCKY now incorporates an **AI Code Assistant** (via user-provided APIs or local TEE-compliant models), an **Ephemeral Dry-Run Sandbox** for pre-deployment testing, and a **Blast-Radius Measurement Engine** that provides a static/dynamic impact analysis timeline stored in an immutable ledger. This prototype is engineered for ₹0 deployment, utilizing open-source infrastructure and local compute (RTX 4050).

## B. Problem Understanding
**Current Problem:** Modern EDR/AV blindly blocks proprietary forensic scripts. Analysts also lack the ability to safely preview the impact of a script before deploying it to a compromised production server, risking further instability.
**Root Causes:**
1. Standard compilers generate recognizable signatures and utilize noisy Windows APIs.
2. Forensic tools lack built-in simulation environments (Sandboxes) and impact analysis (Blast-Radius).
**Consequences:** Analysts trigger alerts, contaminate environments, or accidentally cause system instability because they cannot dry-run payloads.

## C. Environmental Feasibility Audit
- **LLVM Compiler & BYOVD:** Feasible and open-source.
- **Covert C2:** Feasible using legitimate Cloud APIs (GitHub Gists).
- **ADD-001 Sandbox Execution:** Feasible. We will utilize lightweight local containers (e.g., Docker/containerd restricted via seccomp) on the Build Server to perform dry-runs before payload generation.
- **ADD-002 AI Integration:** Feasible via BYOK (Bring Your Own Key) for OpenAI/Anthropic, or local Ollama on the analyst's RTX 4050 to maintain zero cost.
- **ADD-003 TEE Terminal:** Cloud TEEs break the ₹0 cost constraint. **Resolution:** We achieve TEE-level privacy by enforcing a "Local-Only" AI execution mode using the local GPU (Ollama) so zero sensitive code leaves the machine.
- **ADD-004 Blast-Radius & Timeline:** Feasible. Static AST analysis combined with the Sandbox dry-run outputs a precise JSON diff of touched assets, stored in a local SQLite immutable ledger.

## D. Legal / Policy Audit
- **LLVM / Cloud APIs / BYOVD:** Safe for authorized forensic use.
- **AI APIs (ADD-002):** Using third-party APIs risks code leakage. **Mitigation:** Strict BYOK enforcement with zero-data-retention headers (e.g., OpenAI API). Local-only mode (Ollama) guarantees full compliance.

## E. Integration Reality Audit
| Integration | Purpose | Official API? | Free? | Server Req? | Legal Status | Fallback |
|---|---|---|---|---|---|---|
| **LLVM (llvmlite)** | Code Compilation | Local Library | Yes | Local | Safe | Manual C++ AST |
| **GitHub Gists API** | Covert C2 Traffic | Official REST | Yes | No | Safe | Vercel Serverless |
| **Local Container / QEMU** | Sandbox Dry-Run (ADD-001) | Docker/QEMU CLI | Yes | Local | Safe | Chroot |
| **Ollama / OpenAI** | AI Assistant (ADD-002) | REST API | Yes | Local/Cloud | Safe | Static Snippets |

## F. Feature Limitation Audit
- **Limitation:** Dry-run sandboxes cannot perfectly mimic a highly specific compromised Windows target.
  - *Mitigation:* The sandbox serves as an *estimate* (Blast-Radius preview), verifying syntax, logic, and intended asset targeting, not a perfect 1:1 clone of the target OS state.

## G. Feasibility Verdict
✅ **Feasible with constraints.**
The ₹0 constraint strictly forces the "Sandbox" and "AI Model" to run locally on the Analyst's provided hardware (i5, 16GB RAM, RTX 4050). The hardware is perfectly capable of running an 8B parameter LLM (Ollama) and lightweight Docker/QEMU sandboxes alongside the LLVM compiler.

## H. User Journeys
**Entry -> AI Assist -> Dry-Run -> Compile -> Deployment -> Result**
1. Analyst writes a script in the UI, assisted by the **Local AI Assistant** (ADD-002, ADD-003).
2. Analyst clicks "Preview Blast Radius". The Build Server compiles a safe version and executes it in the **Ephemeral Sandbox** (ADD-001).
3. UI displays the **Blast-Radius Timeline** (ADD-004) showing simulated file/network touches. Analyst approves.
4. System compiles the final polymorphic payload.
5. Analyst deploys Payload to Target (BYOVD blind + Syscalls).
6. Results post to GitHub Gist. UI displays actual execution timeline alongside the simulated one.

## I. Problem-to-Solution Traceability
| Problem | Proposed Solution | Feature | Technical Implementation | Status |
|---|---|---|---|---|
| Signatures flag scripts | Custom LLVM Frontend | JOCKY Compiler | Python `llvmlite` | Existing |
| EDR hooks APIs | Direct Syscalls | Memory Exec | SysWhispers | Existing |
| EDR kernel visibility | BYOVD Subversion | EDR Blinder | Exploiting vulnerable driver | Existing |
| Unpredictable Script Impact | Pre-execution Simulation | Sandbox & Blast-Radius | Local Container + AST static analysis | **NEW** (ADD-001, 004) |
| Data leakage via AI | Local execution | TEE Privacy Mode | Ollama on RTX 4050 | **NEW** (ADD-002, 003) |

## J. Functional Requirements
- **FR-001 (Compiler) to FR-006 (Central UI):** [Maintained from original]
- **FR-007 (Sandbox Runner - ADD-001):** System must spin up an ephemeral container/VM to dry-run scripts.
- **FR-008 (AI Assistant - ADD-002/003):** Editor must support an LSP communicating with a local Ollama instance or BYOK API.
- **FR-009 (Blast-Radius - ADD-004):** System must parse AST and sandbox logs to visually present impacted assets before final compilation.
- **FR-010 (Immutable Ledger - ADD-004):** Every execution (simulated and actual) must be hashed and stored in a local SQLite append-only log.
- **FR-011 (Core Forensic Modules):** System must include at least two functional JOCKY scripts to prove end-to-end viability: a **Network Dump Module** (enumerates active TCP/UDP connections) and a **Process Enumeration Module** (lists running processes and PIDs).

## K. Non-Functional Requirements
- **Performance:** Local AI response < 2s. Sandbox spin-up < 3s. Payload compilation < 15s.
- **Security:** Immutable ledger must use SHA-256 hash chaining to prevent tampering with the forensic timeline.

## L. Architecture
```text
Analyst (Next.js UI) <---> [ AI Assistant (Ollama / BYOK) ]
       ↓ (Request Dry-Run)
[ Local Build Server ]
 ├── AST Static Analyzer
 └── Ephemeral Sandbox (Docker/QEMU) -> Generates Blast-Radius JSON
       ↓ (Approve & Compile)
[ Polymorphic Payload.exe ]
       ↓
[ Target System (BYOVD + Syscalls) ] -> Executes & Logs actual impact
       ↓
[ GitHub Gists API (C2) ]
       ↓
[ Immutable SQLite Ledger (Timeline Storage) ] -> Validates against Blast-Radius
```

## M. Technology Stack
*Added to existing:* Docker/containerd (Sandbox), Ollama/Llama.cpp (Local AI), SQLite (Immutable Ledger).

## N. Data Architecture (Updated)
**Table: execution_timeline (NEW)**
| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Primary Key |
| script_id | UUID | Yes | Reference to script |
| phase | Enum | Yes | `SIMULATED` or `ACTUAL` |
| blast_radius | JSONB | Yes | Touched files, networks, APIs |
| previous_hash | String | Yes | Hash chaining for immutability |

## O. Security Model (Updated)
- **Threat (AI Prompt Injection):** Mitigated by treating AI outputs as strictly untrusted until Sandbox verification.
- **Threat (Sandbox Escape):** Mitigated by running dry-runs without BYOVD payloads and restricting the sandbox with strict seccomp profiles.

## P. Testing Strategy
- **T4 (Sandbox):** Run destructive script in sandbox. Result: Sandbox destroyed, host OS unharmed.
- **T5 (Blast-Radius):** Run script accessing `C:\Windows`. Result: Blast-radius UI correctly flags the path.
- **T6 (Forensic Modules):** Compile and deploy the Network Dump and Process Enumeration modules on target. Result: Accurate JSON payloads of connections/processes returned via C2.

## Q. Demo Strategy (Updated)
1. Write/Load the **Network Dump Module** using the AI Assistant.
2. Hit "Dry-Run". Show the Blast-Radius inline diff highlighting simulated network connections.
3. Approve compilation. Deploy to Windows 11 VM.
4. Show EDR bypassing natively.
5. Show the actual Execution Timeline matching the simulated Blast-Radius in the UI ledger, alongside the live Network Dump data.

## R. Free Hosting & Cost Audit
- Development, Hosting, Database, APIs: ₹0
- **AI & Sandbox:** ₹0 (Running locally on the provided RTX 4050 and i5 CPU).

## S. Definition of Done (Updated)
- [ ] AI assistant generates valid JOCKY DSL.
- [ ] Sandbox executes dry-runs safely.
- [ ] Blast-radius inline diff accurately shows expected impact.
- [ ] Execution timeline is immutably stored.
- [ ] **Network Dump** and **Process Enumeration** modules are fully functional and tested on Windows 11.
- *(Plus all existing DODs).*
