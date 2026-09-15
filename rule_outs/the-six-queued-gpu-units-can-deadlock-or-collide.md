---
created_at: '2026-09-01T08:59:40+00:00'
former_ids: []
id: the-six-queued-gpu-units-can-deadlock-or-collide
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T08:59:40+00:00'
topic_tags: []
---

## Claim
The six queued GPU units are at risk of deadlock or collision -- eiv-v39 in particular waits on nothing and could start on a busy card, and eiv-seedfam2/eiv-v37 carry no GPU-busy guard.

## Method
Parsed each unit's ExecStart script out of systemctl show, extracted its wait conditions (both directly-named and loop-iterated), built the dependency graph, searched it for cycles, and checked every active pair for a missing ordering edge plus the presence of an nvidia-smi busy guard. Read-only. Script: scratch/_queue_deadlock_audit.py.

## Evidence
REFUTED, AND THE ALARM CAME FROM MY OWN AUDIT SCRIPT. The first version detected wait edges by matching `is-active ... <unit-name>` on a single line. run_v39_scaled.sh waits with
    for u in eiv-seedfam2 eiv-v37 eiv-v38 eiv-r2floor eiv-geomfloor; do
      while systemctl is-active --quiet "$u.service"; do sleep 60; done
    done
so the unit names sit on the `for` line while the is-active call names only $u. The scanner reported 'eiv-v39 waits on (nothing)' and flagged two collision pairs. Acting on that would have meant editing a script that was already correct, and possibly restarting a unit -- which is exactly how eiv-brace's stop released the seed family's wait condition earlier today.

After teaching the scanner to read loop-iterated dependencies, the real graph is a strict chain:
  eiv-seedfam2   waits on (nothing)                                    gpu-guard NO
  eiv-v37        waits on seedfam2                                     gpu-guard NO
  eiv-v38        waits on seedfam2, v37                                gpu-guard yes
  eiv-r2floor    waits on seedfam2, v37, v38                           gpu-guard yes
  eiv-geomfloor  waits on seedfam2, v37, v38, r2floor                  gpu-guard yes
  eiv-v39        waits on all five                                     gpu-guard yes

  DEADLOCK CHECK  : no cycles, the queue can drain
  COLLISION CHECK : every active pair is ordered by an explicit wait

The two units without a GPU-busy guard are safe for a different reason than the others: eiv-seedfam2 is already running (it holds the card), and eiv-v37 waits on it and is the only successor at its level, so there is no second claimant to race.

WHAT SURVIVES AS A REAL WEAKNESS: the ordering is carried entirely by NAME MATCHING inside shell scripts. A future unit spelled differently -- the eiv-brace / eiv-brace2 case that already bit once -- would be invisible to every waiter. systemd has After=/Conflicts= for exactly this, and the scripts do not use it. Not changed now: editing six live waiters while they poll is a larger risk than the one it removes, and the queue is verified correct as it stands.

METHOD RULE, second instance today: when a checker reports a defect in something that was just written carefully, suspect the checker. The earlier instance was a regex over vault YAML that shifted every diagnostic attribution by one and reported 12 corrupt diagnostics; the truth was 1.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
