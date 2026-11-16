# Role change draft

**Done:** everything below except Fang Gu is now in situations.md. Fang Gu stays here as reference for other scripts.

Draft of what would go into [situations.md](../../situations.md) to let roles and alignments change over time: the Imp's star-pass and the Scarlet Woman on Trouble Brewing, built so Fang Gu, Pit-Hag, Barber, Snake Charmer and others fit later.
It mirrors the sobriety and killing structure: a cause predicate, a closed list of causes, and Role Invariants rows per role.

A dead player keeps their role. After a star-pass, the dead player is still the Imp (a Ravenkeeper or Undertaker would see Imp), so two players can hold the same role as long as at most one of them is alive.

## Problem

"Roles persist (temporary)" and "Alignment persists (temporary)" forbid every change, and "No two players share a role" forbids a dead Imp and a living Imp at once. So the star-pass and the Scarlet Woman cannot happen, and example.md has to assume D and C are not the Imp.

## New predicates and notation

| Claim | Meaning |
| :--- | :---: |
| RoleChanged(S, A, R, $\tau$) | Source $S$ changes player $A$'s role to $R$ at time $\tau$. Takes effect at $\text{next}(\tau)$, like $\text{Died}$ |
| AlignChanged(S, A, M, $\tau$) | Source $S$ changes player $A$'s alignment to $M$ at time $\tau$. Takes effect at $\text{next}(\tau)$ |

Let $\mathcal{C}$ be the set of roles on the script whose ability can change a player's role. Trouble Brewing: $\mathcal{C} = \{\text{Imp}, \text{Scarlet Woman}\}$.<br>
Let $\mathcal{A}$ be the set of roles on the script whose ability can change a player's alignment. Trouble Brewing: $\mathcal{A} = \emptyset$ (a Minion who catches the star-pass is already evil, and a Recluse who catches it keeps their alignment).

## Structural Invariants

### Replaces "Roles persist (temporary)" and "Alignment persists (temporary)"

| Invariant | Statement |
| :--- | :---: |
| Roles change only by a listed source | $\text{Role}(A, R, \text{next}(\tau)) \leftrightarrow \left(\text{Role}(A, R, \tau) \land \neg\bigvee_{S \in \mathcal{C},\, R' \neq R} \text{RoleChanged}(S, A, R', \tau)\right) \lor \bigvee_{S \in \mathcal{C}} \text{RoleChanged}(S, A, R, \tau)$ |
| At most one new role per player per time | $\sum_{S \in \mathcal{C}} \sum_{R} [\text{RoleChanged}(S, A, R, \tau)] \le 1$ |
| Alignments change only by a listed source | $\text{Align}(A, M, \text{next}(\tau)) \leftrightarrow \left(\text{Align}(A, M, \tau) \land \neg\bigvee_{S \in \mathcal{A},\, M' \neq M} \text{AlignChanged}(S, A, M', \tau)\right) \lor \bigvee_{S \in \mathcal{A}} \text{AlignChanged}(S, A, M, \tau)$ <br> **With $\mathcal{A} = \emptyset$ on Trouble Brewing, this reduces to "alignment never changes".** |
| At most one new alignment per player per time | $\sum_{S \in \mathcal{A}} \sum_{M} [\text{AlignChanged}(S, A, M, \tau)] \le 1$ |

### Replaces "No two players share a role"

| Invariant | Statement |
| :--- | :---: |
| No two living players share a role | $\text{Role}(A, R, \tau) \land \text{Role}(B, R, \tau) \land \text{Alive}(A, \tau) \land \text{Alive}(B, \tau) \rightarrow A = B$ <br> **Everyone is alive at setup, so setup roles are still unique. Roles with several living holders (Legion, Riot, Village Idiot) will need their own exception.** |

## Role Invariants

### Imp (star-pass)

| Invariant | Statement |
| :--- | :---: |
| Passes on self-kill | $\left(\text{Role}(X, \text{Imp}, \tau) \land \text{Kills}(\text{Imp}, X, \tau) \land \bigvee_{M \in \mathcal{P}} (\text{Alive}(M, \tau) \land \text{Registers}(M, \text{Minion}, \tau))\right) \rightarrow \sum_{M \in \mathcal{P}} \left[\text{RoleChanged}(\text{Imp}, M, \text{Imp}, \tau) \lor \text{RoleChanged}(\text{Scarlet Woman}, M, \text{Imp}, \tau)\right] = 1$ <br> **Exactly one living player who registers as a Minion becomes the Imp, either through the star-pass or through the Scarlet Woman's own ability. With no such player, no one does, and the game ends.** |
| Only to a living player who registers as a Minion | $\text{RoleChanged}(\text{Imp}, M, R, \tau) \rightarrow R = \text{Imp} \land \text{Alive}(M, \tau) \land \text{Registers}(M, \text{Minion}, \tau) \land \bigvee_{X \in \mathcal{P}} \left(\text{Role}(X, \text{Imp}, \tau) \land \text{Kills}(\text{Imp}, X, \tau) \land X \neq M\right)$ <br> **$\text{Registers}$ covers the Recluse: a sober Recluse may register as a Minion, so the Storyteller may pass the Demon to them. A Recluse keeps their alignment, so a Good Recluse becomes a Good Imp. They would out themselves and be executed for a Good win, so in that case there is no need to use the solver.** |

