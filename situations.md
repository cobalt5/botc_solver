### Assume the player claiming the role is player A
#### Assume that Townsfolk may possibly be evil

## Time
Time for day is a single number t, representing day t<br>
Time for night is a pair $(t, k)$: position $k$ in the night order of night $t$ (the First Night list for $t = 1$, the Other Nights list for $t > 1$). Day $t$ is written $(t, D)$; it comes after every position of night $t$ and before $(t + 1, 1)$. $k_R$ is the position of role $R$ in that night's order.

Let $K_t$ be the last position of night $t$'s order. The time after $\tau$ is $\text{next}(\tau)$:
$$\text{next}((t, k)) = (t, k+1) \text{ for } k < K_t, \qquad \text{next}((t, K_t)) = (t, D), \qquad \text{next}((t, D)) = (t+1, 1)$$
Setup time $(1, 0)$ is followed by $\text{next}((1, 0)) = (1, 1)$.

A state claim at $(t, k)$ is the state when the ability at position $k$ resolves, after every earlier position that night.

**Why:** abilities resolve one at a time in night order, and an earlier ability can change the state a later one reads in the same night (the Poisoner poisons before the Empath wakes, the Imp kills before the Empath counts alive neighbours, the Snake Charmer or Pit-Hag changes a character mid-night). Indexing by night alone cannot express a state that changes partway through a night; $(t, k)$ lets each ability read the state at the moment it acts.


#### For reference
| Symbol | Meaning |  
| :--- | :---: |
| $\neg $ A| Not A|
| A $\land $ B| A AND B|
| A $\lor $ B| A OR B|
| A $\oplus $ B| A XOR B|
| A $\rightarrow $ B| If A, then B|
|Count(C) | There are Count(C) instances of C|

