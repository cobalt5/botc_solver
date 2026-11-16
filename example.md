### Example of a game, with statements, and solving the possible worlds

#### Players: 5 (BaseCount: 3 Townsfolk, 0 Outsiders, 1 Minion, 1 Demon)
#### Script: Trouble Brewing
#### Seating (clockwise): A, B, C, D, E, then back to A
#### The solver is run on day 2, so $d_{\text{cur}} = 2$

| Player | Claim| Actions|
| -------- | -------- |--------|
| A | Investigator| Sees players E and D, one of them is the Scarlet Woman, night 1|
| B | Chef | Sees 0 pairs, night 1|
| C | Ravenkeeper| Dies night 2, chooses E, sees Empath|
| D | Soldier| Does nothing, executed day 1|
| E | Empath| Sees 1 evil neighbour night 1, sees 2 evil neighbours night 2|

Day 2: players A (Investigator), B (Chef) and E (Empath) are alive.

### Timeline
Positions come from [nightorder.json](nightorder.json). Night 1 has $K_1 = 67$ positions and night 2 has $K_2 = 85$.

| Time | Event |
| :--- | :--- |
| $(1, 0)$ | Setup |
| $(1, 23)$ | Poisoner acts (if in play) |
| $(1, 44)$ | Investigator (A) |
| $(1, 45)$ | Chef (B) |
| $(1, 46)$ | Empath (E) |
| $(1, D)$ | No deaths announced for night 1. D is executed and dies. If D was the Imp, a sober Scarlet Woman becomes the Imp |
| $(2, 8)$ | Poisoner acts (if in play and alive) |
| $(2, 30)$ | Imp acts: chooses C, who dies. If C was the Imp, this is a star-pass |
| $(2, 64)$ | Ravenkeeper (C) chooses E |
| $(2, 65)$ | Empath (E) |
| $(2, D)$ | C's death is announced. The solver is run |

### Sets for this game
| Symbol | Value |
| :--- | :--- |
| $\mathcal{P}$ | $\{A, B, C, D, E\}$ |
| $\mathcal{T}$ (Townsfolk) | $\{\text{Washerwoman}, \text{Librarian}, \text{Investigator}, \text{Chef}, \text{Empath}, \text{Fortune Teller}, \text{Undertaker}, \text{Monk}, \text{Ravenkeeper}, \text{Virgin}, \text{Slayer}, \text{Soldier}, \text{Mayor}\}$ |
| $\mathcal{O}$ (Outsiders) | $\{\text{Butler}, \text{Drunk}, \text{Recluse}, \text{Saint}\}$ |
| $\mathcal{M}$ (Minions) | $\{\text{Poisoner}, \text{Spy}, \text{Scarlet Woman}, \text{Baron}\}$ |
| $\mathcal{D}$ (Demons) | $\{\text{Imp}\}$ |
| $\mathcal{S}$ (not-sober sources) | $\{\text{Drunk}, \text{Poisoner}\}$, with $c_{\text{Drunk}} = c_{\text{Poisoner}} = 1$ |
| $\mathcal{K}$ (killing sources) | $\{\text{Imp}, \text{Slayer}, \text{Execution}\}$ |
| $\mathcal{C}$ (role-changing sources) | $\{\text{Imp}, \text{Scarlet Woman}\}$ |
| $\mathcal{A}$ (alignment-changing sources) | $\emptyset$ |
| Seating | $\text{cw}^1(A) = B,\ \text{cw}^1(B) = C,\ \text{cw}^1(C) = D,\ \text{cw}^1(D) = E,\ \text{cw}^1(E) = A$ |
| Times $\Theta$ | $(1, 0), (1, 1), \dots, (1, 67), (1, D), (2, 1), \dots, (2, 85), (2, D)$ |

In every statement below, $X$ ranges over $\mathcal{P}$ and $\tau$ over $\Theta$ unless a row says otherwise.

### Statements

#### Setup
| Condition | Statement|
| -------- | --------- |
|5 players | $\text{PlayerCount}(5) \land \text{BaseCount}(\text{Townsfolk}, 3)\land \text{BaseCount}(\text{Outsider}, 0)\land \text{BaseCount}(\text{Minion}, 1)\land \text{BaseCount}(\text{Demon}, 1)$|

#### Setup Modifiers
The Baron is the only setup modifier on Trouble Brewing. Every other role $R$ on the script has $\text{Mod}(R, G, 0)$ from "Modifiers default to zero".

