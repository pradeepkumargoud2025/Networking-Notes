# OSPF Overview

OSPF (Open Shortest Path First) is a **link-state routing protocol** used for exchanging routing information within an Autonomous System (AS).

Key Characteristics:
- Interior Gateway Protocol (IGP)
- Uses **Hello packets** to discover neighbors
- Builds **Link State Database (LSDB)**
- Forms **adjacency** with selected neighbors for routing updates
- Supports DR/BDR election on broadcast networks

# OSPF Neighbor States

OSPF routers pass through a series of neighbor states when forming adjacencies. 
Each state represents a step in the process of synchronizing routing information. 
When OSPF adjacency is formed, a router goes through several state changes before it becomes fully adjacent with its neighbor. Those states are defined in the OSPF RFC 2328.

## 1. Down
- This is the first OSPF neighbor state. 
- It means that no information (hellos) has been received from this neighbor, but hello packets can still be sent to the neighbor in this state.
- Router is waiting to detect the neighbor via Hello messages.

Note: In the fully adjacent neighbor state, if a router does not receive hello packet from a neighbor within the RouterDeadInterval time (RouterDeadInterval = 4*HelloInterval by default) 
or if the manually configured neighbor is removed from the configuration, then the neighbor state changes from Full to Down.

## 2. Init
- A Hello packet has been received from the neighbor, but the receiving router ID was not included in the hello packet.
- The neighbor has **not yet seen our Router ID** in its Hello.

💡 Memory Tip:
Router ID in Hello = “I exist”
Neighbor list = “I see you and acknowledge you”
Init = “I see you, but you haven’t seen me yet”

When Router A receives a Hello from Router B:

* Router B’s Hello does include B’s Router ID → so A knows “Hello, I see B exists.” ✅
* BUT B’s Hello does NOT yet include A in its neighbor list → so A cannot assume that B has acknowledged it yet. ❌

OSPF requires mutual acknowledgment before moving to Two-Way.

* Seeing the sender’s Router ID alone is not enough.
* The router needs to see its own ID in the neighbor list of the received Hello.

Step 1: Both routers start OSPF on an interface

- Router A (ID 1.1.1.1)
- Router B (ID 2.2.2.2)

Step 2: First Hello packets are sent

- Router A sends Hello → Neighbor list is empty (it hasn’t seen B yet)
- Router B sends Hello → Neighbor list is empty (it hasn’t seen A yet)

Step 3: Router A receives Hello from B

- Router A sees B’s Router ID → knows B exists
- Router A checks B’s neighbor list → its own ID (1.1.1.1) is NOT listed yet → moves to Init state

Step 4: Router B receives Hello from A

- Router B sees A’s Router ID → knows A exists
- Router B checks A’s neighbor list → its own ID (2.2.2.2) is NOT listed yet → moves to Init state

Step 5: Next Hello packets are sent

- Router A sends another Hello → now includes B in its neighbor list
- Router B sends another Hello → now includes A in its neighbor list

Step 6: Mutual acknowledgment occurs

- Router A receives B’s Hello → sees its own ID in B’s neighbor list → moves to Two-Way
- Router B receives A’s Hello → sees its own ID in A’s neighbor list → moves to Two-Way

Note: When a router receives a hello packet from a neighbor, it must list the sender router ID in its hello packet as an acknowledgment that it received a valid hello packet.

Key Condition for Init:
The neighbor's Hello must include the router’s own Router ID in the “neighbor list” to move to the next state (Two-Way).
In Init state, the router has seen the neighbor, but the neighbor hasn’t confirmed two-way communication yet.

## 3. 2-Way
- Bidirectional communication is established.
- When the router receiving the hello packet sees its own Router ID within the received hello packet neighbor field. At this state, a router decides whether to become adjacent with this neighbor.
- On broadcast media and non-broadcast multi-access networks, a router becomes full only with the designated router (DR) and the backup designated router (BDR); it stays in the 2-way state with all other neighbors.
- On Point-to-point and Point-to-multipoint networks, a router becomes full with all connected routers.
- At the end of this stage, the DR and BDR for broadcast and non-broadcast multi-access networks are elected. 

Note: Receiving a Database Descriptor (DBD) packet from a neighbor in the init state can also a cause a transition to 2-way state.

## 4. ExStart
- Once the DR and BDR are elected, the actual process of the exchange link state information can start between the routers and their DR and BDR.
- In this state, the routers and their DR and BDR establish a primary-secondary relationship and choose the initial sequence number for adjacency formation.
- The router with the higher router ID becomes the primary and starts the exchange, and as such, is the only router that can increment the sequence number.
- You would logically conclude that the DR/BDR with the highest router ID is the primary for this process. The DR/BDR election could be because of a higher priority configured on the router instead of highest router ID.
- Thus, it is possible that a DR plays a secondary role. Also, that primary/secondary election is on a per-neighbor basis.
- This ensures proper ordering of Database Description (DBD) packets.

## 5. Exchange
- In the exchange state, OSPF routers exchange database descriptor (DBD) packets. Database descriptors contain link-state advertisement (LSA) headers only and describe the contents of the entire link-state database.
- No actual routing updates yet — just describing what each router knows and Helps identify which LSAs (Link State Advertisements) each router is missing.
- Each DBD packet has a sequence number which can be incremented only by primary which is explicitly acknowledged by secondary.
- Routers also send link-state request packets and link-state update packets (which contain the entire LSA) in this state.
- The contents of the DBD received are compared to the information contained in the routers link-state database to check if new or more current link-state information is available with the neighbor.

## 6. Loading
- In this state, the actual exchange of link state information occurs. Based on the information provided by the DBDs, routers send **Link State Request (LSR)**.
- The neighbor then provides the requested link-state information in **Link State Update (LSU)**. During the adjacency, if a router receives an outdated or lost LSA, it sends a link-state request packet for that LSA. 
- All link-state update packets are acknowledged and Database is gradually synchronized during this state.

## 7. Full
- In this state, routers are fully adjacency is formed. All the router and network LSAs are exchanged and the routers' databases are fully synchronized.
- LSDB is fully synchronized between neighbors.
- Routers can now use the information to calculate routing tables using **Shortest Path First (SPF) algorithm**.
- Neighbor is fully functional for routing updates.

## Neighbors Stuck in Exstart/Exchange State ##

The problem occurs most frequently when you attempt to run OSPF between a Cisco router and another vendor router. 
The problem occurs when the maximum transmission unit (MTU) settings for neighboring router interfaces do not match. 
If the router with the higher MTU sends a packet larger that the MTU set on the neighboring router, the neighbor router ignores the packet.
