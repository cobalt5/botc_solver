# Sobriety draft

**Done:** Afflicts, Active, the general rules, the Drunk and the Poisoner are now in situations.md. The rest is reference for other scripts.

Draft of what would go into [situations.md](../../situations.md) to resolve [sobriety.txt](sobriety.txt).
Drunk and poisoned are treated as the same thing: both make a player not sober.

## New predicate and notation

| Claim | Meaning |
| :--- | :---: |
| Afflicts(S, A, $\tau$) | The ability of role $S$ is making player $A$ not sober (drunk or poisoned) at time $\tau$ |

Let $\mathcal{S}$ be the set of roles on the script that can make a player not sober (Trouble Brewing: $\{\text{Drunk}, \text{Poisoner}\}$).
Each $S \in \mathcal{S}$ has a cap $c_S$: the most players it can affect at once.

Shorthand, for a player $X$ holding a working copy of $S$:
$$\text{Active}(X, S, \tau) \equiv \text{Role}(X, S, \tau) \land \text{Alive}(X, \tau) \land \text{Sober}(X, \tau)$$

## General rules (every kind)

| Invariant | Statement |
| :--- | :---: |
| Sober unless afflicted | $\text{Sober}(A, \tau) \leftrightarrow \bigwedge_{S \in \mathcal{S}} \neg\text{Afflicts}(S, A, \tau)$ <br> **The "$\leftrightarrow$" is what makes this constraining: a player is sober unless a listed source applies.** |
| Cap per source | $\sum_{A \in \mathcal{P}} [\text{Afflicts}(S, A, \tau)] \le c_S$ |
| Global count (derived) | $\text{Count}(\{A : \neg\text{Sober}(A, \tau)\}) \le \sum_{S \in \mathcal{S}} c_S \cdot [\bigvee_{X} \text{Role}(X, S, \tau)]$ <br> **Follows from the two rows above; listed so a solver can use it directly.** |

## Kind 1: Self (Drunk)

The holder of the role is the one affected. Cap $c_{\text{Drunk}} = 1$.

| Invariant | Statement |
| :--- | :---: |
| The Drunk is drunk | $\text{Afflicts}(\text{Drunk}, A, \tau) \leftrightarrow \text{Role}(A, \text{Drunk}, \tau)$ |

## Kind 2: Switching (Poisoner)

A target is chosen again at each of the role's night positions, and the effect lasts until dusk.
Cap $c_{\text{Poisoner}} = 1$. $k_P$ is the Poisoner's position on night $t$ (23 on night 1, 8 on later nights).

| Invariant | Statement |
| :--- | :---: |
| Starts only when chosen | $\left(\neg\text{Afflicts}(\text{Poisoner}, A, \tau) \land \text{Afflicts}(\text{Poisoner}, A, \text{next}(\tau))\right) \rightarrow \bigvee_{X} \left(\tau = (t, k_P) \land \text{Active}(X, \text{Poisoner}, \tau) \land \text{Chose}(X, A, \tau)\right)$ |
| Lasts until dusk | $\left(\text{Afflicts}(\text{Poisoner}, A, \tau) \land \tau \neq (t, D) \land \bigvee_X \text{Active}(X, \text{Poisoner}, \text{next}(\tau))\right) \rightarrow \text{Afflicts}(\text{Poisoner}, A, \text{next}(\tau))$ |
| Ends at dusk | $\neg\text{Afflicts}(\text{Poisoner}, A, (t, 1))$ for $t > 1$ <br> **With "Starts only when chosen", A stays sober from $(t, 1)$ until the Poisoner acts again.** |
| Ends if the Poisoner dies or is poisoned | $\text{Afflicts}(\text{Poisoner}, A, \tau) \rightarrow \bigvee_X \text{Active}(X, \text{Poisoner}, \tau)$ <br> **Edge case: a Poisoner who poisons themselves would make this contradictory; exempt $A = X$ if that should be allowed.** |

