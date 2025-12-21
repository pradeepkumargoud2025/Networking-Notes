## Scenario 1 – BGP Neighbor Issue:

You are working on a Data Center Gateway (DCGW) router. One of your BGP neighbors in a VRF is not coming up. The neighbor is configured, but the BGP session stays in Idle/Active state.

## 1. Question:
Walk me through step-by-step how you would troubleshoot this BGP neighbor issue. What commands would you check, and what possible causes would you consider?

Ans. 
Step 1: Validate local BGP configuration

* Check BGP router ID
* Verify the remote AS number is correct
* Confirm BGP authentication if configured
* Verify the VRF name and that the neighbor is under the correct VRF

Step 2: Check neighbor reachability

* Ping the neighbor IP to ensure IP reachability
* Check the ARP table to see if the neighbor MAC is learned

Step 3: Verify BGP session status

* Use show bgp vrf ABC neighbors <neighbor-ip> detail to check
* Session state (Idle, Active, etc.)
* Timers and errors
* Messages sent/received

Step 4: Look for common causes if neighbor isn’t coming up

* Misconfigured AS number (local or remote)
* VRF mismatch or interface under wrong VRF
* ACL/Firewall blocking TCP 179
* Authentication mismatch (if MD5 is configured)
* IP connectivity issues (subnet mismatch, wrong interface)

Step 5: Advanced checks if still down

* Check BGP logs (show logging or debug bgp neighbor <IP>)
* Verify interface state (show ip interface brief)
* Check route-targets or import/export policy if in VPNv4/VRF scenario

Scenario 2 – Tricky BGP Issue:

Your BGP neighbor in a VRF is still not coming up. You’ve verified:

* Local and remote AS are correct
* IP connectivity is fine (ping works)
* VRF is correct
* No ACL/firewall blocking TCP 179

** But the BGP session flaps repeatedly, going from Idle → Active → Established → Idle, and this happens every few minutes. **

## 2. Question:
What could be the possible reasons for this flapping behavior, and what advanced commands or logs would you check to pinpoint the root cause?

Ans. 
Step 1: Consider BFD and timers

* BFD session down/up can cause BGP to flap rapidly because BGP relies on BFD for fast failure detection.

* Timer mismatch (keepalive/hold timers) between neighbors can also cause session instability.
      * Check with show bgp vrf ABC neighbors <IP> detail for timer values
      * Check BFD status with show bfd neighbors

Step 2: Check for path instability

* If routes keep changing or interface flap happens, BGP will reset.
* Verify interface stability: show interfaces <int> brief
* Check for link errors or CRC issues

Step 3: Check logs and debug

* show logging | include BGP
* debug bgp <neighbor-ip> (if permitted in lab/test environment)
* Look for TCP resets or BGP Notification messages

Step 4: Other potential causes

* Misconfigured route-maps or policies applied on neighbor
* VRF or routing loop issues
* MTU mismatch (less common, but can cause flaps if TCP fails)

## 3. Question:
BGP Neighbor Not Establishing Your eBGP neighbor is in Idle state even after checking AS, IP, and connectivity. What could be advanced causes and steps?

Ans.
If eBGP remains in Idle despite correct AS, IP, and basic connectivity, I look at advanced causes:

* TCP 179 blocked by firewall / control-plane policing (CoPP)
* TTL issue (especially with eBGP multihop not configured)
* Source interface mismatch (update-source vs peer expectation)
* BGP authentication mismatch (MD5 key)
* BGP capability mismatch (AFI/SAFI not activated)
* Maximum-prefix exceeded previously → session suppressed
* Neighbor shutdown / disable

## 4. Question: 
Route Reflector Loop Explain how a BGP route reflector loop occurs and how you prevent it.

Ans. 
RR loop occurs when reflected routes re-enter the RR and get reflected again.

Why it happens:
* Multiple RRs in cluster
* Cluster-ID misconfiguration
* Misconfigured client / non-client relationship

Prevention:
* Cluster-ID (mandatory)
* Originator-ID
* Split RR clusters or use confederation

## 5. Question: 
BGP Flap due to Route Policy Your iBGP neighbor keeps flapping when you apply a route-map. How do you troubleshoot?

Ans.
If session flaps after applying route-map:

Possible causes:
* Route-map denies all prefixes
* Missing permit statement
* AS-path / next-hop manipulation causing withdrawal
* set local-preference applied outbound (invalid)
* match condition never matches

## 6. Question: 
BGP Path Selection Explain in detail the BGP path selection process, including all tie-breakers.

Ans. 
BGP selects best path in this order:

* Weight (Cisco-local)
* Local Preference
* Locally originated
* AS-Path length
* Origin type (IGP < EGP < Incomplete)
* MED (same AS)
* eBGP over iBGP
* Lowest IGP metric to next-hop
* Oldest path
* Lowest Router ID
* Lowest neighbor IP

## 7. Question: 
BGP Route Reflector & Confederation Explain the differences and scenarios where RR or Confederation is preferred.

Ans. 
| Feature      | Route Reflector | Confederation  |
| ------------ | --------------- | -------------- |
| Complexity   | Low             | Medium         |
| AS Path      | Hidden          | Visible sub-AS |
| Loop Control | Cluster-ID      | Sub-AS         |
| Scalability  | Medium          | Very High      |

Use RR: Smaller core, operational simplicity
Use Confederation: Massive ISP-scale networks

## 8. Question: 
BGP with Multiple VRFs You have BGP running in multiple VRFs on the same router. One neighbor is not receiving routes. How do you troubleshoot?

Ans.
If one VRF neighbor doesn’t receive routes:

Check:

* Correct VRF context
* AF activated under VRF
* Route leaking policy
* RT import/export
* Next-hop reachability in VRF

## 9. Question: 
BGP Dampening When and why would you use BGP route dampening? Explain parameters and impact.

Ans. 
Used to suppress unstable routes.

Key Parameters:
* Penalty
* Suppress-limit
* Reuse-limit
* Half-life

Impact:
* Reduces churn
* May delay legitimate routes
* Typically avoided today unless instability is severe.

## 10. Question: 

BGP Next-Hop Issue Your eBGP routes are received but traffic is not flowing. Next-hop is unreachable. How do you resolve?

Ans.
Routes received but traffic fails → next-hop unreachable.

Fixes:
* next-hop-self (iBGP)
* Ensure IGP reachability
* Use ebgp-multihop when needed

## 11. Question: 
BGP Policy Troubleshoot After applying route-map outbound, certain prefixes disappear. How do you debug?

Ans. 
* Verify route-map sequence order
* Check prefix-list logic
* Confirm outbound vs inbound
* Look for implicit deny