| Condition | Statement|
| -------- | --------- |
|Baron | $\text{InPlay}(\text{Baron}) \rightarrow \text{Mod}(\text{Baron}, \text{Outsider}, +2) \land \text{Mod}(\text{Baron}, \text{Townsfolk}, -2)$|
|Baron not in play | $\neg\text{InPlay}(\text{Baron}) \rightarrow \text{Mod}(\text{Baron}, \text{Outsider}, 0) \land \text{Mod}(\text{Baron}, \text{Townsfolk}, 0)$|

#### Structural Invariants
Every row of Structural Invariants in [situations.md](situations.md), filled in for this game.

**Roles and types**
| Invariant | Statement|
| -------- | --------- |
|Every player has exactly one role | $\bigvee_{R \in \mathcal{T} \cup \mathcal{O} \cup \mathcal{M} \cup \mathcal{D}} \text{Role}(X, R, \tau) \land \left(\text{Role}(X, R_1, \tau) \land \text{Role}(X, R_2, \tau) \rightarrow R_1 = R_2\right)$|
|No two living players share a role | $\text{Role}(X, R, \tau) \land \text{Role}(Y, R, \tau) \land \text{Alive}(X, \tau) \land \text{Alive}(Y, \tau) \rightarrow X = Y$|
|Townsfolk defined by role | $\text{Townsfolk}(X, \tau) \leftrightarrow \bigvee_{R \in \mathcal{T}} \text{Role}(X, R, \tau)$|
|Outsider defined by role | $\text{Outsider}(X, \tau) \leftrightarrow \text{Role}(X, \text{Butler}, \tau) \lor \text{Role}(X, \text{Drunk}, \tau) \lor \text{Role}(X, \text{Recluse}, \tau) \lor \text{Role}(X, \text{Saint}, \tau)$|
|Minion defined by role | $\text{Minion}(X, \tau) \leftrightarrow \text{Role}(X, \text{Poisoner}, \tau) \lor \text{Role}(X, \text{Spy}, \tau) \lor \text{Role}(X, \text{Scarlet Woman}, \tau) \lor \text{Role}(X, \text{Baron}, \tau)$|
|Demon defined by role | $\text{Demon}(X, \tau) \leftrightarrow \text{Role}(X, \text{Imp}, \tau)$|
|Every player has exactly one type | $[\text{Townsfolk}(X, \tau)] + [\text{Outsider}(X, \tau)] + [\text{Minion}(X, \tau)] + [\text{Demon}(X, \tau)] = 1$|
|Every player has exactly one alignment | $\text{Align}(X, \text{Good}, \tau) \oplus \text{Align}(X, \text{Evil}, \tau)$|
|Assume good players are honest | $\left(\text{Align}(X, \text{Good}, (2, D)) \land \text{Claims}(X, R)\right) \rightarrow \text{Role}(X, R, (2, D)) \oplus \text{Role}(X, \text{Drunk}, (2, D))$ <br> **With the claims below: A is the Investigator, B the Chef, C the Ravenkeeper, D the Soldier and E the Empath, or the Drunk, unless they are evil.**|
|A good Drunk claims a Townsfolk not in play |$\left(\text{Align}(X, \text{Good}, (2, D)) \land \text{Role}(X, \text{Drunk}, (2, D)) \land \text{Claims}(X, R)\right) \rightarrow R \in \mathcal{T} \land \neg\text{InPlay}(R)$ <br> **Without the Baron there are 0 Outsiders, so no Drunk is in play and this row does nothing here.**|
|Roles change only by a listed source | $\text{Role}(X, R, \text{next}(\tau)) \leftrightarrow \left(\text{Role}(X, R, \tau) \land \neg\bigvee_{S \in \{\text{Imp}, \text{Scarlet Woman}\},\, R' \neq R} \text{RoleChanged}(S, X, R', \tau)\right) \lor \bigvee_{S \in \{\text{Imp}, \text{Scarlet Woman}\}} \text{RoleChanged}(S, X, R, \tau)$|
|At most one new role per player per time | $\sum_{S \in \{\text{Imp}, \text{Scarlet Woman}\}} \sum_{R} [\text{RoleChanged}(S, X, R, \tau)] \le 1$|
|Alignments change only by a listed source | $\text{Align}(X, M, \text{next}(\tau)) \leftrightarrow \text{Align}(X, M, \tau)$ <br> **$\mathcal{A} = \emptyset$, so alignment never changes in this game.**|
|At most one new alignment per player per time | Trivial: $\mathcal{A} = \emptyset$.|

