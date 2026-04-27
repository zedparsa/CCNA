
## 📖 Part — Routing Fundamentals

### 📝 Summary:
Routing is the process by which a router forwards packets toward their destinations based on information stored in its routing table. A router’s forwarding behavior depends entirely on the routes it knows. Static routes allow administrators to manually define paths, offering full control but requiring manual maintenance.

### 🎯 Objectives:
- Understand router forwarding logic based on the routing table.
- Learn how to display and analyze learned routes.
- Configure static routes using next-hop IP or exit interface.
- Understand the role of prefix length and the Longest Prefix Match rule.
- Learn how Administrative Distance (AD) affects route preference.

### 🧩 Topology:
Any two- or three-router linear topology is suitable for practicing static routing.  
Example: R1 ↔ R2 ↔ R3

---

### 🛠️ Step-by-Step:

```cisco
Router# show ip route
Router# show ip route static
```

These commands display the routing table and show only static routes respectively.

#### Command Table
| Command | Where | Purpose |
|---|---|---|
| `show ip route` | Router IOS | Displays the entire routing table and route sources |
| `show ip route static` | Router IOS | Shows only static routes currently installed |

---

### Adding Static Routes

```cisco
Router(config)# ip route <destination-ip> <subnet-mask> <next-hop-ip>
```

Or using exit interface:

```cisco
Router(config)# ip route <destination-ip> <subnet-mask> <exit-interface>
```

#### Explanation of Options
- **Next-Hop IP:** The address of the neighbor router to which packets should be forwarded.  
- **Exit Interface:** Specifies the outbound interface directly.

#### Command Table
| Command | Where | Purpose |
|---|---|---|
| `ip route <dest> <mask> <next-hop-ip>` | Router IOS (config mode) | Adds a static route using a next-hop address |
| `ip route <dest> <mask> <interface>` | Router IOS (config mode) | Adds a static route using an exit interface |

---

### Longest Prefix Match (LPM)

Routers choose the route with the **longest prefix**, meaning the most specific route.

Example:
- `192.168.0.0/16`  
- `192.168.1.0/24`  

Traffic for `192.168.1.x` → matches **/24** because 24-bit prefix is more specific.

This rule takes precedence over AD.

---

### Static Route with Administrative Distance (AD)

```cisco
Router(config)# ip route <destination> <mask> <next-hop/interface> <AD>
```

- **Lower AD = more preferred**
- **Default AD for static routes = 1**

If two static routes exist for the same prefix:
- The one with **lower AD** is installed.

But if prefixes differ:
- **Prefix length wins first**
- AD is only compared within identical-prefix routes

#### Command Table  
| Command | Where | Purpose |
|---|---|---|
| `ip route <dst> <mask> <nh/interface> <AD>` | Router IOS (config mode) | Creates a static route with custom AD |
| `show running-config \| include ip route` | Router IOS | Displays configured static routes |

---

### ✅ Verification:

```cisco
Router# show ip route
Router# show running-config | include ip route
```

#### Command Table  
| Command | Where | Purpose |
|---|---|---|
| `show ip route` | Router IOS | Verifies routes installed in the routing table |
| `show running-config \| include ip route` | Router IOS | Shows static route configuration lines |

---

### ⚠️ Note:
Static routes do not adapt to topology changes. If the next-hop becomes unreachable, the route remains in the table unless enhanced with tracking mechanisms (e.g., IP SLA + object tracking).
