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
| `line console 0 `      | Enter console line configuration    |
| `password 123`         | Set console password (123)          |
| `login`                | Enable password checking on console |
| `end` / `Ctrl+Z`         | Exit to privileged EXEC mode        |

</p>

### 🔑 Extra: Securing with `service password-encryption`

Another method is to use the command `service password-encryption`.  
This command hides all plain-text passwords in the running configuration by converting them into Type 7.  

If you add the command service password-encryption in global configuration: 
```cisco
Switch(config)# service password-encryption
```
Then in show running-config the password will appear encrypted (Type 7):
```cisco
line console 0
  password 7 0822455D0A16
  login
```

And if you don't add :
```cisco
line console 0
  password 123
  login
```

### 🔑 Extra: Removing Encryption

If you want to remove the encryption applied by `service password-encryption`,  
use the command:

```cisco
Switch(config)# no service password-encryption
```
Now, when you check the running configuration again:
- The command **does not actually decrypt stored values**;
it simply stops encrypting **new or updated passwords**.
- Already encrypted ones may remain until reconfigured
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


---
---
---
## 📖 Part 2 — Local Authentication:


### 📝 Summary:
Using only passwords can lead to many problems. For example, if we have three users who need to log in to a Cisco device with different access levels, we must create **three separate users**.
Another problem that we may face is that if there are issues in the switch configuration, we won’t know who caused them because multiple users share access.
This can be solved and accomplished using **local authentication**.

### 🎯 Objectives:
- Understand why simple console passwords are insufficient when multiple users access a device.
- Learn to create multiple local user accounts with different privilege levels.
- Configure local authentication on a Cisco switch or router.
- Verify which user is logged in and track user activity.
- Recognize the benefits of using local authentication over a single shared password.

### 🧩 Topology:

<p align="center">
  <img src="topologies/port-security-1.png" alt="Port Security Topology" />
</p>

>  Agian
- One PC and one Switch 
- One PC connected to one Switch via a console cable (RS-232 → Console)