**Setup counts**
| Invariant | Statement|
| -------- | --------- |
|In play defined by setup | $\text{InPlay}(R) \leftrightarrow \bigvee_{X \in \{A, B, C, D, E\}} \text{Role}(X, R, (1, 0))$, for each $R$ on the script|
|One modifier per role and type | $\text{Mod}(R, G, d_1) \land \text{Mod}(R, G, d_2) \rightarrow d_1 = d_2$|
|Modifiers default to zero | $\text{Mod}(R, G, 0)$ for every $R \neq \text{Baron}$, and $\neg\text{InPlay}(\text{Baron}) \rightarrow \text{Mod}(\text{Baron}, G, 0)$|
|Modifiers preserve player count | $\delta(\text{Baron}, \text{Townsfolk}) + \delta(\text{Baron}, \text{Outsider}) = 0$|
|Setup type counts | $\text{Count}(\{X : \text{Townsfolk}(X, (1, 0))\}) = 3 + \delta(\text{Baron}, \text{Townsfolk})$ <br> $\text{Count}(\{X : \text{Outsider}(X, (1, 0))\}) = 0 + \delta(\text{Baron}, \text{Outsider})$ <br> $\text{Count}(\{X : \text{Minion}(X, (1, 0))\}) = 1$ <br> $\text{Count}(\{X : \text{Demon}(X, (1, 0))\}) = 1$|
|Base counts sum to the player count | $3 + 0 + 1 + 1 = 5 = \lvert\{A, B, C, D, E\}\rvert$|
|Nonnegative group sizes | TBD in [situations.md](situations.md); holds here, since every count above is at least 0.|
|Starting alignment (Good) | $\left(\text{Townsfolk}(X, (1, 0)) \lor \text{Outsider}(X, (1, 0))\right) \rightarrow \text{Align}(X, \text{Good}, (1, 0))$|
|Starting alignment (Evil) | $\left(\text{Minion}(X, (1, 0)) \lor \text{Demon}(X, (1, 0))\right) \rightarrow \text{Align}(X, \text{Evil}, (1, 0))$|

So there are two possible setups:
- Baron not in play: 3 Townsfolk, 0 Outsiders, 1 Minion, 1 Demon.
- Baron in play: 1 Townsfolk, 2 Outsiders, 1 Minion (the Baron), 1 Demon.

**Registration**
| Invariant | Statement|
| -------- | --------- |
|Evil registers as Evil | $(\text{Align}(X, \text{Evil}, \tau) \land \neg(\text{Role}(X, \text{Spy}, \tau) \land \text{Sober}(X, \tau))) \rightarrow \text{Registers}(X, \text{Evil}, \tau)$|
|Good does not register as Evil | $(\text{Align}(X, \text{Good}, \tau) \land \neg(\text{Role}(X, \text{Recluse}, \tau) \land \text{Sober}(X, \tau))) \rightarrow \neg\text{Registers}(X, \text{Evil}, \tau)$|
|Players register as their true type | $\neg((\text{Role}(X, \text{Spy}, \tau) \lor \text{Role}(X, \text{Recluse}, \tau)) \land \text{Sober}(X, \tau)) \rightarrow (\text{Registers}(X, G, \tau) \leftrightarrow G(X, \tau))$, for each $G \in \mathcal{G}$|
|Every player registers as exactly one alignment | $\text{Registers}(X, \text{Good}, \tau) \oplus \text{Registers}(X, \text{Evil}, \tau)$|
|Every player registers as exactly one type | $\sum_{G \in \mathcal{G}} [\text{Registers}(X, G, \tau)] = 1$|

**Red Herring** (these apply but do nothing here: no one claims Fortune Teller, so with "Assume good players are honest" no Fortune Teller is in play)
| Invariant | Statement|
| -------- | --------- |
|At most one Red Herring | $\text{Redherring}(X, \tau) \land \text{Redherring}(Y, \tau) \rightarrow X = Y$|
|Red Herring exists if a Fortune Teller is in play | $\left(\bigvee_{X} \text{Role}(X, \text{Fortune Teller}, (1, 0))\right) \rightarrow \bigvee_{Y} \text{Redherring}(Y, (1, 0))$|
|The Red Herring is Good | $\text{Redherring}(X, (1, 0)) \rightarrow \text{Align}(X, \text{Good}, (1, 0))$|
|The Red Herring does not change | $\text{Redherring}(X, \tau) \leftrightarrow \text{Redherring}(X, \text{next}(\tau))$|

