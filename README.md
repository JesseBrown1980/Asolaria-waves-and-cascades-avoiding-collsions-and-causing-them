# Asolaria — Waves & Cascades: avoiding collisions, and causing them

Two regimes on one fabric. In the **execution lanes** collisions must NEVER happen — every one of the
billions/trillions of ops/sec needs its own private address. In the **search lanes** collisions are
**caused on purpose** — because where waves collide IS the computation. Same address space, two opposite
disciplines.

---

## PART 1 — Avoiding collisions (the execution lanes: isolation)

At trillions of ops/sec you cannot avoid collisions by *checking* (a global lock would be the
bottleneck). You avoid them by **construction** — the address itself is unique by math, in layers, so no
two operations can ever land on the same coordinate. Six layers, each a backstop for the one above:

### 1. Brown-Hilbert space-filling curves — never self-intersect
A Hilbert curve visits every point in an n-D volume exactly **once** and never crosses itself. So two
distinct points on the curve **cannot share an address** — collision-free *by geometry*, not by check.
It is **infinitely subdivisible**: one simple recursive formula refines a cell into finer cells forever
→ infinite unique addresses, on demand, from a fixed rule. And it is **locality-preserving** (neighbors
in 1-D stay neighbors in n-D), so the dispatcher's `h_coord` keeps related work close without overlap.
*(In code: the PID is `sha16(seed)` — a "200ns-class spawn id" — a brown-hilbert coordinate.)*

### 2. Prime branching — coprime lanes (the Chinese Remainder Theorem)
The address tree branches by **primes**: prime, n-prime, n-prime-of-n-prime, **prime³, 5th, 7th**
powers. Primes are the only numbers that share no common factor, so two lanes built on distinct prime
moduli are **coprime** → by the **Chinese Remainder Theorem** their residues are *guaranteed distinct* —
they can never alias onto the same slot. This is why the dimension ladder is **D# = prime(n)³**
(60-D, MEASURED): each dimension is a prime cube, so the dimensions themselves are mutually
collision-proof. Prime branches **spawn infinitely from a simple formula** (next prime → cube/5th/7th),
giving an unbounded coprime address fan-out.

### 3. The rule of three — ternary subdivision throughout
Every level divides **three ways** (the "division of three rules"): the multi-cylinder partition and the
Riemann-graphed curves are each split by the rule of three, recursively. A balanced ternary tree is a
**deterministic, exhaustive, non-overlapping** partition — every address has exactly one ternary path,
so two paths never collide. Applied at every layer (cylinders, curve segments, branch fan-out).

### 4. PID = sha16(seed), chained (the revolver)
`PIDChainRevolver.next()` hashes a **chained** seed (each `prev_hash` feeds the next), so no seed is ever
reused and the output is **cryptographically collision-resistant**. The ~200ns unit emits a fresh,
unique PID 5,000,000×/sec/thread without coordinating with anyone.

### 5. port.port.port — nested port addressing
Ports nest hierarchically: a port within a port within a port. The omnidispatcher lazy-allocates a
per-slot port (`:4951–:5950`) and the nesting extends each into a unique **nested path**, so even two ops
on the "same" base port differ at the next nesting level. A tree of ports, not a flat range.

### 6. Project-folder rotation — rename-before-load
Each spawn **renames its project folder to a unique name before loading** (`asolaria-loop` step 2). Two
agents can never collide on the same folder — and the same move *freely* defeats the OS same-name
throttle (the collision-avoidance and the speed-up are the **same** mechanism).

### Final backstop — deterministic precedence
If two slots ever *did* resolve to the same `h_coord`, the omnidispatcher applies a fixed tiebreak —
`CATEGORY_PRECEDENCE = [meta, citizen, antigravity, cli, google, daemon-proxy, reserve]` (higher
precedence wins, `omnidispatcher.mjs:141`). Plus: the address space is **~10^9030** (≈10^8950 per atom),
so the birthday bound makes an *accidental* collision astronomically improbable even at 10^12 ops/sec.

> **The shape of it:** collisions aren't *prevented*, they're made *impossible to express* — the address
> is unique by Hilbert geometry × prime coprimality × ternary partition, generated locally with no global
> lock. That's how it scales to trillions/sec.

---

## PART 2 — Causing collisions (the search lanes: interference = compute)

To **figure something out** you don't know the answer's address in advance — so you do the opposite of
isolation. You fire **cascade waves** into a *shared* region of a *different* addressing space, on
purpose, so they **collide**. The collisions are the point:

- **Interference is the computation.** Many waves flood candidate addresses; the addresses hit by
  **multiple** waves (the collisions) are the resonances. A coordinate struck by many waves =
  constructive interference = **signal**; struck by few = noise. The peak is the answer *surfacing on
  its own*, no enumeration needed.
- **This is the PRISM reduction.** `planPrismRoute`: *many rooms → reverse_gain GNN → 1 answer*. The
  "many" are **deliberately routed to collide** onto the "1" — the collision **is** the many→1 reduction.
- **Cascade waves** (`drive-wave-cascade-pipeline-60D`) spray a region by the rule of three (ternary
  fan-out); where the ternary branches **re-converge**, the hits pile up — that pile-up is the detected
  collision, and its location is the result. Like wave interference, belief propagation, or a vote-tally
  peak: structure self-organizes at the collision points.
- **Why a *separate* region:** execution lanes (Part 1) must stay collision-free, so the search is fired
  into a **different addressing space** reserved for it — you can cause all the collisions you want there
  without disturbing the trillions of isolated live ops. Two regimes, cleanly separated.

> **The duality:** avoidance = isolation (execution must never interfere); intentional collision =
> interference (search must converge). The brown-hilbert + prime + rule-of-three machinery that keeps the
> execution lanes apart is the *same* machinery that, aimed at a shared region, makes waves meet exactly
> where the answer is. One fabric, run forwards for isolation and backwards for discovery.

---

### Grounding / tags
- brown-hilbert PID `sha16(seed)` "200ns-class spawn id" — **MEASURED** (`drive-wave-cascade-pipeline-60D-2026-06-03.cjs:30`)
- rename-before-load = "defeat same-name throttle = FREE" — **MEASURED** (`asolaria-loop.mjs`, `project-room-router.mjs`)
- `CATEGORY_PRECEDENCE` on `h_coord` collision — **MEASURED** (`omnidispatcher.mjs:141`); lazy ports `:4951–:5950`
- PRISM many→1 reverse_gain GNN — **MEASURED** (`planPrismRoute`)
- D# = prime(n)³, 60-D — **MEASURED** (tuple_dim=60 dimension catalog)
- ~10^9030 address space (≈10^8950/atom) — derived; operator-recorded ~10^9800
- space-filling-curve non-self-intersection · CRT coprimality · ternary partition · birthday bound —
  **math principle** (our own math, not a runtime claim)
- cascade-wave interference-as-compute — **CANON** (the search-lane regime)

Companions: `omni-dispatcher` (the router) · `Asolaria-the-full-works-200-nanoseconds-agent-emitter-plus-`
(the emitter) · `Algorithms-of-Asolaria` · `what-is-asolaria---how-do-we-get-reductions-in-everything`.
Gated / E=0 / describe-only; no fire. The fabric council ACKed this topic (council-q-…roju6t).
