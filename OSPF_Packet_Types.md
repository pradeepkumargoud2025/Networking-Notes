# OSPF Packet Types

OSPF routers use five packet types to exchange routing and topology information.

## 1. Hello (Type 1)
- Used to discover neighbors and maintain adjacency.
- Contains:
  - Router ID
  - Hello interval / Dead interval
  - Area ID
  - Options
- Sent periodically on all OSPF-enabled interfaces.

## 2. Database Description (DBD) (Type 2)
- Exchanged after neighbor reaches ExStart state.
- Summarizes contents of LSDB.
- Helps routers identify missing LSAs.
- Master/Slave negotiation occurs before DBD exchange.

## 3. Link-State Request (LSR) (Type 3)
- Sent to request specific LSAs that the router does not have or has outdated.
- Helps synchronize LSDB with neighbors.

## 4. Link-State Update (LSU) (Type 4)
- Response to LSR packets.
- Contains one or more LSAs.
- Updates the neighbor’s LSDB.

## 5. Link-State Acknowledgment (LSAck) (Type 5)
- Confirms receipt of LSAs.
- Ensures reliable delivery of LSAs.
- Can be sent individually or as a list of LSAs.

### Key Points
- All OSPF packets are encapsulated in IP Protocol 89.
- Hello packets maintain neighbor relationships.
- DBD, LSR, LSU, and LSAck are used to **synchronize LSDB** between routers.
- Proper sequence ensures SPF calculations and full network convergence.