**Life and death**
| Invariant | Statement|
| -------- | --------- |
|Everyone is alive at setup | $\text{Alive}(X, (1, 0))$|
|Only the living die | $\text{Died}(X, \tau) \rightarrow \text{Alive}(X, \tau)$|
|Death is immediate | $\text{Died}(X, \tau) \rightarrow \neg\text{Alive}(X, \text{next}(\tau))$|
|The living stay alive unless they die | $\left(\text{Alive}(X, \tau) \land \neg\text{Died}(X, \tau)\right) \rightarrow \text{Alive}(X, \text{next}(\tau))$|
|The dead stay dead | $\neg\text{Alive}(X, \tau) \rightarrow \neg\text{Alive}(X, \text{next}(\tau))$|
|Nearest alive neighbours | $\text{Alive\_Neighbors}(X, Y, \tau) \leftrightarrow \text{Alive}(Y, \tau) \land \bigvee_{i \ge 1} \left(\left(Y = \text{cw}^i(X) \land \bigwedge_{j < i} \neg\text{Alive}(\text{cw}^j(X), \tau)\right) \lor \left(Y = \text{ccw}^i(X) \land \bigwedge_{j < i} \neg\text{Alive}(\text{ccw}^j(X), \tau)\right)\right)$, for $Y \neq X$, $1 \le i \le 4$|
|At most one execution per day | $\text{Executed}(X, t) \land \text{Executed}(Y, t) \rightarrow X = Y$, for $t \in \{1, 2\}$|
|Died only if killed | $\text{Died}(X, \tau) \leftrightarrow \text{Kills}(\text{Imp}, X, \tau) \lor \text{Kills}(\text{Slayer}, X, \tau) \lor \text{Kills}(\text{Execution}, X, \tau)$|
|At most one kill per source per time | $\sum_{X} [\text{Kills}(S, X, \tau)] \le 1$, for $S \in \{\text{Imp}, \text{Slayer}, \text{Execution}\}$|

**Sobriety**
| Invariant | Statement|
| -------- | --------- |
|Sober unless afflicted | $\text{Sober}(X, \tau) \leftrightarrow \neg\text{Afflicts}(\text{Drunk}, X, \tau) \land \neg\text{Afflicts}(\text{Poisoner}, X, \tau)$|
|Cap per source | $\sum_{X} [\text{Afflicts}(\text{Drunk}, X, \tau)] \le 1$ <br> $\sum_{X} [\text{Afflicts}(\text{Poisoner}, X, \tau)] \le 1$|
|Total not sober | $\text{Count}(\{X : \neg\text{Sober}(X, \tau)\}) \le \left[\bigvee_{X} \text{Role}(X, \text{Drunk}, \tau)\right] + \left[\bigvee_{X} \text{Role}(X, \text{Poisoner}, \tau)\right]$|

#### Role Invariants
Every row of Role Invariants in [situations.md](situations.md). $k_{\text{Poisoner}}$ is 23 on night 1 and 8 on night 2; $k_{\text{Imp}} = 30$ and $k_{\text{Monk}} = 15$ on night 2. The Monk and Slayer rows apply but do nothing here: no one claims either role, so with "Assume good players are honest" neither is in play.

