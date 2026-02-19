## 📖 Part — Password Recovery (Router & Switch)

### 📝 Summary:
Password recovery on Cisco devices is a **physical-access** procedure.  
The common idea is to boot the device **without loading the startup configuration**, so you can regain access, restore the original configuration, and then set new credentials.

This guide covers:
- Router password recovery using **ROMMON** and the **configuration register**
- Switch password recovery using the **boot loader** (switch: prompt) and renaming config.text

### 🎯 Objectives:
- Understand why password recovery requires physical access (console + power cycle).
- Explain what the configuration register does (especially (0x2102) vs (0x2142)).
- Perform password recovery on a router using ROMMON.
- Perform password recovery on a Catalyst switch (e.g., 2960) using the boot loader.
- Restore the original configuration safely and reset new credentials.
- Understand the risk of no service password-recovery.

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

- Normal boot (loads startup-config): (0x2102)
- Ignore startup-config during boot (recovery mode): (0x2142)

Important correction:
- (0x2142) means: “Boot IOS normally, but **do not load startup-config** into running-config.”
- It does **not** mean “run running-config.” Running-config only exists after IOS boots (in RAM).

#### 1) Enter ROMMON
1. Power off the router.
2. Power on the router.
3. During early boot, send the **Break** sequence (varies by terminal):
   - Packet Tracer: usually a “Break” button/option in the terminal window
   - Real terminal apps: may be Ctrl+Break, Ctrl+Fn+B, etc.
4. You should land in ROMMON prompt, often like:
   - rommon 1 >

#### 2) Set the recovery configuration register and boot
```
rommon 1 > confreg 0x2142  
rommon 2 > reset  
```
(Some platforms use confreg interactively; the goal is still to set (0x2142).)

#### 3) Boot IOS without startup-config
After IOS loads, you may see the initial configuration dialog:
- Answer: no

Now you can enter privileged mode (because the startup-config was ignored):
```
Router> enable
```
#### 4) Restore the original config into RAM
This is the critical step to avoid losing IP addressing/routing settings.
```
Router# copy startup-config running-config
```
#### 5) Change credentials (recommended approach)
Do **not** use no username without specifying a username (that’s not a valid generic command in IOS).

Set or replace a local admin user:
```
Router# configure terminal  
Router(config)# username Parsa secret 4444  
Router(config)# enable secret 123  
Router(config)# end  
```
#### 6) Set config-register back to normal and save
Verify the current register:
```
Router# show version
```
If you see Configuration register is 0x2142, change it back:
```
Router# configure terminal  
Router(config)# config-register 0x2102  
Router(config)# end  
Router# write memory  
```
#### 7) Reload and test
```
Router# reload
```
After reload, confirm you can:
- SSH/console login with your user
- Enter privileged EXEC with enable

---

### 🔹 B) Switch Password Recovery (Boot Loader)

#### ✅ Concept
On many Catalyst switches (including common CCNA models), the startup configuration is stored as a file in flash:
- flash:config.text (startup configuration)
- Running-config lives in RAM

For password recovery, we temporarily rename config.text so the switch boots **without** it, then we load it back into RAM and change passwords.

#### 1) Enter switch boot loader (switch: prompt)
1. Power off the switch.
2. Press and hold the **MODE** button.
3. Power on the switch while holding MODE.
4. Release MODE when the LED behavior indicates boot loader mode (varies by model).
5. You should see:
- switch: prompt

#### 2) Initialize flash and rename the config
```
switch: flash_init  
switch: dir flash:  
switch: rename flash:config.text flash:config.text.old  
switch: boot  
```
#### 3) Boot without startup-config
The switch boots with a “clean” configuration. You may see the setup dialog:
- Answer: no

Enter privileged mode:
```
Switch> enable
```
#### 4) Load the original config into running-config (RAM)
Use the old file you renamed:
```
Switch# copy flash:config.text.old running-config
```
#### 5) Set new passwords/users
```
Switch# configure terminal  
Switch(config)# username Parsa secret 4444  
Switch(config)# enable secret 123  
Switch(config)# line console 0  
Switch(config-line)# login local  
Switch(config-line)# exit  
Switch(config)# line vty 0 15  
Switch(config-line)# login local  
Switch(config-line)# transport input ssh  
Switch(config-line)# end  
```
#### 6) Save and clean up
Save your new working config as the new startup-config:
```
Switch# write memory
```
Delete the old backup file:
```
Switch# delete flash:config.text.old
```
---

### 🧾 Command Caption Table

| Command | Where | Purpose |
|---|---|---|
| confreg 0x2142 | Router ROMMON | Boot IOS but ignore startup-config (recovery mode) |
| reset | Router ROMMON | Reboot device using the new register value |
| copy startup-config running-config | Router IOS | Restore original configuration into RAM |
| config-register 0x2102 | Router IOS | Return to normal boot behavior |
| flash_init | Switch boot loader | Initialize flash filesystem |
| rename flash:config.text flash:config.text.old | Switch boot loader | Prevent loading startup-config by hiding the file |
| copy flash:config.text.old running-config | Switch IOS | Load old config into RAM so you don’t lose settings |
| write memory | Router/Switch IOS | Save running-config as startup-config |

### ✅ Verification:
Router:
```
show version  
show running-config | include username|enable secret  
show running-config | section line  
```
Switch:
```
dir flash:  
show running-config | include username|enable secret  
show running-config | section line  
```
