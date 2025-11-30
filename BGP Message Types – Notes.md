# BGP Header (Common Header)

BGP uses four primary message types to establish, maintain, and terminate sessions.
BGP messages always start with a **19-byte common header**, regardless of the message type.

| Field   | Length     | Description                         |
|---------|------------|-------------------------------------|
| Marker  | 16 bytes   | All 1s (0xFF…) for authentication or compatibility |
| Length  | 2 bytes    | Total message length (header + body) |
| Type    | 1 byte     | BGP message type (1–4)              |

**Notes:**
- Minimum BGP message length: 19 bytes (header only, e.g., KEEPALIVE)
- Maximum length: 4096 bytes
- All BGP messages are sent over **TCP port 179**

# BGP Message Types Overview

BGP uses **four primary message types** to establish, maintain, and terminate sessions.  
All BGP messages share the 19-byte header.

| Type | Message     | Purpose                                     |
|------|-------------|---------------------------------------------|
| 1    | OPEN        | Establish BGP session, negotiate capabilities |
| 2    | UPDATE      | Advertise or withdraw routes               |
| 3    | NOTIFICATION | Error message, terminates session          |
| 4    | KEEPALIVE   | Keep session alive                          |

---

## BGP Session Flow

TCP 3-Way Handshake
↓
OPEN ↔ OPEN
↓
KEEPALIVE
↓
Exchange UPDATE messages
↓
Regular KEEPALIVE + Updates

# BGP OPEN Message (Type 1)

Used to **establish a BGP session** after TCP connection.

### Fields:

| Field                | Description                                      |
|----------------------|--------------------------------------------------|
| Version              | BGP version (usually 4)                          |
| My Autonomous System | Sender’s AS number                                |
| Hold Timer           | Negotiated hold time between peers               |
| BGP Identifier       | Router ID                                        |
| Optional Parameters  | Capabilities: MP-BGP, Route Refresh, Graceful Restart |

### Key Points:

- Sent immediately after **TCP 3-way handshake**
- Both peers must **accept capabilities**
- Mismatch triggers a **NOTIFICATION** and session is closed


# BGP UPDATE Message (Type 2)

Used to **advertise new routes** and **withdraw old routes**.

### Structure:
Withdrawn Routes Length (2 bytes)
Withdrawn Routes (variable)
Total Path Attribute Length (2 bytes)
Path Attributes (variable)
NLRI (Networks, variable)

### Path Attributes can include:

- AS-PATH  
- NEXT-HOP  
- LOCAL_PREF  
- MED  
- COMMUNITY  

### Role:

- Main message for **routing information exchange**
- Contains new prefixes (NLRI) and withdrawn prefixes

# BGP NOTIFICATION Message (Type 3)

Sent when a **BGP error** occurs. This terminates the session immediately.

### Common Triggers:

- Wrong AS number  
- Capability mismatch  
- Hold timer expired  
- Malformed packet  
- Missing mandatory attribute  

### Structure:

| Field          | Description                     |
|----------------|---------------------------------|
| Error Code     | 1 byte, indicates type of error |
| Error Subcode  | 1 byte, more detailed reason    |
| Data           | Optional, additional info       |

**Important:** Once NOTIFICATION is sent, the BGP session **closes immediately**.

# BGP KEEPALIVE Message (Type 4)

Used to **maintain session liveliness** between BGP peers.

### Key Points:

- Contains **only the 19-byte header**
- No message body
- Sent every **1/3 of Hold Timer**
- Prevents session timeout

**Example:**  
If Hold Timer = 90 sec → KEEPALIVE sent every 30 sec