| Role | Invariant | Statement|
| -------- | -------- | --------- |
|Drunk|The Drunk is drunk | $\text{Afflicts}(\text{Drunk}, X, \tau) \leftrightarrow \text{Role}(X, \text{Drunk}, \tau)$|
|Poisoner|Poison starts only when chosen | $\left(\neg\text{Afflicts}(\text{Poisoner}, X, \tau) \land \text{Afflicts}(\text{Poisoner}, X, \text{next}(\tau))\right) \rightarrow \bigvee_{Y} \left(\tau \in \{(1, 23), (2, 8)\} \land \text{Active}(Y, \text{Poisoner}, \tau) \land \text{Chose}(Y, X, \tau)\right)$|
|Poisoner|Poison lasts until dusk | $\left(\text{Afflicts}(\text{Poisoner}, X, \tau) \land \tau \notin \{(1, D), (2, D)\} \land \bigvee_{Y} \left(\text{Role}(Y, \text{Poisoner}, \text{next}(\tau)) \land \text{Alive}(Y, \text{next}(\tau)) \land (\text{Sober}(Y, \text{next}(\tau)) \lor Y = X)\right)\right) \rightarrow \text{Afflicts}(\text{Poisoner}, X, \text{next}(\tau))$|
|Poisoner|Poison ends at dusk | $\neg\text{Afflicts}(\text{Poisoner}, X, (2, 1))$|
|Poisoner|Poison ends if the Poisoner stops working | $\text{Afflicts}(\text{Poisoner}, X, \tau) \rightarrow \bigvee_{Y} \left(\text{Role}(Y, \text{Poisoner}, \tau) \land \text{Alive}(Y, \tau) \land (\text{Sober}(Y, \tau) \lor Y = X)\right)$|
|Imp|Must choose | $\text{Active}(X, \text{Imp}, (2, 30)) \rightarrow \sum_{Y} [\text{Chose}(X, Y, (2, 30))] = 1$|
|Imp|Kills only on its turn | $\text{Kills}(\text{Imp}, Y, \tau) \rightarrow \tau = (2, 30) \land \bigvee_{X} \text{Active}(X, \text{Imp}, \tau)$|
|Imp|Target dies unless protected | $\left(\text{Active}(X, \text{Imp}, (2, 30)) \land \text{Chose}(X, Y, (2, 30)) \land \text{Alive}(Y, (2, 30)) \land \neg\text{Safe}(Y, (2, 30)) \land \neg(\text{Role}(Y, \text{Soldier}, (2, 30)) \land \text{Sober}(Y, (2, 30))) \land \neg(\text{Role}(Y, \text{Mayor}, (2, 30)) \land \text{Sober}(Y, (2, 30)))\right) \rightarrow \text{Kills}(\text{Imp}, Y, (2, 30))$|
|Imp|Only the target dies | $\text{Kills}(\text{Imp}, Y, \tau) \rightarrow \bigvee_{X} \Big(\text{Active}(X, \text{Imp}, \tau) \land \big(\text{Chose}(X, Y, \tau) \lor \bigvee_{M} (\text{Chose}(X, M, \tau) \land \text{Role}(M, \text{Mayor}, \tau) \land \text{Sober}(M, \tau))\big)\Big)$|
|Imp|Passes on self-kill | $\left(\text{Role}(X, \text{Imp}, \tau) \land \text{Kills}(\text{Imp}, X, \tau) \land \bigvee_{M} (\text{Alive}(M, \tau) \land \text{Registers}(M, \text{Minion}, \tau))\right) \rightarrow \sum_{M} \left[\text{RoleChanged}(\text{Imp}, M, \text{Imp}, \tau) \lor \text{RoleChanged}(\text{Scarlet Woman}, M, \text{Imp}, \tau)\right] = 1$|
|Imp|Only to a living player who registers as a Minion | $\text{RoleChanged}(\text{Imp}, M, R, \tau) \rightarrow R = \text{Imp} \land \text{Alive}(M, \tau) \land \text{Registers}(M, \text{Minion}, \tau) \land \bigvee_{X} \left(\text{Role}(X, \text{Imp}, \tau) \land \text{Kills}(\text{Imp}, X, \tau) \land X \neq M\right)$|
|Scarlet Woman|Becomes the Demon | $\text{RoleChanged}(\text{Scarlet Woman}, M, R, \tau) \leftrightarrow \text{Active}(M, \text{Scarlet Woman}, \tau) \land \text{Count}(\{Z : \text{Alive}(Z, \tau)\}) \ge 5 \land \bigvee_{X} \left(\text{Demon}(X, \tau) \land \text{Role}(X, R, \tau) \land \text{Died}(X, \tau)\right)$ <br> **Only D's death at $(1, D)$ has 5 alive; C's death at $(2, 30)$ has 4.**|
|Scarlet Woman|Takes priority over the star-pass | $\text{RoleChanged}(\text{Scarlet Woman}, M, R, \tau) \rightarrow \bigwedge_{M'} \neg\text{RoleChanged}(\text{Imp}, M', R, \tau)$|
|Monk|Protection lasts the night | $\left(\text{Active}(X, \text{Monk}, (2, 15)) \land \text{Chose}(X, Y, (2, 15)) \land X \neq Y\right) \rightarrow \text{Safe}(Y, (2, k))$ for every $15 < k \le 85$|
|Monk|Safe only when chosen by an active Monk | $\text{Safe}(Y, (2, k)) \rightarrow k > 15 \land \bigvee_{X} \left(\text{Active}(X, \text{Monk}, (2, 15)) \land \text{Chose}(X, Y, (2, 15)) \land X \neq Y\right)$|
|Slayer|Kills only the Demon (or a Recluse) | $\text{Kills}(\text{Slayer}, B, (t, D)) \rightarrow \bigvee_{X} \left(\text{Active}(X, \text{Slayer}, (t, D)) \land \text{Chose}(X, B, (t, D)) \land \left(\text{Demon}(B, (t, D)) \lor (\text{Role}(B, \text{Recluse}, (t, D)) \land \text{Sober}(B, (t, D)))\right)\right)$|
|Execution|Execution kills | $\text{Kills}(\text{Execution}, X, (t, D)) \leftrightarrow \text{Executed}(X, t)$|
|Execution|Executions only by day | $\text{Kills}(\text{Execution}, X, \tau) \rightarrow \tau \in \{(1, D), (2, D)\}$|

