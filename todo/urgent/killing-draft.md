# Killing draft

**Done:** everything below is now in situations.md.

Draft of what would go into [situations.md](../../situations.md) to say what can kill a player, and when.
It mirrors the sobriety structure: a cause predicate, a closed list of causes, and Role Invariants rows per killing role.

## Problem

Nothing says what causes a death. In [example.md](../../example.md), C's night 2 death has no required cause, and nothing fixes when in night 2 C died. So E's night 2 Empath neighbours could be A and C (if C died after position 65) instead of A and B. The example has to assume "only the Imp kills at night".

## New predicate and notation

| Claim | Meaning |
| :--- | :---: |
| Kills(S, A, $\tau$) | Source $S$ kills player $A$ at time $\tau$. This is the successful kill; the attempt is $\text{Chose}$, and protection decides whether an attempt becomes a kill |

Let $\mathcal{K}$ be the set of killing sources on the script. A source is a role, or Execution (not a role). Trouble Brewing: $\mathcal{K} = \{\text{Imp}, \text{Slayer}, \text{Execution}\}$. The Virgin's effect is an execution, so it is covered by Execution.

## Structural Invariants

| Invariant | Statement |
| :--- | :---: |
| Died only if killed | $\text{Died}(A, \tau) \leftrightarrow \bigvee_{S \in \mathcal{K}} \text{Kills}(S, A, \tau)$ <br> **The "$\leftrightarrow$" is what makes this constraining: a player dies only if a listed source kills them, and only at a time that source can act.** |
| At most one kill per source per time | $\sum_{A \in \mathcal{P}} [\text{Kills}(S, A, \tau)] \le 1$ |

## Role Invariants

### Imp
$k_{\text{Imp}}$ is the Imp's position on night $t$ (30 on later nights; the Imp does not act on night 1).

| Invariant | Statement |
| :--- | :---: |
| Must choose | $\text{Active}(X, \text{Imp}, (t, k_{\text{Imp}})) \rightarrow \sum_{Y \in \mathcal{P}} [\text{Chose}(X, Y, (t, k_{\text{Imp}}))] = 1$, for $t > 1$ <br> **The Imp must choose a player each night. So a night with no deaths means the target was protected or already dead, or the Imp was not sober.** |
| Kills only on its turn | $\text{Kills}(\text{Imp}, A, \tau) \rightarrow \tau = (t, k_{\text{Imp}}) \land t > 1 \land \bigvee_{X \in \mathcal{P}} \text{Active}(X, \text{Imp}, \tau)$ <br> **A drunk or poisoned Imp kills no one, since $\text{Active}$ needs $\text{Sober}$.** |
| Target dies unless protected | $\left(\text{Active}(X, \text{Imp}, \tau) \land \text{Chose}(X, A, \tau) \land \text{Alive}(A, \tau) \land \neg\text{Safe}(A, \tau) \land \neg(\text{Role}(A, \text{Soldier}, \tau) \land \text{Sober}(A, \tau)) \land \neg(\text{Role}(A, \text{Mayor}, \tau) \land \text{Sober}(A, \tau))\right) \rightarrow \text{Kills}(\text{Imp}, A, \tau)$, for $\tau = (t, k_{\text{Imp}})$ |
| Only the target dies | $\text{Kills}(\text{Imp}, A, \tau) \rightarrow \bigvee_{X \in \mathcal{P}} \Big(\text{Active}(X, \text{Imp}, \tau) \land \big(\text{Chose}(X, A, \tau) \lor \bigvee_{M \in \mathcal{P}} (\text{Chose}(X, M, \tau) \land \text{Role}(M, \text{Mayor}, \tau) \land \text{Sober}(M, \tau))\big)\Big)$ <br> **The second case is the Mayor: when a sober Mayor is chosen, the Mayor or another player may die instead, at the Storyteller's choice. "Target dies unless protected" leaves the Mayor out, so either outcome (or the redirected player also being protected) is allowed.** |

**Deferred:** the star-pass. If the Imp chooses itself, "Only the target dies" already lets it die, but the Minion becoming the Imp needs the role changes that "Roles persist" does not allow yet.

### Monk
$\text{Safe}$ is read by the Imp's "Target dies unless protected" row, so it must be closed too; otherwise a solver could make anyone Safe and block every kill.

| Invariant | Statement |
| :--- | :---: |
| Safe only when chosen by an active Monk | $\text{Safe}(A, (t, k)) \rightarrow \bigvee_{X \in \mathcal{P}} \left(\text{Active}(X, \text{Monk}, (t, k_{\text{Monk}})) \land \text{Chose}(X, A, (t, k_{\text{Monk}})) \land X \neq A\right) \land k > k_{\text{Monk}}$, for $t > 1$ <br> **$k_{\text{Monk}}$ is 15 on later nights. The Monk cannot choose themselves.** |

### Slayer

| Invariant | Statement |
| :--- | :---: |
| Kills only the Demon (or a Recluse) | $\text{Kills}(\text{Slayer}, B, (t, D)) \rightarrow \bigvee_{X \in \mathcal{P}} \left(\text{Active}(X, \text{Slayer}, (t, D)) \land \text{Chose}(X, B, (t, D)) \land \left(\text{Demon}(B, (t, D)) \lor (\text{Role}(B, \text{Recluse}, (t, D)) \land \text{Sober}(B, (t, D)))\right)\right)$ |

### Execution (not a role)

| Invariant | Statement |
| :--- | :---: |
| Execution kills | $\text{Kills}(\text{Execution}, A, (t, D)) \leftrightarrow \text{Executed}(A, t)$ <br> **True in Trouble Brewing. Scripts with a Zombuul, Fool, Devil's Advocate or Tea Lady weaken this to "$\rightarrow$" plus their own exceptions.** |
| Executions only by day | $\text{Kills}(\text{Execution}, A, \tau) \rightarrow \tau = (t, D)$ |

## Changes to existing rows

- **General Event "A is executed on day t and dies":** follows from "Execution kills" and "Died only if killed", so it can shrink to just $\text{Executed}(A, t)$.
- **Role-Event "A claims Slayer, shoots B on day t (B dies)":** becomes $\text{Kills}(\text{Slayer}, B, (t, D))$. The Slayer row above then supplies the rest.
- **Role-Event "A claims Slayer, shoots B on day t (B lives)":** unchanged.

## Effect on example.md

- C's night 2 death forces $\text{Kills}(S, C, \tau)$ for some $S \in \mathcal{K}$. The only night source is the Imp, so $\tau = (2, 30)$, an active Imp chose C, and the Imp was alive and sober.
- So C is dead at $(2, 65)$, and E's night 2 neighbours are A and B. The "Only the Imp kills at night" assumption is no longer needed.
- The Imp is not D (dead at $(2, 30)$).
- **Watch out:** C being the Imp and choosing itself (a star-pass) is *allowed* by these rows and by "The game is still going" (a living Minion exists). "Roles persist" only stops the Minion becoming the Imp; it does not stop the star-pass itself. So until role changes are modelled, the example either keeps "C is not the Imp" as an assumption or gains star-pass worlds (with no Imp afterwards).
