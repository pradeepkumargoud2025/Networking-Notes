# 🧭 BGP Attributes

**Border Gateway Protocol (BGP)** uses various attributes to determine the **best path** for routing traffic between autonomous systems (AS).  
Each attribute carries specific routing information and helps routers make intelligent decisions during path selection.

---

## 🔹 Types of BGP Attributes

| Attribute Type | Description |
|----------------|--------------|
| **Well-known Mandatory** | Must be recognized and included in every BGP update. |
| **Well-known Discretionary** | Recognized by all BGP implementations but optional in updates. |
| **Optional Transitive** | May or may not be recognized, but if unrecognized, is passed along to other peers. |
| **Optional Non-Transitive** | May not be recognized and is **not** passed to other peers. |

---

## 🔸 Common BGP Attributes

| Attribute | Type | Description |
|------------|------|-------------|
| **1. ORIGIN** | Well-known Mandatory | Indicates the origin of the route — `IGP (i)`, `EGP (e)`, or `Incomplete (?)`. IGP is preferred over EGP, and EGP over Incomplete. |
| **2. AS_PATH** | Well-known Mandatory | Lists AS numbers the route has traversed. Used to prevent routing loops and prefer shorter paths. |
| **3. NEXT_HOP** | Well-known Mandatory | Specifies the next-hop IP address to reach the destination network. |
| **4. LOCAL_PREFERENCE** | Well-known Discretionary | Indicates the preferred path for outbound traffic within an AS. Higher value = higher preference. |
| **5. MED (MULTI_EXIT_DISC)** | Optional Non-Transitive | Suggests preferred entry points into an AS for inbound traffic. Lower value = more preferred. |
| **6. ATOMIC_AGGREGATE** | Well-known Discretionary | Indicates that route summarization has occurred. |
| **7. AGGREGATOR** | Optional Transitive | Identifies the router and AS that performed route aggregation. |
| **8. COMMUNITY** | Optional Transitive | Used for tagging routes with metadata to control routing policy. |
| **9. EXTENDED COMMUNITY** | Optional Transitive | Extends the Community attribute with more granular control (used in MPLS VPNs). |
| **10. ORIGINATOR_ID** | Optional Non-Transitive | Identifies the route reflector that originated the route (used in Route Reflection). |
| **11. CLUSTER_LIST** | Optional Non-Transitive | List of route reflector cluster IDs traversed (prevents routing loops in clusters). |
| **12. AIGP (Accumulated IGP Metric)** | Optional Non-Transitive | Carries IGP metric information across AS boundaries to support end-to-end path selection based on IGP cost. |

---

## ⚙️ BGP Path Selection Order

When multiple routes exist to the same destination, BGP uses the following sequence to select the **best path**:

1. Prefer the **highest LOCAL_PREFERENCE**  
2. Prefer the route that was **locally originated** (via network/aggregate/redistribute)  
3. Prefer the **shortest AS_PATH**  
4. Prefer the **lowest ORIGIN type** (`IGP < EGP < Incomplete`)  
5. Prefer the **lowest MED** (if paths are from the same neighboring AS)  
6. Prefer **eBGP over iBGP**  
7. Prefer the path with the **lowest IGP metric** to the NEXT_HOP  
8. Prefer the **oldest path** (to reduce route flaps)  
9. Prefer the path from the **router with the lowest BGP Router ID**  
10. Prefer the path with the **lowest neighbor IP address** (if Router IDs are identical)

---

## 🧩 Notes

- BGP attributes can be **manipulated** using **route maps, policies, or filters** to control inbound and outbound routing decisions.  
- Some attributes are **local to a router or AS** (e.g., `WEIGHT`, `LOCAL_PREF`) and are not advertised to peers.  
- Other attributes like **AS_PATH**, **MED**, and **COMMUNITY** are **propagated** and influence inter-AS routing behavior.  

---

### ✍️ Example Use Cases

- **Traffic Engineering:** Adjust `LOCAL_PREF` or `MED` to control preferred paths.  
- **Loop Prevention:** `AS_PATH` ensures routes don’t re-enter the same AS.  
- **Policy Tagging:** Use `COMMUNITY` values to group or filter routes.  


🏷️ **Category:** Routing — BGP Deep Dive