So a player can be not sober on night 1 from $(1, 24)$ (the Poisoner's night 1 target, or the Drunk), and on night 2 from $(2, 9)$ (the Poisoner's night 2 target, if the Poisoner is alive, or the Drunk). The night 1 target is sober again from $(2, 1)$.

C's death must come from a source in $\mathcal{K}$. The only night source is the Imp, so C died at $(2, 30)$: an active (alive, sober) Imp chose C. So C is dead at $(2, 64)$ and $(2, 65)$, the Imp was not poisoned on night 2, and if C was the Imp, the Demon passed to a living player who registers as a Minion.

#### General Events
| Event | Statement|
| -------- | --------- |
|Day 1: no deaths announced for night 1 ($X = \emptyset$) | $\text{Alive}(Y, (1, 1)) \rightarrow \text{Alive}(Y, (1, D))$ for each $Y \in \{A, B, C, D, E\}$|
|D is executed on day 1 | $\text{Executed}(D, 1)$ <br> **With "Execution kills" and "Died only if killed", this gives $\text{Died}(D, (1, D))$.**|
|Day 2: C's death is announced ($X = \{C\}$) | $\text{Alive}(C, (2, 1)) \land \neg\text{Alive}(C, (2, D))$ <br> $\text{Alive}(Y, (2, 1)) \rightarrow \text{Alive}(Y, (2, D))$ for each $Y \in \{A, B, D, E\}$|
|The game is still going on day 2 | $\bigvee_{X} \left(\text{Demon}(X, \tau) \land \text{Alive}(X, \tau)\right)$ for every $\tau$ up to and including $(2, D)$ <br> **If D was the Imp, a sober Scarlet Woman must have taken over at $(1, D)$. If C was the Imp, it must have passed the Demon on at $(2, 30)$.**|

#### Claims
$d_{\text{cur}} = 2$, so every claim checks $\text{Align}(\cdot, \text{Good}, (2, D))$.

Each player's final-day claim: $\text{Claims}(A, \text{Investigator}) \land \text{Claims}(B, \text{Chef}) \land \text{Claims}(C, \text{Ravenkeeper}) \land \text{Claims}(D, \text{Soldier}) \land \text{Claims}(E, \text{Empath})$

