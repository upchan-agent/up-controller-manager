# 🆙 Controller Manager

**LSP-6 KeyManager Guardian Management UI for Universal Profiles**

A secure web interface for managing Universal Profile controllers on LUKSO. Runs exclusively inside **universaleverything.io Grid**.

🌐 **Access**: [universaleverything.io](https://universaleverything.io) → Connect your UP → Open this app

---

## 🎯 Why This Tool Exists

**Problem:** Managing Universal Profile controllers is dangerous and opaque.

- Existing tools require complex setups or external extensions
- Permission management is all-or-nothing — no fine-grained control
- No visual feedback on what calldata is being signed
- High risk of deadlock (locking yourself out) or over-permissioning

**Solution:** A transparent, auditable controller management tool.

- **100% transparent code** — single HTML file, no build process
- **Official imports only** — `@lukso/up-provider` and `ethers.js` from CDN
- **Fine-grained permissions** — 23 individual bits with risk analysis
- **Deadlock detection** — warns before you lock yourself out
- **Calldata preview** — see exactly what you're signing before confirming

---

## 🔍 Key Differentiators

### Single HTML, Full Transparency
This is **not** a bundled, minified application. The entire tool lives in one `index.html` file:

- **No build process** — what you see is what runs
- **No hidden code** — 100% readable and auditable
- **Official imports only** — ethers.js and @lukso/up-provider from trusted CDNs
- **Source-verifiable** — inspect every line on GitHub

This is a **controller management tool** — you're granting access to your Universal Profile. You should be able to verify exactly what code you're running.

---

## 🌟 Features

### Core Functionality
- **Add Guardian Controllers** — Grant another Universal Profile access as a backup controller
- **Permission Management** — Fine-grained control over 23 LSP-6 permission bits with preset templates
- **Controller Removal** — Safe deletion with deadlock detection and swap-and-pop strategy
- **Real-time Validation** — On-chain verification with ERC165 interface detection (LSP-0, LSP-11)

### Security First
- **Deadlock Prevention** — Warns if removal would leave no admin-capable controllers
- **Risk Analysis** — Clear warnings for dangerous permissions (REENTRANCY, DELEGATECALL, SUPER_DELEGATECALL)
- **LSP-11 Protection** — Confirmed social recovery contracts cannot be accidentally removed
- **AllowedCalls Protection** — Controllers with restrictions cannot be removed (prevents orphaned data)
- **Calldata Transparency** — Full calldata preview before signing (relayer + direct modes)

### Preset Permissions
| Preset | Use Case | Permissions |
|--------|----------|-------------|
| **AI-Profile** 🤖📝 | Profile & data updates only | `STATICCALL`, `SUPER_SETDATA`, `SIGN`, `EXECUTE_RELAY_CALL` |
| **AI-Wallet** 🤖💰 | Full on-chain actions (LYX restricted) | `TRANSFERVALUE`, `SUPER_CALL`, `STATICCALL`, `SUPER_SETDATA`, `SIGN`, `EXECUTE_RELAY_CALL` |
| **Recovery** 🔑 | Full admin (compatible with LUKSO Extension) | `ADDCONTROLLER`, `EDITPERMISSIONS`, all execution permissions except `CHANGEOWNER` |
| **Custom** ⚙️ | Manual bit-level control | Toggle any of 23 permission bits |

### UX Excellence
- **Dark Mode UI** — Professional amber-gold + slate blue theme
- **Responsive Design** — Works on desktop and mobile within Grid
- **One-Click Presets** — Select preset or toggle individual bits
- **Bitmap Editor** — Manual hex input with validation (0x + 64 chars)
- **Explorer Links** — Direct links to Blockscout and erc725-inspect for each controller

---

## 🚀 Usage

### Prerequisites
- **universaleverything.io Grid** account
- Universal Profile on LUKSO Mainnet with controller permissions
- Small amount of LYX for gas (if using direct mode)

### How to Access
1. Go to [universaleverything.io](https://universaleverything.io)
2. Connect your Universal Profile
3. Open the app from your Grid

### How to Use
1. **Connect** — Your UP connects automatically via Grid
2. **View Controllers** — See all registered controllers with type badges (KM, RECOVERY, UP, EOA)
3. **Add Guardian** — Enter a guardian UP address and select permissions
4. **Review** — Check calldata preview before signing
5. **Sign** — Execute via UP Extension (relayer or direct mode)

---

## 🏗️ Architecture

### Tech Stack
- **Framework** — Vanilla HTML/CSS/JavaScript (no build step)
- **Blockchain** — ethers.js 5.7.2
- **Provider** — @lukso/up-provider 0.3.7 (injected by Grid)
- **RPC** — LUKSO Mainnet (`https://rpc.mainnet.lukso.network`)
- **Explorer** — Blockscout (`https://explorer.execution.mainnet.lukso.network`)

### Key Components
```
index.html
├── UP Provider integration (createClientUPProvider)
├── LSP-3 Profile metadata fetching (IPFS resolution)
├── Controller list rendering (with type detection)
├── Permission bit UI (23 bits with tooltips)
├── AllowedCalls/AllowedDataKeys detection (LSP-2 compliance)
├── Calldata builder (setDataBatch encoding)
└── Transaction execution (relayer + direct modes)
```

### LSP Standards Compliance
- **LSP-0** — ERC725Account (Universal Profile)
- **LSP-2** — ERC725YJSONSchema (data key encoding)
- **LSP-3** — Profile Metadata (name, avatar)
- **LSP-6** — KeyManager (permission system)
- **LSP-11** — Basic Social Recovery (recovery contract detection)
- **LSP-25** — ExecuteRelayCall (gasless transactions)

---

## 📋 LSP-6 Permission Reference

### Admin Permissions (High Risk)
| Permission | Bit | Hex | Description |
|------------|-----|-----|-------------|
| `CHANGEOWNER` | 0 | 0x0001 | Transfer full ownership |
| `ADDCONTROLLER` | 1 | 0x0002 | Add new controllers |
| `EDITPERMISSIONS` | 2 | 0x0004 | Modify existing permissions |

### Execution Permissions
| Permission | Bit | Hex | Description |
|------------|-----|-----|-------------|
| `SUPER_TRANSFERVALUE` | 8 | 0x0100 | Send LYX to any address |
| `TRANSFERVALUE` | 9 | 0x0200 | Send LYX (AllowedCalls restricted) |
| `SUPER_CALL` | 10 | 0x0400 | Call any contract |
| `CALL` | 11 | 0x0800 | Call contracts (AllowedCalls restricted) |
| `SUPER_STATICCALL` | 12 | 0x1000 | Read-only calls (unrestricted) |
| `STATICCALL` | 13 | 0x2000 | Read-only calls (restricted) |
| `SUPER_DELEGATECALL` ⚠️ | 14 | 0x4000 | Arbitrary code execution |
| `DELEGATECALL` ⚠️ | 15 | 0x8000 | Delegate call (restricted) |
| `DEPLOY` | 16 | 0x10000 | Deploy contracts |

### Data & Utility Permissions
| Permission | Bit | Hex | Description |
|------------|-----|-----|-------------|
| `SUPER_SETDATA` | 17 | 0x20000 | Write any data key |
| `SETDATA` | 18 | 0x40000 | Write data keys (restricted) |
| `SIGN` | 21 | 0x200000 | ERC-1271 signatures |
| `EXECUTE_RELAY_CALL` | 22 | 0x400000 | Gasless relay calls |

---

## 🔐 Security Considerations

### Dangerous Permissions
The following permissions carry critical risk and trigger prominent warnings:
- **`SUPER_DELEGATECALL`** — Called contract runs in profile's storage context (arbitrary code execution)
- **`DELEGATECALL`** — Same as above, but scoped to AllowedCalls
- **`REENTRANCY`** — Allows re-entering execute() during call (reentrancy attacks)
- **`CHANGEOWNER`** — Full ownership transfer (irreversible takeover)

### Deadlock Detection
The tool prevents permission changes that would:
- Leave zero controllers with `ADDCONTROLLER` permission
- Leave zero controllers with `EDITPERMISSIONS` permission
- Remove the last admin-capable controller

### AllowedCalls Protection
**Controllers with AllowedCalls or AllowedERC725YDataKeys restrictions cannot be removed via this tool.**

This is a **fail-safe design** to prevent:
1. **Orphaned data** — Restrictions left on-chain after controller removal
2. **Future conflicts** — Old restrictions affecting re-added controllers
3. **Data inconsistency** — Mismatch between AddressPermissions[] and restriction keys

**To remove such controllers:**
1. Clear restrictions manually via LUKSO Extension or Blockscout
2. Then use this tool to remove the controller

### LSP-11 Social Recovery Protection
Confirmed LSP-11 recovery contracts cannot be removed via this UI to prevent accidental loss of account recovery capability. Use Blockscout → Write Contract for intentional removal.

---

## ⚠️ Known Limitations

### AllowedCalls Configuration (Not Yet Implemented)

This tool currently **does not support setting** `AddressPermissions:AllowedCalls:<address>` or `AddressPermissions:AllowedERC725YDataKeys:<address>`.

**Impact:**
- `TRANSFERVALUE` permission without AllowedCalls = LYX transfers blocked by KeyManager
- `CALL` permission without AllowedCalls = contract calls blocked by KeyManager
- `SUPER_CALL` and `SUPER_SETDATA` are unrestricted (high risk)

**Current Behavior:**
- AI-Wallet preset grants `TRANSFERVALUE` + `SUPER_CALL`
- Without AllowedCalls, `TRANSFERVALUE` is effectively blocked
- `SUPER_CALL` allows unrestricted contract calls (use with caution)

**Workaround:**
Use LUKSO Extension or erc725-inspect to set AllowedCalls manually after adding a guardian.

**Future Work:**
AllowedCalls configuration UI is planned for a future release.

---

## 📝 Transaction Flow

### Adding a New Controller
1. User enters guardian UP address
2. Tool validates address and checks on-chain state
3. User selects permission preset
4. Tool builds calldata:
   ```
   setDataBatch([
     AddressPermissions[],              // increment length
     AddressPermissions[index]:address, // new controller address
     AddressPermissions:Permissions:address // permission bitmap
   ])
   ```
5. User reviews calldata and signs via UP Extension
6. Transaction executes via relayer (gasless) or direct mode

### Removing a Controller
1. User clicks delete button on controller row
2. Tool checks safety conditions:
   - Not Key Manager address
   - Not LSP-11 Social Recovery contract
   - Not unknown contract
   - **No AllowedCalls/AllowedDataKeys restrictions**
   - Not self (connected UP)
   - No deadlock risk
3. Modal shows strategy (direct removal or swap-and-pop)
4. User confirms and signs
5. Tool executes:
   ```
   // Direct removal (last index)
   setDataBatch([
     AddressPermissions[],              // decrement length
     AddressPermissions[index]:address, // clear to 0x
     AddressPermissions:Permissions:address // clear to 0x
   ])
   
   // Swap-and-pop (middle index)
   setDataBatch([
     AddressPermissions[],              // decrement length
     AddressPermissions[target]:address, // move last entry here
     AddressPermissions[last]:address,   // clear old last
     AddressPermissions:Permissions:target // clear permissions
   ])
   ```

---

## 🎨 Design Philosophy

**Authoritative, not playful.** The amber-gold primary color conveys authority and trust. The near-black background with neutral tones ensures long coding sessions without eye strain.

**Information density without clutter.** Every pixel serves a purpose. Tooltips on hover provide detailed permission descriptions without overwhelming the main view.

**Transparency builds trust.** Full calldata preview, explorer links, and clear risk warnings empower users to make informed decisions.

**Fail-safe over convenience.** Blocking removal of controllers with restrictions prevents orphaned data, even if it requires extra steps.

---

## 🏆 Synthesis Hackathon

**Track:** LUKSO / Universal Profile Infrastructure

**Team:** 🆙chan (AI Agent) + shell (Human)

**Agent Harness:** OpenClaw

**Model:** Qwen 3.5 Plus (Bailian)

**Conversation Log:** This tool was born from a need to safely manage Universal Profile guardians. The design prioritizes security over convenience, with multiple layers of validation and clear warnings for dangerous operations.

---

## 📄 License

MIT License — see LICENSE file.

---

## 🔗 Links

- **GitHub:** [github.com/upchan-agent/up-controller-manager](https://github.com/upchan-agent/up-controller-manager)
- **LUKSO Docs:** [docs.lukso.tech](https://docs.lukso.tech)
- **LSP-6 Spec:** [github.com/lukso-network/LIPs/blob/main/LSPs/LSP-6-KeyManager.md](https://github.com/lukso-network/LIPs/blob/main/LSPs/LSP-6-KeyManager.md)
- **Blockscout:** [explorer.execution.mainnet.lukso.network](https://explorer.execution.mainnet.lukso.network)
- **erc725-inspect:** [erc725-inspect.lukso.tech](https://erc725-inspect.lukso.tech)

---

_Built with ❤️ for The Synthesis Hackathon 2026_
