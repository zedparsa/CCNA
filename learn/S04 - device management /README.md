## 📖 Part 1 — Password Recovery (Router & Switch)

### 📝 Summary:
Password recovery on Cisco devices is a **physical-access** procedure.  
The common idea is to boot the device **without loading the startup configuration**, so you can regain access, restore the original configuration, and then set new credentials.

This guide covers:
- Router password recovery using **ROMMON** and the **configuration register**
- Switch password recovery using the **boot loader** (`switch:` prompt) and renaming `config.text`

### 🎯 Objectives:
- Understand why password recovery requires physical access (console + power cycle).
- Explain what the configuration register does (especially \(0x2102\) vs \(0x2142\)).
- Perform password recovery on a router using ROMMON.
- Perform password recovery on a Catalyst switch (e.g., 2960) using the boot loader.
- Restore the original configuration safely and reset new credentials.
- Understand the risk of `no service password-recovery`.

### 🧩 Topology:
- 1x Router (console access)
- 1x Switch (console access)
- 1x PC/Laptop (terminal emulator: Packet Tracer PC, PuTTY, Tera Term, etc.)
- Console cable for each device (or direct console in Packet Tracer)

### 🛠️ Step-by-Step:

### 🔹 A) Router Password Recovery (ROMMON)

#### ✅ Concept
Routers can enter **ROMMON** (like a low-level recovery mode).  
From ROMMON, you can temporarily change the boot behavior using the **configuration register**.

- Normal boot (loads startup-config): `0x2102`
- Ignore startup-config during boot (recovery mode): `0x2142`

Important correction:
- \(0x2142\) means: “Boot IOS normally, but **do not load startup-config** into running-config.”
- It does **not** mean “run running-config.” Running-config only exists after IOS boots (in RAM).

#### 1) Enter ROMMON
1. Power off the router.
2. Power on the router.
3. During early boot, send the **Break** sequence (varies by terminal):
   - Packet Tracer: usually a “Break” button/option in the terminal window
   - Real terminal apps: may be `Ctrl+Break`, `Ctrl+Fn+B`, etc.
4. You should land in ROMMON prompt, often like:
   - `rommon 1 >`

#### 2) Set the recovery configuration register and boot