| Condition | Statement|
| -------- | --------- |
|A claims Investigator, sees E and D as the Scarlet Woman | $(\text{Sober}(A, (1, 44)) \land \text{Align}(A, \text{Good}, (2, D))) \rightarrow ((\text{Role}(E, \text{Scarlet Woman}, (1, 44)) \oplus \text{Role}(D, \text{Scarlet Woman}, (1, 44))) \lor (\text{Role}(E, \text{Recluse}, (1, 44)) \land \text{Sober}(E, (1, 44))) \lor (\text{Role}(D, \text{Recluse}, (1, 44)) \land \text{Sober}(D, (1, 44))))$|
|B claims Chef, sees 0 | $(\text{Sober}(B, (1, 45)) \land \text{Align}(B, \text{Good}, (2, D))) \rightarrow \sum_{(X, Y)} [\text{Registers}(X, \text{Evil}, (1, 45)) \land \text{Registers}(Y, \text{Evil}, (1, 45))] = 0$ <br> **Every adjacent pair: $(A, B), (B, C), (C, D), (D, E), (E, A)$.**|
|E claims Empath, sees 1 on night 1 | $(\text{Sober}(E, (1, 46)) \land \text{Align}(E, \text{Good}, (2, D))) \rightarrow \sum_{Y \neq E} [\text{Alive\_Neighbors}(E, Y, (1, 46)) \land \text{Registers}(Y, \text{Evil}, (1, 46))] = 1$ <br> **Everyone is alive at $(1, 46)$, so $\text{Alive\_Neighbors}$ holds for D and A only, giving $[\text{Registers}(D, \text{Evil}, (1, 46))] + [\text{Registers}(A, \text{Evil}, (1, 46))] = 1$.**|
|C claims Ravenkeeper, chose E, sees Empath (Good) | $\left(\text{Align}(C, \text{Good}, (2, D)) \rightarrow \bigvee_{k < 64} \text{Died}(C, (2, k))\right) \land$<br>$(\text{Sober}(C, (2, 64)) \land \text{Align}(C, \text{Good}, (2, D))) \rightarrow \text{Role}(E, \text{Empath}, (2, 64)) \lor (\text{Role}(E, \text{Spy}, (2, 64)) \land \text{Sober}(E, (2, 64)))$|
|E claims Empath, sees 2 on night 2 | $(\text{Sober}(E, (2, 65)) \land \text{Align}(E, \text{Good}, (2, D))) \rightarrow \sum_{Y \neq E} [\text{Alive\_Neighbors}(E, Y, (2, 65)) \land \text{Registers}(Y, \text{Evil}, (2, 65))] = 2$ <br> **D is dead from $(2, 1)$ and C from $(2, 31)$, so $\text{Alive\_Neighbors}$ holds for A and B only, giving $[\text{Registers}(A, \text{Evil}, (2, 65))] + [\text{Registers}(B, \text{Evil}, (2, 65))] = 2$.**|
|D claims Soldier | No statement: the Soldier gets no information.|

C's choice of E is part of C's claim, not a public fact, so it is not stated as $\text{Chose}(C, E, (2, 64))$. The Ravenkeeper row does not need it.

The Ravenkeeper row reads E's role at $(2, 64)$, after any star-pass. If E caught the star-pass, E is the Imp there, not the Spy.

### Solving

#### Assumptions
None beyond [situations.md](situations.md). Good players being honest is now the structural invariant "Assume good players are honest", which comes from assumption 1 of [design.md](design.md).

#### Result
Checked by enumerating every role assignment, Poisoner target, Spy registration, Scarlet Woman takeover and star-pass: **16 possible worlds**. 13 have no role change; 3 have C as the Imp, star-passing on night 2.

| Conclusion | Why |
| :--- | :--- |
| The Baron is not in play | It would add 2 Outsiders. All 5 players claim Townsfolk, and the Drunk is the only Good Outsider who can do that, so there is no second Outsider. |
| There is no Drunk or Recluse | Without the Baron there are 0 Outsiders. |
| D is Good and really is the Soldier | As the Minion: E's night 2 reading needs both A and B to be evil, leaving no room for D, and a dead D can't poison E on night 2. As the Imp: a Scarlet Woman must take over on day 1, and every placement of her breaks a claim. She can't be A (E's night 1 reading would be 2), B (A's Investigator reading names E or D), E (C's Ravenkeeper reading needs E to be the Empath or Spy at $(2, 64)$, but E is the Imp by then), or C (C would then be the Imp who died at night, with no Minion left to pass to). |
| The Imp at setup is A, B, C or E | A in 6 worlds, B in 4, C in 3, E in 3 |
| The living Imp on day 2 is A, B or E | A in 7 worlds, B in 4, E in 5. In the 3 star-pass worlds, C passed to A (a Poisoner) or E (a Poisoner or Scarlet Woman). |
| The Minion at setup is the Poisoner, the Spy or the Scarlet Woman | Poisoner in 10 worlds, Spy in 3, Scarlet Woman in 3 |
| The most likely evil pair is A and B | A and B in 6 worlds, A and E in 3, C and E in 3, A and C in 2, B and E in 2 |

Why A and B: if E is sober and Good on night 2, both A and B must register as evil. Every world without that needs E to be evil, or to have been poisoned on night 2.