### 🛠️ Step-by-Step:
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# line console 0
Switch(config-line)# login local  
Switch(config-line)# exit
Switch(config)# username parsa password 123456   
Switch(config)# exit
Switch# write memory                    
```
| Command / Step                               | Description / Purpose                                         |
|---------------------------------------------|---------------------------------------------------------------|
| `line console 0`                             | Enter console line configuration mode                         |
| `login local`                                | Enable login using usernames & passwords                       |
| `exit`                                       | Exit console line configuration mode                           |
| `username parsa password 123456`            | Create a user named "parsa" with a password (level 0)         |
| `write memory` or `wr`                       | Save the configuration to NVRAM                                 |

### ✅ Verification:

1. **Test login on console:**
   - Disconnect and reconnect the console session.
   - When prompted, type:
     ```cisco
     Username: parsa
     Password: 123456
     ```
   - You should enter privileged or user mode depending on configuration.

2. **Check users in running configuration:**
    ```cisco
    Switch> enable
    Switch# show running-config
    ```
- You should see something like:
  ```
  username parsa password 0 123456
  line console 0
    login local
  ```
3. **Optional: Test multiple users**
- Create additional users and verify each can log in with their own credentials.

  
### 🔹Key Points
> Before we move to hashed passwords, here are some important things about local authentication:
> - User-based access: Each user can have a unique username and password, making it easier to track who did what on the device.
> - Multiple users: Avoid using a single password for everyone — local authentication allows multiple users with different credentials.
> - Plaintext warning: Using username <name> password <pass> stores the password as level 0 (plain text) — visible in show running-config.
> - Login local: Applying login local on console or VTY lines forces the device to ask for username + password instead of just a password.
> - Privilege levels: Users can be assigned privilege levels (0–15) to control access, but for now, just focus on creating separate users.

### ⚠️ Note:
Using plain passwords (level 0) **is not secure**. Later, you will learn how to use hashed passwords for stronger security.

---
### 📖 Part 2.2 — Hash / Salted Password
**📝 Summary**  
Using plaintext passwords is weak. Cisco allows hashed passwords (Type 5/9) to store credentials more securely.

💡 **Related Concepts:**
> - Learn the basics of [Hash Functions](https://github.com/zedparsa/Extra-Notes/blob/main/Security/Hash.md) — what they are and why they're used in password protection.
> - Understand [Salt & Pepper](https://github.com/zedparsa/Extra-Notes/blob/main/Security/Salt%20%26%20Pepper%20.md) — and how they make password hashes more secure.

**🎯 Objectives**
- Understand the difference between plaintext and hashed passwords.
- Learn how to configure a hashed password for a user.
- See how hashing with salt improves security.
- Compare Type 0 (plaintext) vs Type 5 (MD5) vs Type 9 (scrypt) in `show running-config`.

**🛠️ Step-by-Step**
- Configure a user with a hashed password:
```cisco
Switch> enable
Switch# configure terminal
Switch(config)# username parsa secret 123456
Switch(config)# username yazdan secret 654321
Switch(config)# line console 0
Switch(config-line)# login local
Switch(config-line)# end
```
| Command                                | Description                                                                 |
|----------------------------------------|-----------------------------------------------------------------------------|
| `username parsa secret 123456`           | Create a local user named "parsa" with a secret password (type 5 hashed)  |
| `username yazdan secret 654321`             | Create another local user named "yazdan" with a secret password              |

- Check users in running configuration:
 ```cisco
  Switch> enable
  Switch# show running-config
 ```
- Result :  
```cisco
  Switch# show running-config
  ...
  username parsa secret 5 $1$azVP$APCQAdM.O6BqTkXAgPGXB0
  username yazdan secret 5 $1$hKXA$T8bJ4ZV4nC5lQRuxdXY5v.
  
  line con 0
   login local
  ...
```

---
---
---

## 📖 Part 3 — [ TelNet & SSH ]:

### 📖 Part 3.1 — [ TelNet ]:

**📝 Summary** :  
Telnet provides remote access to network devices over an IP network, but it sends all data — including usernames and passwords — in plaintext, making it insecure.

**🎯 Objectives**:  
- Understand how Telnet remote access works
- Configure Telnet access on a Cisco device
- Use local authentication for Telnet
- Verify Telnet connectivity
- Recognize Telnet security weaknesses

**🧩 Topology**:
- One PC
- One Router or Switch
- PC connected to the device over the network
- IP addresses must already be configured on both sides

**🛠️ Step-by-Step**:
```cisco
Router> enable
Router# configure terminal
Router(config)# username admin password 123456
Router(config)# line vty 0 15
Router(config-line)# login local
Router(config-line)# transport input telnet
Router(config-line)# end
```

| Command              | Description                         |
|----------------------|-------------------------------------|
| `line vty 0 15`      | Enters virtual terminal (remote access) configuration mode   |
| `login local`                | Forces Telnet to use local usernames and passwords |
| `transport input telnet`         | Allows Telnet connections on VTY lines        |

- Why this configuration is needed:  
> Telnet requires user authentication to prevent unauthorized access.  
> Local authentication allows the device to prompt for a username and password.

**✅ Verification**:  
- From the PC command prompt:
```
telnet 192.168.1.1
```
| Command              | Description                         |
|----------------------|-------------------------------------|
| `telnet 192.168.1.1`      | Initiates a Telnet session to the device   |

- Login prompt:
```
Username: admin
Password: 123456
```

- If successful, you should see:
```
Router>
```

**⚠️ Note**:  
- Telnet does not encrypt data
- Usernames and passwords are sent in clear text
- Vulnerable to packet sniffing and man-in-the-middle attacks
- Telnet should be used only for labs and learning
- In real networks, SSH must always be used instead
---