|Claim|Meaning|
| :--- | :---: |
|Role(A, R, t)| Player A has role R at day t<br> **Note that $(\text{Role}(A, R, t) \land \text{Alive}(A, t)) \rightarrow \neg(\text{Role}(B, R, t) \land \text{Alive}(B, t))$ for all B $\neq$ A. A dead player keeps their role, so a dead and a living player can share one (e.g. after a star-pass).**|
|Role(A, R, (t, k))| Player A has role R at night t immediately after night order k|
|Align(A, M, t)| Player A has alignment M at time t|
|Sober(A, t)| Player A is sober at time t (neither drunk nor poisoned; the two are treated the same)|
|Afflicts(S, A, $\tau$)| The ability of role S is making player A not sober at time $\tau$|
|Active(X, S, $\tau$)| Shorthand: player X holds a working copy of role S at $\tau$, i.e. $\text{Role}(X, S, \tau) \land \text{Alive}(X, \tau) \land \text{Sober}(X, \tau)$ <br> **Roles whose ability works while dead (Spy, Recluse, Sweetheart) drop the $\text{Alive}$ part.**|
|Chose(A, B, t) | Player A chose B at time t|
|Claims(A, R) | Player A claims to be role R (their final-day claim)|
|Redherring(A, t)|Player A is the redherring at time t<br>**See the Red Herring rows in Structural Invariants.**|
|Registers(A, X, t)|Player A registers as X to the ability resolving at time t, where X is an alignment (Good, Evil) or a type in $\mathcal{G}$ (Townsfolk, Outsider, Minion, Demon)<br>**See the registration rows in Structural Invariants.**|
|Executed(A, t)| Player A was executed on day t<br>**Note that execution does not always mean death (e.g. Zombuul, Fool, Devil's Advocate).**|
|Alive(A, $\tau$)| Player A is alive at time $\tau$|
|Died(A, $\tau$)| Player A dies at time $\tau$: during the ability at night position $\tau = (t, k)$, or on day $\tau = (t, D)$. A is still alive at $\tau$ and dead from $\text{next}(\tau)$|
|Kills(S, A, $\tau$)| Source S kills player A at time $\tau$. S is a role, or Execution (not a role). This is the successful kill: the attempt is $\text{Chose}$, and protection decides whether an attempt becomes a kill|
|RoleChanged(S, A, R, $\tau$)| Source S changes player A's role to R at time $\tau$. Takes effect at $\text{next}(\tau)$, like $\text{Died}$|
|AlignChanged(S, A, M, $\tau$)| Source S changes player A's alignment to M at time $\tau$. Takes effect at $\text{next}(\tau)$|
|Alive_Neighbors(A, B, $\tau$)| B is one of A's nearest alive neighbours at time $\tau$ (defined in Structural Invariants)|
|Night_Protected(A, (t, k)) | Player A is protected from death at night(Innkeeper)|
|Protected(A, t) | Player A is protected from death, period. (Tea Lady)|
|Exec_Protected(A, t) | Player A is protected from execution(Devil's advocate, Tea Lady)|
|Safe(A, (t, k)) | Player A is safe from all demon effects (Monk)|
|Townsfolk(A, t) | Player A is a townsfolk at time t|
|Outsider(A, t) | Player A is an outsider at time t|
|Minion(A, t) | Player A is a minion at time t|
|Demon (A, t) | Player A is a demon at time t|
|PlayerCount(N) | There are N players in the game|
|BaseCount(G, N) | The base number of players of type G (e.g. Townsfolk) is N|
|InPlay(R) | Role R is assigned to some player at setup (setup time is $=(1,0)$)|
|Mod(R, G, d) | Role R changes the setup count of type G by d <br> **Note that d can be negative, and every pair (R, G) has exactly one d (0 if R changes Nothing)**<br>**This rule is here due to some roles being able to change group counts by undetermined amounts (e.g. Godfather)**|


Refer to [nightorder.json](nightorder.json) for the night order

### Setup situations

**Setup Player Counts**
| Situation | Statement |
| :--- | :---: |
|There are 5 players|$\text{PlayerCount}(5) \land \text{BaseCount}(\text{Townsfolk}, 3)\land \text{BaseCount}(\text{Outsider}, 0)\land \text{BaseCount}(\text{Minion}, 1)\land \text{BaseCount}(\text{Demon}, 1)$|
|There are 6 players|$\text{PlayerCount}(6) \land \text{BaseCount}(\text{Townsfolk}, 3)\land \text{BaseCount}(\text{Outsider}, 1)\land \text{BaseCount}(\text{Minion}, 1)\land \text{BaseCount}(\text{Demon}, 1)$|
|There are 7 players|$\text{PlayerCount}(7) \land \text{BaseCount}(\text{Townsfolk}, 5)\land \text{BaseCount}(\text{Outsider}, 0)\land \text{BaseCount}(\text{Minion}, 1)\land \text{BaseCount}(\text{Demon}, 1)$|
|There are 8 players|$\text{PlayerCount}(8) \land \text{BaseCount}(\text{Townsfolk}, 5)\land \text{BaseCount}(\text{Outsider}, 1)\land \text{BaseCount}(\text{Minion}, 1)\land \text{BaseCount}(\text{Demon}, 1)$|
|There are 9 players|$\text{PlayerCount}(9) \land \text{BaseCount}(\text{Townsfolk}, 5)\land \text{BaseCount}(\text{Outsider}, 2)\land \text{BaseCount}(\text{Minion}, 1)\land \text{BaseCount}(\text{Demon}, 1)$|
|There are 10 players|$\text{PlayerCount}(10) \land \text{BaseCount}(\text{Townsfolk}, 7)\land \text{BaseCount}(\text{Outsider}, 0)\land \text{BaseCount}(\text{Minion}, 2)\land \text{BaseCount}(\text{Demon}, 1)$|
|There are 11 players|$\text{PlayerCount}(11) \land \text{BaseCount}(\text{Townsfolk}, 7)\land \text{BaseCount}(\text{Outsider}, 1)\land \text{BaseCount}(\text{Minion}, 2)\land \text{BaseCount}(\text{Demon}, 1)$|
|There are 12 players|$\text{PlayerCount}(12) \land \text{BaseCount}(\text{Townsfolk}, 7)\land \text{BaseCount}(\text{Outsider}, 2)\land \text{BaseCount}(\text{Minion}, 2)\land \text{BaseCount}(\text{Demon}, 1)$|
|There are 13 players|$\text{PlayerCount}(13) \land \text{BaseCount}(\text{Townsfolk}, 9)\land \text{BaseCount}(\text{Outsider}, 0)\land \text{BaseCount}(\text{Minion}, 3)\land \text{BaseCount}(\text{Demon}, 1)$|
|There are 14 players|$\text{PlayerCount}(14) \land \text{BaseCount}(\text{Townsfolk}, 9)\land \text{BaseCount}(\text{Outsider}, 1)\land \text{BaseCount}(\text{Minion}, 3)\land \text{BaseCount}(\text{Demon}, 1)$|
|There are 15 players|$\text{PlayerCount}(15) \land \text{BaseCount}(\text{Townsfolk}, 9)\land \text{BaseCount}(\text{Outsider}, 2)\land \text{BaseCount}(\text{Minion}, 3)\land \text{BaseCount}(\text{Demon}, 1)$|

**Setup Modifiers**
| Situation | Statement |
| :--- | :---: |
|Baron|$\text{InPlay}(\text{Baron}) \rightarrow \text{Mod}(\text{Baron}, \text{Outsider}, +2) \land \text{Mod}(\text{Baron}, \text{Townsfolk}, -2)$|
|Godfather|$\text{InPlay}(\text{Godfather}) \rightarrow (\text{Mod}(\text{Godfather}, \text{Outsider}, +1) \land \text{Mod}(\text{Godfather}, \text{Townsfolk}, -1)) \oplus (\text{Mod}(\text{Godfather}, \text{Outsider}, -1) \land \text{Mod}(\text{Godfather}, \text{Townsfolk}, +1))$|
|Fang Gu|$\text{InPlay}(\text{Fang Gu}) \rightarrow \text{Mod}(\text{Fang Gu}, \text{Outsider}, +1) \land \text{Mod}(\text{Fang Gu}, \text{Townsfolk}, -1)$|
|Vigormortis|$\text{InPlay}(\text{Vigormortis}) \rightarrow \text{Mod}(\text{Vigormortis}, \text{Outsider}, -1) \land \text{Mod}(\text{Vigormortis}, \text{Townsfolk}, +1)$|
|Balloonist|$\text{InPlay}(\text{Balloonist}) \rightarrow (\text{Mod}(\text{Balloonist}, \text{Outsider}, 0) \land \text{Mod}(\text{Balloonist}, \text{Townsfolk}, 0)) \oplus (\text{Mod}(\text{Balloonist}, \text{Outsider}, +1) \land \text{Mod}(\text{Balloonist}, \text{Townsfolk}, -1))$|

### Structural Invariants
These hold in every possible world, for all players $A$, $B$ and all times $t$ (day or $(t, k)$), unless a row says otherwise.<br>
Let $\mathcal{T}, \mathcal{O}, \mathcal{M}, \mathcal{D}$ be the sets of Townsfolk, Outsider, Minion and Demon characters on the script, and $\mathcal{P}$ the set of players.<br>
Let $\mathcal{G} = \{\text{Townsfolk}, \text{Outsider}, \text{Minion}, \text{Demon}\}$ be the set of types, and $\delta(R, G)$ the unique $d$ such that $\text{Mod}(R, G, d)$. "One modifier per role and type" and "Modifiers default to zero" together guarantee that this $d$ exists and is unique.<br>
Setup time is $(1, 0)$: after the grimoire is built, before the first position of night 1.<br>
Players sit in a circle. $\text{cw}^i(A)$ is the player $i$ seats clockwise of $A$, and $\text{ccw}^i(A)$ the player $i$ seats counterclockwise, for $1 \le i < \lvert\mathcal{P}\rvert$.<br>
Let $\mathcal{S}$ be the set of roles on the script whose ability can make a player not sober (drunk or poisoned; the two are treated the same). Trouble Brewing: $\mathcal{S} = \{\text{Drunk}, \text{Poisoner}\}$. Each $S \in \mathcal{S}$ has a cap $c_S$, the most players it can affect at once, given in Role Invariants.<br>
Let $\mathcal{K}$ be the set of killing sources on the script: roles whose ability can kill, plus Execution. Trouble Brewing: $\mathcal{K} = \{\text{Imp}, \text{Slayer}, \text{Execution}\}$ (the Virgin's effect is an execution).<br>
Let $\mathcal{C}$ be the set of roles on the script whose ability can change a player's role. Trouble Brewing: $\mathcal{C} = \{\text{Imp}, \text{Scarlet Woman}\}$.<br>
Let $\mathcal{A}$ be the set of roles on the script whose ability can change a player's alignment. Trouble Brewing: $\mathcal{A} = \emptyset$ (a Minion who catches the star-pass is already evil, and a Recluse who catches it keeps their alignment).<br>

| Invariant | Statement |
| :--- | :---: |
|Every player has exactly one role|$\bigvee_{R} \text{Role}(A, R, t) \land \left(\text{Role}(A, R_1, t) \land \text{Role}(A, R_2, t) \rightarrow R_1 = R_2\right)$|
|No two living players share a role | $\text{Role}(A, R, \tau) \land \text{Role}(B, R, \tau) \land \text{Alive}(A, \tau) \land \text{Alive}(B, \tau) \rightarrow A = B$ <br> **Everyone is alive at setup, so setup roles are still unique. Roles with several living holders (Legion, Riot, Village Idiot) will need their own exception.** |
|Townsfolk defined by role|$\text{Townsfolk}(A, t) \leftrightarrow \bigvee_{R \in \mathcal{T}} \text{Role}(A, R, t)$|
|Outsider defined by role|$\text{Outsider}(A, t) \leftrightarrow \bigvee_{R \in \mathcal{O}} \text{Role}(A, R, t)$|
|Minion defined by role|$\text{Minion}(A, t) \leftrightarrow \bigvee_{R \in \mathcal{M}} \text{Role}(A, R, t)$|
|Demon defined by role|$\text{Demon}(A, t) \leftrightarrow \bigvee_{R \in \mathcal{D}} \text{Role}(A, R, t)$|
|Every player has exactly one type|$[\text{Townsfolk}(A, t)] + [\text{Outsider}(A, t)] + [\text{Minion}(A, t)] + [\text{Demon}(A, t)] = 1$ <br> **Follows from the rows above, since $\mathcal{T}, \mathcal{O}, \mathcal{M}, \mathcal{D}$ are disjoint; listed so a solver can use it directly.**|
|Every player has exactly one alignment|$\text{Align}(A, \text{Good}, t) \oplus \text{Align}(A, \text{Evil}, t)$|
|Assume good players are honest|$\left(\text{Align}(A, \text{Good}, (d_{\text{cur}}, D)) \land \text{Claims}(A, R)\right) \rightarrow \text{Role}(A, R, (d_{\text{cur}}, D)) \oplus \text{Role}(A, \text{Drunk}, (d_{\text{cur}}, D))$ <br> **From assumption 1 of [design.md](design.md): the solver is run on the final day, when Good players are honest. The Drunk believes they are a Townsfolk, so a Good Drunk claims a Townsfolk that is not in play. Other exceptions (Damsel, Politician, madness) are excluded by design.md or not on Trouble Brewing.**|
|A good Drunk claims a Townsfolk not in play|$\left(\text{Align}(A, \text{Good}, (d_{\text{cur}}, D)) \land \text{Role}(A, \text{Drunk}, (d_{\text{cur}}, D)) \land \text{Claims}(A, R)\right) \rightarrow R \in \mathcal{T} \land \neg\text{InPlay}(R)$ <br> **The Drunk is told they are a Townsfolk that is not in play, and a Good Drunk claims it honestly.**|
|In play defined by setup|$\text{InPlay}(R) \leftrightarrow \bigvee_{A \in \mathcal{P}} \text{Role}(A, R, (1, 0))$ <br> **Only setup counts: a Baron created later (e.g. Pit-Hag) does not change type counts.**|
|One modifier per role and type|$\text{Mod}(R, G, d_1) \land \text{Mod}(R, G, d_2) \rightarrow d_1 = d_2$|
|Modifiers default to zero|$\neg\text{InPlay}(R) \rightarrow \text{Mod}(R, G, 0)$, and $\text{Mod}(R, G, 0)$ for every $R$ that has no Setup Modifier row|
|Modifiers preserve player count|$\sum_{G \in \mathcal{G}} \delta(R, G) = 0$ <br> **A check on the modifier rows: a modifier moves players between types, never adds or removes them.**|
|Setup type counts|$\text{BaseCount}(G, b) \rightarrow \text{Count}(\{A : G(A, (1, 0))\}) = b + \sum_{R} \delta(R, G)$|
|Base counts sum to the player count|$\text{PlayerCount}(N) \rightarrow \sum_{G \in \mathcal{G}} b_G = N = \lvert\mathcal{P}\rvert$, where $\text{BaseCount}(G, b_G)$|
|There must be nonnegative group sizes at all times|TBD|
|Starting alignment (Good)|$\left(\text{Townsfolk}(A, (1, 0)) \lor \text{Outsider}(A, (1, 0))\right) \rightarrow \text{Align}(A, \text{Good}, (1, 0))$|
|Starting alignment (Evil)|$\left(\text{Minion}(A, (1, 0)) \lor \text{Demon}(A, (1, 0))\right) \rightarrow \text{Align}(A, \text{Evil}, (1, 0))$ <br> **Alignment can change later (e.g. Snake Charmer, Goon); this only fixes it at setup.**|
| Roles change only by a listed source | $\text{Role}(A, R, \text{next}(\tau)) \leftrightarrow \left(\text{Role}(A, R, \tau) \land \neg\bigvee_{S \in \mathcal{C},\, R' \neq R} \text{RoleChanged}(S, A, R', \tau)\right) \lor \bigvee_{S \in \mathcal{C}} \text{RoleChanged}(S, A, R, \tau)$ |
| At most one new role per player per time | $\sum_{S \in \mathcal{C}} \sum_{R} [\text{RoleChanged}(S, A, R, \tau)] \le 1$ |
| Alignments change only by a listed source | $\text{Align}(A, M, \text{next}(\tau)) \leftrightarrow \left(\text{Align}(A, M, \tau) \land \neg\bigvee_{S \in \mathcal{A},\, M' \neq M} \text{AlignChanged}(S, A, M', \tau)\right) \lor \bigvee_{S \in \mathcal{A}} \text{AlignChanged}(S, A, M, \tau)$ <br> **With $\mathcal{A} = \emptyset$ on Trouble Brewing, this reduces to "alignment never changes".** |
| At most one new alignment per player per time | $\sum_{S \in \mathcal{A}} \sum_{M} [\text{AlignChanged}(S, A, M, \tau)] \le 1$ |
|Evil registers as Evil|$(\text{Align}(A, \text{Evil}, t) \land \neg(\text{Role}(A, \text{Spy}, t) \land \text{Sober}(A, t))) \rightarrow \text{Registers}(A, \text{Evil}, t)$|
|Good does not register as Evil|$(\text{Align}(A, \text{Good}, t) \land \neg(\text{Role}(A, \text{Recluse}, t) \land \text{Sober}(A, t))) \rightarrow \neg\text{Registers}(A, \text{Evil}, t)$ <br> **A sober Spy or Recluse may register as either alignment.**|
|Players register as their true type|$\neg((\text{Role}(A, \text{Spy}, t) \lor \text{Role}(A, \text{Recluse}, t)) \land \text{Sober}(A, t)) \rightarrow (\text{Registers}(A, G, t) \leftrightarrow G(A, t))$, for each $G \in \mathcal{G}$ <br> **A sober Spy may register as a Townsfolk or Outsider, and a sober Recluse as a Minion or Demon, instead of their true type.**|
|Every player registers as exactly one alignment at a certain time|$\text{Registers}(A, \text{Good}, t) \oplus \text{Registers}(A, \text{Evil}, t)$ <br> **Registration is fixed for a given ability at time $t$: a sober Spy or Recluse cannot register as Good to one pair and Evil to another in the same Chef reading. It may register differently to different abilities.**|
|Every player registers as exactly one type at a certain time|$\sum_{G \in \mathcal{G}} [\text{Registers}(A, G, t)] = 1$|
|At most one Red Herring|$\text{Redherring}(A, t) \land \text{Redherring}(B, t) \rightarrow A = B$|
|Red Herring exists if a Fortune Teller is in play|$\left(\bigvee_{A \in \mathcal{P}} \text{Role}(A, \text{Fortune Teller}, (1, 0))\right) \rightarrow \bigvee_{B \in \mathcal{P}} \text{Redherring}(B, (1, 0))$|
|The Red Herring is Good|$\text{Redherring}(A, (1, 0)) \rightarrow \text{Align}(A, \text{Good}, (1, 0))$ <br> **Chosen at setup from the Good players (the Fortune Teller may choose themselves).**|
|The Red Herring does not change|$\text{Redherring}(A, \tau) \leftrightarrow \text{Redherring}(A, \text{next}(\tau))$ <br> **True on Trouble Brewing. Scripts that can create a new Fortune Teller mid-game (e.g. Pit-Hag) will need to weaken this.**|
|Everyone is alive at setup|$\text{Alive}(A, (1, 0))$|
|Only the living die|$\text{Died}(A, \tau) \rightarrow \text{Alive}(A, \tau)$|
|Death is immediate|$\text{Died}(A, \tau) \rightarrow \neg\text{Alive}(A, \text{next}(\tau))$|
|The living stay alive unless they die|$\left(\text{Alive}(A, \tau) \land \neg\text{Died}(A, \tau)\right) \rightarrow \text{Alive}(A, \text{next}(\tau))$|
|The dead stay dead|$\neg\text{Alive}(A, \tau) \rightarrow \neg\text{Alive}(A, \text{next}(\tau))$ <br> **Trouble Brewing never revives anyone. To be weakened when revival is added (e.g. Professor, Shabaloth).**|
|Nearest alive neighbours|$\text{Alive\_Neighbors}(A, B, \tau) \leftrightarrow \text{Alive}(B, \tau) \land \bigvee_{i \ge 1} \left(\left(B = \text{cw}^i(A) \land \bigwedge_{j < i} \neg\text{Alive}(\text{cw}^j(A), \tau)\right) \lor \left(B = \text{ccw}^i(A) \land \bigwedge_{j < i} \neg\text{Alive}(\text{ccw}^j(A), \tau)\right)\right)$, for $B \neq A$ <br> **A has at most two such neighbours, and one if only A and B are alive. Dead players are skipped.**|
|At most one execution per day|$\text{Executed}(A, t) \land \text{Executed}(B, t) \rightarrow A = B$|
|Sober unless afflicted|$\text{Sober}(A, \tau) \leftrightarrow \bigwedge_{S \in \mathcal{S}} \neg\text{Afflicts}(S, A, \tau)$ <br> **A player is sober unless a listed source applies, so $\mathcal{S}$ must list every such role on the script.**|
|Cap per source|$\sum_{A \in \mathcal{P}} [\text{Afflicts}(S, A, \tau)] \le c_S$|
|Total not sober|$\text{Count}(\{A : \neg\text{Sober}(A, \tau)\}) \le \sum_{S \in \mathcal{S}} c_S \cdot \left[\bigvee_{X \in \mathcal{P}} \text{Role}(X, S, \tau)\right]$ <br> **Follows from the two rows above; listed so a solver can use it directly.**|
|Died only if killed|$\text{Died}(A, \tau) \leftrightarrow \bigvee_{S \in \mathcal{K}} \text{Kills}(S, A, \tau)$ <br> **A player dies only if a listed source kills them, and only at a time that source can act, so $\mathcal{K}$ must list every killing source on the script.**|
|At most one kill per source per time|$\sum_{A \in \mathcal{P}} [\text{Kills}(S, A, \tau)] \le 1$|

### Role Invariants
Rules that follow from a role's ability, for every role on the script. A role $S \in \mathcal{S}$ lists its cap $c_S$. Execution is not a role, but its rows are here with the other killing sources in $\mathcal{K}$.

| Role | Invariant | Statement |
| :--- | :--- | :---: |
|Drunk|Cap|$c_{\text{Drunk}} = 1$|
|Drunk|The Drunk is drunk|$\text{Afflicts}(\text{Drunk}, A, \tau) \leftrightarrow \text{Role}(A, \text{Drunk}, \tau)$ <br> **Worked out from the current role alone: if the Drunk's role changes, they become sober.**|
|Poisoner|Cap|$c_{\text{Poisoner}} = 1$|
|Poisoner|Poison starts only when chosen|$\left(\neg\text{Afflicts}(\text{Poisoner}, A, \tau) \land \text{Afflicts}(\text{Poisoner}, A, \text{next}(\tau))\right) \rightarrow \bigvee_{X \in \mathcal{P}} \left(\tau = (t, k_{\text{Poisoner}}) \land \text{Active}(X, \text{Poisoner}, \tau) \land \text{Chose}(X, A, \tau)\right)$ <br> **$k_{\text{Poisoner}}$ is 23 on night 1 and 8 on later nights. A is poisoned from the position after the Poisoner acts.**|
|Poisoner|Poison lasts until dusk|$\left(\text{Afflicts}(\text{Poisoner}, A, \tau) \land \tau \neq (t, D) \land \bigvee_{X \in \mathcal{P}} \left(\text{Role}(X, \text{Poisoner}, \text{next}(\tau)) \land \text{Alive}(X, \text{next}(\tau)) \land (\text{Sober}(X, \text{next}(\tau)) \lor X = A)\right)\right) \rightarrow \text{Afflicts}(\text{Poisoner}, A, \text{next}(\tau))$|
|Poisoner|Poison ends at dusk|$\neg\text{Afflicts}(\text{Poisoner}, A, (t, 1))$ for $t > 1$ <br> **With "Poison starts only when chosen", A stays sober from $(t, 1)$ until the Poisoner acts again.**|
|Poisoner|Poison ends if the Poisoner stops working|$\text{Afflicts}(\text{Poisoner}, A, \tau) \rightarrow \bigvee_{X \in \mathcal{P}} \left(\text{Role}(X, \text{Poisoner}, \tau) \land \text{Alive}(X, \tau) \land (\text{Sober}(X, \tau) \lor X = A)\right)$ <br> **Ends if the Poisoner dies, changes role, or is made not sober by another source. The $X = A$ case lets a Poisoner poison themselves, which would otherwise contradict itself.**|
|Imp|Must choose|$\text{Active}(X, \text{Imp}, (t, k_{\text{Imp}})) \rightarrow \sum_{Y \in \mathcal{P}} [\text{Chose}(X, Y, (t, k_{\text{Imp}}))] = 1$, for $t > 1$ <br> **$k_{\text{Imp}}$ is 30 on later nights; the Imp does not act on night 1. Because the Imp must choose, a night with no deaths means the target was protected or already dead, or the Imp was not sober.**|
|Imp|Kills only on its turn|$\text{Kills}(\text{Imp}, A, \tau) \rightarrow \tau = (t, k_{\text{Imp}}) \land t > 1 \land \bigvee_{X \in \mathcal{P}} \text{Active}(X, \text{Imp}, \tau)$ <br> **A drunk or poisoned Imp kills no one, since $\text{Active}$ needs $\text{Sober}$.**|
|Imp|Target dies unless protected|$\left(\text{Active}(X, \text{Imp}, \tau) \land \text{Chose}(X, A, \tau) \land \text{Alive}(A, \tau) \land \neg\text{Safe}(A, \tau) \land \neg(\text{Role}(A, \text{Soldier}, \tau) \land \text{Sober}(A, \tau)) \land \neg(\text{Role}(A, \text{Mayor}, \tau) \land \text{Sober}(A, \tau))\right) \rightarrow \text{Kills}(\text{Imp}, A, \tau)$, for $\tau = (t, k_{\text{Imp}})$|
|Imp|Only the target dies|$\text{Kills}(\text{Imp}, A, \tau) \rightarrow \bigvee_{X \in \mathcal{P}} \Big(\text{Active}(X, \text{Imp}, \tau) \land \big(\text{Chose}(X, A, \tau) \lor \bigvee_{M \in \mathcal{P}} (\text{Chose}(X, M, \tau) \land \text{Role}(M, \text{Mayor}, \tau) \land \text{Sober}(M, \tau))\big)\Big)$ <br> **The second case is the Mayor: when a sober Mayor is chosen, the Mayor or another player may die instead, at the Storyteller's choice.**|
|Imp|Passes on self-kill|$\left(\text{Role}(X, \text{Imp}, \tau) \land \text{Kills}(\text{Imp}, X, \tau) \land \bigvee_{M \in \mathcal{P}} (\text{Alive}(M, \tau) \land \text{Registers}(M, \text{Minion}, \tau))\right) \rightarrow \sum_{M \in \mathcal{P}} \left[\text{RoleChanged}(\text{Imp}, M, \text{Imp}, \tau) \lor \text{RoleChanged}(\text{Scarlet Woman}, M, \text{Imp}, \tau)\right] = 1$ <br> **Exactly one living player who registers as a Minion becomes the Imp, either through the star-pass or through the Scarlet Woman's own ability. With no such player, no one does, and the game ends.**|
|Imp|Only to a living player who registers as a Minion|$\text{RoleChanged}(\text{Imp}, M, R, \tau) \rightarrow R = \text{Imp} \land \text{Alive}(M, \tau) \land \text{Registers}(M, \text{Minion}, \tau) \land \bigvee_{X \in \mathcal{P}} \left(\text{Role}(X, \text{Imp}, \tau) \land \text{Kills}(\text{Imp}, X, \tau) \land X \neq M\right)$ <br> **$\text{Registers}$ covers the Recluse: a sober Recluse may register as a Minion, so the Storyteller may pass the Demon to them. A Recluse keeps their alignment, so a Good Recluse becomes a Good Imp. They would out themselves and be executed for a Good win, so in that case there is no need to use the solver.**|
|Scarlet Woman|Becomes the Demon|$\text{RoleChanged}(\text{Scarlet Woman}, M, R, \tau) \leftrightarrow \text{Active}(M, \text{Scarlet Woman}, \tau) \land \text{Count}(\{Z : \text{Alive}(Z, \tau)\}) \ge 5 \land \bigvee_{X \in \mathcal{P}} \left(\text{Demon}(X, \tau) \land \text{Role}(X, R, \tau) \land \text{Died}(X, \tau)\right)$ <br> **Forced, not optional, so "$\leftrightarrow$". She becomes the same Demon character that died. The count is taken at $\tau$, when the dying Demon is still alive, so it includes the Demon. Works for a death by day (execution, Slayer) or by night (star-pass).**|
|Scarlet Woman|Takes priority over the star-pass|$\text{RoleChanged}(\text{Scarlet Woman}, M, R, \tau) \rightarrow \bigwedge_{M' \in \mathcal{P}} \neg\text{RoleChanged}(\text{Imp}, M', R, \tau)$ <br> **If the Imp star-passes while her ability fires, she is the one who becomes the Imp.**|
|Monk|Protection lasts the night|$\left(\text{Active}(X, \text{Monk}, (t, k_{\text{Monk}})) \land \text{Chose}(X, A, (t, k_{\text{Monk}})) \land X \neq A\right) \rightarrow \text{Safe}(A, (t, k))$ for every $k_{\text{Monk}} < k \le K_t$, for $t > 1$ <br> **$k_{\text{Monk}}$ is 15 on later nights, so the protection covers the Imp at position 30.**|
|Monk|Safe only when chosen by an active Monk|$\text{Safe}(A, (t, k)) \rightarrow k > k_{\text{Monk}} \land \bigvee_{X \in \mathcal{P}} \left(\text{Active}(X, \text{Monk}, (t, k_{\text{Monk}})) \land \text{Chose}(X, A, (t, k_{\text{Monk}})) \land X \neq A\right)$, for $t > 1$ <br> **$k_{\text{Monk}}$ is 15 on later nights. The Monk cannot choose themselves. Needed because the Imp's "Target dies unless protected" reads $\neg\text{Safe}$: without it, anyone could be Safe and every kill could be blocked.**|
|Slayer|Kills only the Demon (or a Recluse)|$\text{Kills}(\text{Slayer}, B, (t, D)) \rightarrow \bigvee_{X \in \mathcal{P}} \left(\text{Active}(X, \text{Slayer}, (t, D)) \land \text{Chose}(X, B, (t, D)) \land \left(\text{Demon}(B, (t, D)) \lor (\text{Role}(B, \text{Recluse}, (t, D)) \land \text{Sober}(B, (t, D)))\right)\right)$|
|Execution|Execution kills|$\text{Kills}(\text{Execution}, A, (t, D)) \leftrightarrow \text{Executed}(A, t)$ <br> **True in Trouble Brewing. Scripts with a Zombuul, Fool, Devil's Advocate or Tea Lady weaken this to "$\rightarrow$" plus their own exceptions.**|
|Execution|Executions only by day|$\text{Kills}(\text{Execution}, A, \tau) \rightarrow \tau = (t, D)$|

### General Event situations
These are public events, announced by the Storyteller or seen by everyone, so they hold regardless of any player's alignment. Only Trouble Brewing roles are accounted for.
| Situation | Statement |
| :--- | :---: |
| On day $t$, the Storyteller announces that exactly the players in $X$ died last night ($X$ may be empty) | For each $A \in X$: $\text{Alive}(A, (t, 1)) \land \neg\text{Alive}(A, (t, D))$ <br> For each $B \notin X$: $\text{Alive}(B, (t, 1)) \rightarrow \text{Alive}(B, (t, D))$ <br> **Night deaths happen between $(t, 1)$ and $(t, K_t)$; they are announced at dawn of day $t$.** |
| A is executed on day $t$ | $\text{Executed}(A, t)$ <br> **A's death at $(t, D)$ follows from "Execution kills" and "Died only if killed".** |
| The game is still going on day $d_{\text{cur}}$ | $\bigvee_{X \in \mathcal{P}} \left(\text{Demon}(X, \tau) \land \text{Alive}(X, \tau)\right)$ for every $\tau$ up to and including $(d_{\text{cur}}, D)$ <br> **Role changes take effect at $\text{next}(\tau)$, the same time a dying Demon is dead. So if the Demon dies at $\tau$ with no successor (star-pass or Scarlet Woman), no living Demon exists at $\text{next}(\tau)$. Other ways to end a game (2 players alive, Saint, Mayor) are not covered yet.** |

### Role-Event situations
#### Note: 'A' claims ROLE means player 'A' is claming role ROLE
#### Note: $d_{\text{cur}}$ is the current day, $(d_{\text{cur}}, D)$, on which the solver is being run. A claim is trusted only if $A$ is Good at $d_{\text{cur}}$: alignment can change several times, and only $A$'s alignment now decides whether $A$ is telling the truth.
| Situation | Statement |
| :--- | :---: |
| A claims Washerwoman, sees B or C is Townsfolk R | $(\text{Sober}(A, (1, k_{\text{Washerwoman}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow R \in \mathcal{T} \land ((\text{Role}(B, R, (1, k_{\text{Washerwoman}})) \oplus \text{Role}(C, R, (1, k_{\text{Washerwoman}}))) \lor (\text{Role}(B, \text{Spy}, (1, k_{\text{Washerwoman}})) \land \text{Sober}(B, (1, k_{\text{Washerwoman}}))) \lor (\text{Role}(C, \text{Spy}, (1, k_{\text{Washerwoman}})) \land \text{Sober}(C, (1, k_{\text{Washerwoman}}))))$ <br>Note: $k_{\text{Washerwoman}}$ = 42, refer to [nightorder.json](nightorder.json)|
| A claims Librarian, sees B or C is Outsider R | $(\text{Sober}(A, (1, k_{\text{Librarian}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow R \in \mathcal{O} \land ((\text{Role}(B, R, (1, k_{\text{Librarian}})) \oplus \text{Role}(C, R, (1, k_{\text{Librarian}})))  \lor(\text{Role}(B, \text{Spy}, (1, k_{\text{Librarian}})) \land \text{Sober}(B, (1, k_{\text{Librarian}}))) \lor (\text{Role}(C, \text{Spy}, (1, k_{\text{Librarian}})) \land \text{Sober}(C, (1, k_{\text{Librarian}}))))$  |
| A claims Librarian, sees zero Outsiders | $(\text{Sober}(A, (1, k_{\text{Librarian}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow \bigwedge_{B \in \mathcal{P}} \neg\text{Registers}(B, \text{Outsider}, (1, k_{\text{Librarian}}))$ <br> **A sober Recluse may register as a Minion or Demon, so it can be hidden. A sober Spy that registered as an Outsider would have to be shown, so here it registers as something else.**|
| A claims Investigator, sees B or C is Minion R | $(\text{Sober}(A, (1, k_{\text{Investigator}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow R \in \mathcal{M} \land ((\text{Role}(B, R, (1, k_{\text{Investigator}})) \oplus \text{Role}(C, R, (1, k_{\text{Investigator}}))) \lor (\text{Role}(B, \text{Recluse}, (1, k_{\text{Investigator}})) \land \text{Sober}(B, (1, k_{\text{Investigator}}))) \lor (\text{Role}(C, \text{Recluse}, (1, k_{\text{Investigator}})) \land \text{Sober}(C, (1, k_{\text{Investigator}}))))$ |
| A claims Chef, sees N | $(\text{Sober}(A, (1, k_{\text{Chef}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow \left(\sum_{B \in \mathcal{P}} [\text{Registers}(B, \text{Evil}, (1, k_{\text{Chef}})) \land \text{Registers}(\text{cw}^1(B), \text{Evil}, (1, k_{\text{Chef}}))] = N\right)$ <br> **Counts every adjacent pair $(B, \text{cw}^1(B))$ once, including pairs with the Chef in them.**|
| A claims Empath, sees N| $(\text{Sober}(A, (t, k_{\text{Empath}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow \left(\sum_{B \neq A} [\text{Alive\_Neighbors}(A, B, (t, k_{\text{Empath}})) \land \text{Registers}(B, \text{Evil}, (t, k_{\text{Empath}}))] = N\right)$ |
| A claims Fortune Teller, chooses B and C, sees Yes | $\left(\text{Align}(A, \text{Good}, d_{\text{cur}}) \rightarrow \text{Chose}(A, B, (t, k_{\text{Fortune Teller}})) \land \text{Chose}(A, C, (t, k_{\text{Fortune Teller}}))\right) \land$<br>$(\text{Sober}(A, (t, k_{\text{Fortune Teller}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow \text{Registers}(B, \text{Demon}, (t, k_{\text{Fortune Teller}})) \lor \text{Registers}(C, \text{Demon}, (t, k_{\text{Fortune Teller}})) \lor \text{Redherring}(B, (t, k_{\text{Fortune Teller}})) \lor \text{Redherring}(C, (t, k_{\text{Fortune Teller}}))$ <br> **$k_{\text{Fortune Teller}}$ is 47 on night 1 and 66 on later nights. $\text{Registers}$ covers a sober Recluse registering as a Demon. A Good Fortune Teller chooses even if drunk or poisoned, so the choice does not depend on Sober.**|
| A claims Fortune Teller, chooses B and C, sees No | $\left(\text{Align}(A, \text{Good}, d_{\text{cur}}) \rightarrow \text{Chose}(A, B, (t, k_{\text{Fortune Teller}})) \land \text{Chose}(A, C, (t, k_{\text{Fortune Teller}}))\right) \land$<br>$(\text{Sober}(A, (t, k_{\text{Fortune Teller}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow \neg\text{Registers}(B, \text{Demon}, (t, k_{\text{Fortune Teller}})) \land \neg\text{Registers}(C, \text{Demon}, (t, k_{\text{Fortune Teller}})) \land \neg\text{Redherring}(B, (t, k_{\text{Fortune Teller}})) \land \neg\text{Redherring}(C, (t, k_{\text{Fortune Teller}}))$ <br> **A sober Recluse may register as a non-Demon, so a No does not rule out a Recluse.**|
| A claims Undertaker, sees B as role R on night t | $(\text{Sober}(A, (t, k_{\text{Undertaker}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow \text{Executed}(B, t-1) \land \text{Died}(B, (t-1, D)) \land$<br>$\Big(\text{Role}(B, R, (t-1, D)) \lor \big(\text{Role}(B, \text{Spy}, (t-1, D)) \land \text{Sober}(B, (t, k_{\text{Undertaker}})) \land R \in \mathcal{T} \cup \mathcal{O}\big) \lor \big(\text{Role}(B, \text{Recluse}, (t-1, D)) \land \text{Sober}(B, (t, k_{\text{Undertaker}})) \land R \in \mathcal{M} \cup \mathcal{D}\big)\Big)$ <br> **$k_{\text{Undertaker}}$ is 67 on later nights; the Undertaker does not act on night 1. The Undertaker only learns a character if the executed player died, so both $\text{Executed}$ and $\text{Died}$ are needed. The role is read at $(t-1, D)$, when B died: a dead player keeps their role, so this is the character B died as. A sober Spy may appear as a Townsfolk or Outsider, and a sober Recluse as a Minion or Demon.**|
| A claims Undertaker, sees nothing on night t | $(\text{Sober}(A, (t, k_{\text{Undertaker}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow \bigwedge_{B \in \mathcal{P}} \neg\left(\text{Executed}(B, t-1) \land \text{Died}(B, (t-1, D))\right)$ <br> **No one died by execution on day $t-1$: either no one was executed, or the executed player did not die.**|
| A claims Monk, chose B on night t| $\text{Align}(A, \text{Good}, d_{\text{cur}}) \rightarrow \text{Chose}(A, B, (t, k_{\text{Monk}}))$ <br> **A Good Monk chooses even if drunk or poisoned, so this does not depend on Sober. Whether B is Safe then follows from the Monk rows in Role Invariants, which need the Monk to be active (alive and sober). Works regardless of the alignment of B.**|
| A claims Ravenkeeper, chose B on night t, and saw role R (Good)| $\left(\text{Align}(A, \text{Good}, d_{\text{cur}}) \rightarrow \bigvee_{k < k_{\text{Ravenkeeper}}} \text{Died}(A, (t, k))\right) \land$<br>$(\text{Sober}(A, (t, k_{\text{Ravenkeeper}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}}))\rightarrow\text{Role}(B, R,(t, k_{\text{Ravenkeeper}})) \lor(\text{Role}(B, \text{Spy}, (t, k_{\text{Ravenkeeper}})) \land \text{Sober}(B, (t, k_{\text{Ravenkeeper}}))) $ <br> **Note: a drunk or poisoned Ravenkeeper still wakes when it dies at night, so Died does not depend on Sober. Only possible for $t > 1$.**|
| A claims Ravenkeeper, chose B on night t, and saw role R (Evil)| $\left(\text{Align}(A, \text{Good}, d_{\text{cur}}) \rightarrow \bigvee_{k < k_{\text{Ravenkeeper}}} \text{Died}(A, (t, k))\right) \land$<br>$(\text{Sober}(A, (t, k_{\text{Ravenkeeper}})) \land \text{Align}(A, \text{Good}, d_{\text{cur}}))\rightarrow\text{Role}(B, R,(t, k_{\text{Ravenkeeper}})) \lor(\text{Role}(B, \text{Recluse}, (t, k_{\text{Ravenkeeper}})) \land \text{Sober}(B, (t, k_{\text{Ravenkeeper}}))) $ <br> **Note: a drunk or poisoned Ravenkeeper still wakes when it dies at night, so Died does not depend on Sober. Only possible for $t > 1$.**|
| A claims Virgin, nominated by B on day t (B executed) | $\text{Executed}(B, t) \land \text{Active}(A, \text{Virgin}, (t, D)) \land \text{Registers}(B, \text{Townsfolk}, (t, D))$ <br> **A public event: the Storyteller only executes B on the spot if A's Virgin ability works, so this does not depend on A's alignment. $\text{Registers}$ covers a sober Spy registering as a Townsfolk. The ability only works on A's first nomination; nominations are not tracked yet.**|
| A claims Virgin, nominated by B on day t (B not executed) | $(\text{Sober}(A, (t, D)) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow \neg\text{Registers}(B, \text{Townsfolk}, (t, D))$ <br> **Only applies if this was A's first nomination.**|
| A claims Slayer, shoots B on day t (B dies) | $\text{Kills}(\text{Slayer}, B, (t, D))$ <br> **The Slayer row in Role Invariants then gives that A is an active Slayer who chose B, and B is the Demon or a sober Recluse.**|
| A claims Slayer, shoots B on day t (B lives) | $(\text{Sober}(A, (t, D)) \land \text{Align}(A, \text{Good}, d_{\text{cur}})) \rightarrow \neg\text{Demon}(B, (t, D))$ <br> **Only A's first shot is entered; later shots are not tracked.**|
