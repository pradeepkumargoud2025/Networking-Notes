# OSPF DR/BDR Election

DR (Designated Router) and BDR (Backup DR) are elected on **broadcast and NBMA networks** to reduce LSDB flooding.

## Election Rules:
1. Router with **highest OSPF priority** becomes DR.
2. If priorities are equal, **highest Router ID** is chosen.
3. BDR is next highest after DR.
4. Routers with priority 0 cannot become DR/BDR.

## Key Points:
- Non-DR/BDR routers form adjacency **only with DR/BDR**.
- Point-to-point links do not elect DR/BDR.
- Election occurs when interface comes up or DR/BDR fails.

# OSPF Full Adjacency

Full adjacency is achieved when:
1. OSPF neighbors progress through all neighbor states.
2. LSDB is fully synchronized.
3. SPF algorithm calculates shortest path routes.

## Conditions:
- On point-to-point links: Full adjacency occurs between two routers.
- On broadcast/NBMA networks: Full adjacency occurs **only between router and DR/BDR**.
- Non-DR routers have **2-Way state** with other non-DR neighbors.

## Benefits:
- Routers have identical LSDB for SPF calculation.
- Ensures correct routing paths across the OSPF domain.


- Verifies OSPF-learned routes in routing table.

## Common Troubleshooting Steps:
1. Verify Hello and Dead intervals match.
2. Check OSPF area IDs are correct.
3. Verify interface is OSPF-enabled.
4. Confirm no passive-interface misconfiguration.
5. Check for mismatched OSPF timers or MTU issues.