**Variant (Sailor):** same rows with $k_{\text{Sailor}}$, except the target is the chosen player or the Sailor:
$\left(\neg\text{Afflicts}(\text{Sailor}, A, \tau) \land \text{Afflicts}(\text{Sailor}, A, \text{next}(\tau))\right) \rightarrow \bigvee_{X, B} \left(\tau = (t, k_{\text{Sailor}}) \land \text{Active}(X, \text{Sailor}, \tau) \land \text{Chose}(X, B, \tau) \land (A = B \lor A = X)\right)$

## Kind 3: Permanent (Widow, Sweetheart)

A target is fixed once and then stays affected.

**Widow** (chosen on night 1, lasts while the Widow is active). Cap $c_{\text{Widow}} = 1$.

| Invariant | Statement |
| :--- | :---: |
| Starts only on night 1 | $\left(\neg\text{Afflicts}(\text{Widow}, A, \tau) \land \text{Afflicts}(\text{Widow}, A, \text{next}(\tau))\right) \rightarrow \bigvee_{X} \left(\tau = (1, k_{\text{Widow}}) \land \text{Active}(X, \text{Widow}, \tau) \land \text{Chose}(X, A, \tau)\right)$ |
| Persists | $\left(\text{Afflicts}(\text{Widow}, A, \tau) \land \bigvee_X \text{Active}(X, \text{Widow}, \text{next}(\tau))\right) \rightarrow \text{Afflicts}(\text{Widow}, A, \text{next}(\tau))$ |
| Ends if the Widow dies or is poisoned | $\text{Afflicts}(\text{Widow}, A, \tau) \rightarrow \bigvee_X \text{Active}(X, \text{Widow}, \tau)$ |

**Sweetheart** (triggered by the Sweetheart's death, then lasts forever). Cap $c_{\text{Sweetheart}} = 1$.

| Invariant | Statement |
| :--- | :---: |
| Starts only when the Sweetheart dies | $\left(\neg\text{Afflicts}(\text{Sweetheart}, A, \tau) \land \text{Afflicts}(\text{Sweetheart}, A, \text{next}(\tau))\right) \rightarrow \bigvee_{X} \left(\text{Role}(X, \text{Sweetheart}, \tau) \land \text{Sober}(X, \tau) \land \text{Died}(X, \tau)\right)$ |
| Persists while the role is held | $\left(\text{Afflicts}(\text{Sweetheart}, A, \tau) \land \bigvee_X \text{Role}(X, \text{Sweetheart}, \text{next}(\tau))\right) \rightarrow \text{Afflicts}(\text{Sweetheart}, A, \text{next}(\tau))$ |
| Ends if the role changes | $\text{Afflicts}(\text{Sweetheart}, A, \tau) \rightarrow \bigvee_X \text{Role}(X, \text{Sweetheart}, \tau)$ <br> **Like the Spy and Recluse, the ability works while dead (no $\text{Alive}$), but stops if the Sweetheart's role is changed.** |

## Kind 4: Positional (No Dashii)

No choice: the targets follow from seating and who is alive, so the rule is "$\leftrightarrow$". Cap $c_{\text{No Dashii}} = 2$.

| Invariant | Statement |
| :--- | :---: |
| Nearest alive Townsfolk | $\text{TownsNeighbor}(X, A, \tau) \leftrightarrow \text{Townsfolk}(A, \tau) \land \text{Alive}(A, \tau) \land \bigvee_{i \ge 1} \Big( \big(A = \text{cw}^i(X) \land \bigwedge_{j < i} \neg(\text{Townsfolk}(\text{cw}^j(X), \tau) \land \text{Alive}(\text{cw}^j(X), \tau))\big) \lor \big(A = \text{ccw}^i(X) \land \bigwedge_{j < i} \neg(\text{Townsfolk}(\text{ccw}^j(X), \tau) \land \text{Alive}(\text{ccw}^j(X), \tau))\big) \Big)$ |
| No Dashii poisons them | $\text{Afflicts}(\text{No Dashii}, A, \tau) \leftrightarrow \bigvee_X \left(\text{Active}(X, \text{No Dashii}, \tau) \land \text{TownsNeighbor}(X, A, \tau)\right)$ <br> **Targets change as players die, with no choice involved; this is why positional is separate from permanent.** |

## Not in scope

Vortox, Marionette and Lunatic give false information without making anyone drunk or poisoned. They belong with truthful-claims, not here.