### Scarlet Woman

| Invariant | Statement |
| :--- | :---: |
| Becomes the Demon | $\text{RoleChanged}(\text{Scarlet Woman}, M, R, \tau) \leftrightarrow \text{Active}(M, \text{Scarlet Woman}, \tau) \land \text{Count}(\{Z : \text{Alive}(Z, \tau)\}) \ge 5 \land \bigvee_{X \in \mathcal{P}} \left(\text{Demon}(X, \tau) \land \text{Role}(X, R, \tau) \land \text{Died}(X, \tau)\right)$ <br> **Forced, not optional, so "$\leftrightarrow$". She becomes the same Demon character that died. The count is taken at $\tau$, when the dying Demon is still alive, so it includes the Demon. Works for a death by day (execution, Slayer) or by night (star-pass).** |
| Takes priority over the star-pass | $\text{RoleChanged}(\text{Scarlet Woman}, M, R, \tau) \rightarrow \bigwedge_{M' \in \mathcal{P}} \neg\text{RoleChanged}(\text{Imp}, M', R, \tau)$ <br> **If the Imp star-passes while her ability fires, she is the one who becomes the Imp.** |

### Fang Gu (future; shows the structure handles alignment changes and jumps)

| Invariant | Statement |
| :--- | :---: |
| Jumps to the first Outsider it attacks | $\left(\text{Active}(X, \text{Fang Gu}, \tau) \land \text{Chose}(X, O, \tau) \land \text{Registers}(O, \text{Outsider}, \tau) \land \bigwedge_{\tau' < \tau} \neg\bigvee_{B, R} \text{RoleChanged}(\text{Fang Gu}, B, R, \tau')\right) \rightarrow \text{RoleChanged}(\text{Fang Gu}, O, \text{Fang Gu}, \tau) \land \text{AlignChanged}(\text{Fang Gu}, O, \text{Evil}, \tau) \land \text{Kills}(\text{Fang Gu}, X, \tau) \land \neg\text{Died}(O, \tau)$, for $\tau = (t, k_{\text{Fang Gu}})$ <br> **"First" comes from history: no earlier jump. $\text{Registers}$ lets a Spy or Recluse count as an Outsider. The old Fang Gu dies and stays the Fang Gu, so "No two living players share a role" still holds.** |
| Only by a jump | $\text{RoleChanged}(\text{Fang Gu}, O, R, \tau) \rightarrow R = \text{Fang Gu} \land \bigvee_{X \in \mathcal{P}} \left(\text{Active}(X, \text{Fang Gu}, \tau) \land \text{Chose}(X, O, \tau) \land \text{Registers}(O, \text{Outsider}, \tau)\right)$ |

Fang Gu would be added to $\mathcal{C}$, $\mathcal{A}$ and $\mathcal{K}$ on scripts that include it.

## Changes to existing rows

- **Structural Invariants:** remove "Roles persist (temporary)" and "Alignment persists (temporary)"; they are replaced by the frame rows above.
- **Structural Invariants:** replace "No two players share a role" with "No two living players share a role".
- **Predicate table, Role row:** the note "$\text{Role}(A, R, t) \rightarrow \neg\text{Role}(B, R, t)$ for all $B \neq A$" becomes "for all living $B \neq A$ while A is alive".
- **General Event "The game is still going on day $d_{\text{cur}}$":** replace the long statement with
  $$\bigvee_{X \in \mathcal{P}} \left(\text{Demon}(X, \tau) \land \text{Alive}(X, \tau)\right) \text{ for every } \tau \text{ up to and including } (d_{\text{cur}}, D)$$
  Role changes take effect at $\text{next}(\tau)$, the same time the old Demon is dead. So if the Demon dies at $\tau$ with no successor, no living Demon exists at $\text{next}(\tau)$. This covers the star-pass, the Scarlet Woman and Fang Gu without special cases. Other ways to end a game (2 players alive, Saint, Mayor) would be separate rows.
- **Imp "Only the target dies":** drop the note saying the star-pass is not modelled.

## Effect on example.md

- The assumption "D is not the Imp and C is not the Imp" is no longer needed.
- The result will change. These worlds become possible and the 13-world count needs re-running:
  - D was the Imp, executed on day 1 with 5 alive, and a sober Scarlet Woman became the Imp.
  - C was the Imp, chose itself at $(2, 30)$, and a living Minion (or sober Recluse) became the Imp.
