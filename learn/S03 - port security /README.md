# 🔐 Port security — Tutorial
- When you buy a Cisco device like a switch or router, 
it comes with a basic configuration and no security enabled by default. 
- You are responsible for securing it. 
- How? Stay with me and we will figure it out together.
## 📖 Part 1 — Console Password (Type 7):
### 📝 Summary:
Set a basic console line password to secure device access (weak, Type 7 encryption).
### 🎯 Objective
- Understand the need for securing console access on Cisco devices.  
- Configure a basic console line password using `line console 0`.  
- Verify console login with the configured password.  
- Be aware of Type 7 password weakness.  

### 🧩 Topology
 
<p align="center">
  <img src="topologies/port-security-1.png" alt="Port Security Topology" />
</p>

- One PC and one Switch 
- One PC connected to one Switch via a console cable (RS-232 → Console)

### 🛠️ Step-by-Step
Use the following commands to configure a console password :
  ```cisco
  Switch> enable
  Switch# configure terminal
  Switch(config)# line console 0
  Switch(config-line)# password 123
  Switch(config-line)# login
  Switch(config-line)# end
```
<p align="center">

| Command              | Description                         |
|----------------------|-------------------------------------|
| line console 0       | Enter console line configuration    |
| password 123         | Set console password (123)          |
| login                | Enable password checking on console |
| end / Ctrl+Z         | Exit to privileged EXEC mode        |

</p>

### ✅ Verification:
- Exit the console session (close terminal).
- Reconnect to the switch via console.
- You should now be prompted for a password before accessing User EXEC mode.

### ⚠️ Note:
This method is weak because:
> - Type 7 encryption is easily reversible.  
> - Only one shared password for all users.  
> - No usernames, so no individual accountability.  
> - Works only on console line (no centralized authentication).  

So what should we do? 🤔  
- In the next part you will get the answer.  
- For now, just practice this method to understand the basics.
