# OSPF Area Types

OSPF supports hierarchical network design using **areas**. Each area reduces LSDB size, limits SPF calculations, and controls routing updates.

## 1. Backbone Area (Area 0)
- Core of all OSPF networks.
- All other areas must connect to Area 0 (directly or via virtual link).
- Exchanges routing information between areas.

## 2. Standard/Regular Area
- Typical OSPF area.
- Accepts all LSA types (1, 2, 3, 4).
- Can connect to other areas via Area 0.

## 3. Stub Area
- Does **not accept external LSAs (Type 5)** from ASBR.
- Reduces routing table size.
- Must have a default route injected by ABR.
- LSAs inside area: Type 1 (Router), Type 2 (Network), Type 3 (Summary).

## 4. Totally Stubby Area
- Cisco proprietary.
- Blocks **Type 5 (External)** and **Type 3 (Inter-area)** LSAs.
- Only default route is injected by ABR.
- Minimizes routing table even more than Stub Area.

## 5. Not-So-Stubby Area (NSSA)
- Allows **limited external routes (Type 7)**.
- Type 7 LSAs are converted to Type 5 at ABR for other areas.
- Useful for connecting external networks without allowing all Type 5 LSAs.

## 6. Backbone-Stub or NSSA-Stubby Variants
- Can combine features for special designs (Cisco specific).

### Key Points:
- **ABR** (Area Border Router) connects areas.
- **ASBR** (Autonomous System Boundary Router) injects external routes.
- Each area maintains **its own LSDB** except for summaries from other areas.



