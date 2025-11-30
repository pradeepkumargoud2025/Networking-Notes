BGP uses four primary message types to establish, maintain, and terminate sessions.
All messages share the same 19-byte BGP header:

Field	Length	Description
Marker	16 bytes	All 1s (authentication placeholder)
Length	2 bytes	Total message length
Type	1 byte	Message type (1–4)
🧱 BGP Message Types Overview
✅ 1. OPEN (Type 1)

Used to establish a BGP session after TCP handshake.

Fields:
Field	Description
Version	Usually 4
My Autonomous System	Sender’s AS
Hold Timer	Negotiated between peers
BGP Identifier	Router ID
Optional Parameters	Capabilities (MP-BGP, Route Refresh, Graceful Restart, etc.)
When is OPEN sent?

Immediately after TCP port 179 handshake

Both peers must accept capabilities

If mismatch, a NOTIFICATION is sent

✅ 2. UPDATE (Type 2)

Carries routing information (new routes or withdrawn routes).

Structure:
Withdrawn Routes Length
Withdrawn Routes
Total Path Attribute Length
Path Attributes
NLRI (Networks)

UPDATE can contain:

New prefixes

Withdrawn prefixes

Path attributes such as:

AS-PATH

NEXT-HOP

LOCAL_PREF

MED

COMMUNITY

Role:

Main message for advertising changes when routing topology updates

❗ 3. NOTIFICATION (Type 3)

Sent when a BGP error occurs.

Triggers:

Wrong AS number

Capability mismatch

Hold timer expired

Malformed packet

Missing mandatory attribute

Important:

After sending NOTIFICATION, BGP session is terminated.

🔁 4. KEEPALIVE (Type 4)

Used to maintain session liveliness.

Key Points:

Contains only the 19-byte header

No body

Sent every 1/3 of Hold Time

Prevents session timeout

Example:
If Hold Timer = 90 seconds → Keepalive every 30 sec.

📡 Message Type Summary Table
Type	Message	Purpose
1	OPEN	Establish BGP session, negotiate capabilities
2	UPDATE	Advertise or withdraw prefixes
3	NOTIFICATION	Error message, terminates session
4	KEEPALIVE	Keep session active
🔄 Full BGP Session Establishment Flow
TCP 3-Way Handshake
        ↓
   BGP OPEN  →  OPEN
        ↓
   KEEPALIVE
        ↓
   UPDATE Exchange
        ↓
Regular KEEPALIVE + Updates
