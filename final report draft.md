# IEOR E4004 — Project II: Scheduling the NBA

**Spring 2026**

Angel Huang (ah4422) · Cole Zemel (ccz2107) · Nathan Layani (ndl2127) · Suyi Li (sl5867)

---

## Table of Contents

1. [Question 1 — Schedule Analysis](#question-1--schedule-analysis)
2. [Question 2 — Integer Programming Formulation](#question-2--integer-programming-formulation)
3. [Question 3 — Timezone Travel Constraint](#question-3--timezone-travel-constraint)
4. [Question 4 — Minimizing Maximum Travel Distance](#question-4--minimizing-maximum-travel-distance)

---

## Question 1 — Schedule Analysis

For each team $i$ we extracted the following four items from the preliminary schedule:

- **(a)** All dates when team $i$ played at home.
- **(b)** For each opponent $j$, the number of times team $i$ hosted $j$ at home.
- **(c)** For each opponent $j$, the number of times team $i$ played away at $j$'s arena.
- **(d)** All dates when team $i$ played away.

### (a) & (d) Home and Away Dates

The table below lists each team's home game dates (a) and away game dates (d). The schedule spans November 1 – December 25, 2025 across 16 dates, with 8 games per date (128 total).

| Team | (a) Home Dates | (d) Away Dates |
|---|---|---|
| Atlanta Hawks | Nov 03, 07, 15, 17, 19, 23, 27, 28, 29; Dec 25 | Nov 01, 05, 11, 13, 21; Dec 01 |
| Boston Celtics | Nov 01, 07, 13, 19, 23, 28 | Nov 03, 05, 11, 15, 17, 21, 27, 29; Dec 01, 25 |
| Brooklyn Nets | Nov 01, 05, 11, 13, 21, 29; Dec 01 | Nov 03, 07, 15, 17, 19, 23, 27, 28; Dec 25 |
| Chicago Bulls | Nov 01, 03, 13, 21, 23, 27, 28, 29; Dec 01 | Nov 05, 07, 11, 15, 17, 19; Dec 25 |
| Cleveland Cavaliers | Nov 03, 07, 17, 19, 21, 23, 28, 29; Dec 01, 25 | Nov 01, 05, 11, 13, 15, 27 |
| Dallas Mavericks | Nov 03, 07, 15, 17, 19, 23, 27, 28; Dec 25 | Nov 01, 05, 11, 13, 21, 29; Dec 01 |
| Denver Nuggets | Nov 05, 07, 11, 15, 17, 19, 21; Dec 25 | Nov 01, 03, 13, 23, 27, 28, 29; Dec 01 |
| Golden State Warriors | Nov 03, 05, 11, 15, 17, 27; Dec 25 | Nov 01, 07, 13, 19, 21, 23, 28, 29; Dec 01 |
| Houston Rockets | Nov 03, 11, 13, 15, 19, 21, 23, 27; Dec 25 | Nov 01, 05, 07, 17, 28, 29; Dec 01 |
| Los Angeles Lakers | Nov 03, 05, 11, 15, 17, 19; Dec 25 | Nov 01, 07, 13, 21, 23, 27, 28, 29; Dec 01 |
| Miami Heat | Nov 01, 05, 13, 17, 27, 28, 29; Dec 01 | Nov 03, 07, 11, 15, 19, 21, 23; Dec 25 |
| Milwaukee Bucks | Nov 01, 07, 13, 21, 23, 27, 28, 29; Dec 01 | Nov 03, 05, 11, 15, 17, 19; Dec 25 |
| New York Knicks | Nov 01, 05, 11, 13, 15; Dec 01 | Nov 03, 07, 17, 19, 21, 23, 27, 28, 29; Dec 25 |
| Philadelphia 76ers | Nov 01, 05, 07, 11, 21, 29; Dec 01 | Nov 03, 13, 15, 17, 19, 23, 27, 28; Dec 25 |
| Phoenix Suns | Nov 03, 11, 13, 15, 19, 21, 23; Dec 25 | Nov 01, 05, 07, 17, 27, 28, 29; Dec 01 |
| Toronto Raptors | Nov 01, 05, 07, 17, 27, 28, 29; Dec 01 | Nov 03, 11, 13, 15, 19, 21, 23; Dec 25 |

### (b) Home Matchup Counts — $c^H(i,j)$

Each cell shows the number of times the row team $i$ hosted the column team $j$ at home. A value of 0 indicates no home game between that pair; "—" marks a team against itself.

| Team \ Opp. | ATL | BOS | BKN | CHI | CLE | DAL | DEN | GSW | HOU | LAL | MIA | MIL | NYK | PHI | PHX | TOR |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Atlanta Hawks | — | 1 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 1 | 1 | 1 | 1 | 0 | 1 | 1 |
| Boston Celtics | 0 | — | 0 | 0 | 0 | 1 | 0 | 1 | 0 | 0 | 1 | 1 | 0 | 1 | 1 | 0 |
| Brooklyn Nets | 1 | 1 | — | 0 | 0 | 1 | 1 | 1 | 0 | 0 | 1 | 0 | 0 | 1 | 0 | 0 |
| Chicago Bulls | 1 | 1 | 1 | — | 0 | 0 | 1 | 1 | 1 | 1 | 0 | 1 | 0 | 0 | 0 | 1 |
| Cleveland Cavaliers | 1 | 1 | 1 | 1 | — | 0 | 1 | 0 | 1 | 0 | 1 | 1 | 1 | 0 | 0 | 1 |
| Dallas Mavericks | 0 | 0 | 0 | 1 | 1 | — | 1 | 1 | 1 | 1 | 1 | 0 | 0 | 1 | 0 | 1 |
| Denver Nuggets | 1 | 1 | 1 | 0 | 0 | 0 | — | 0 | 1 | 1 | 1 | 0 | 1 | 1 | 0 | 0 |
| Golden State Warriors | 0 | 1 | 0 | 0 | 1 | 0 | 1 | — | 1 | 0 | 0 | 1 | 1 | 1 | 0 | 0 |
| Houston Rockets | 1 | 1 | 1 | 0 | 0 | 0 | 0 | 0 | — | 1 | 0 | 1 | 1 | 1 | 1 | 1 |
| Los Angeles Lakers | 0 | 1 | 1 | 0 | 1 | 0 | 0 | 1 | 0 | — | 0 | 0 | 1 | 0 | 1 | 1 |
| Miami Heat | 0 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 1 | 1 | — | 0 | 1 | 1 | 1 | 0 |
| Milwaukee Bucks | 0 | 0 | 1 | 0 | 1 | 1 | 1 | 0 | 0 | 1 | 1 | — | 1 | 1 | 1 | 0 |
| New York Knicks | 0 | 1 | 1 | 1 | 0 | 1 | 0 | 0 | 0 | 1 | 0 | 0 | — | 0 | 0 | 1 |
| Philadelphia 76ers | 1 | 0 | 0 | 1 | 1 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 1 | — | 1 | 1 |
| Phoenix Suns | 0 | 0 | 1 | 1 | 1 | 1 | 1 | 1 | 0 | 0 | 0 | 0 | 1 | 1 | — | 0 |
| Toronto Raptors | 0 | 1 | 1 | 0 | 0 | 0 | 1 | 1 | 1 | 0 | 1 | 1 | 0 | 0 | 1 | — |

### (c) Away Matchup Counts — $c^A(i,j)$

Each cell shows the number of times the row team $i$ played away at the column team $j$'s arena.

| Team \ Opp. | ATL | BOS | BKN | CHI | CLE | DAL | DEN | GSW | HOU | LAL | MIA | MIL | NYK | PHI | PHX | TOR |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Atlanta Hawks | — | 0 | 1 | 1 | 1 | 0 | 1 | 0 | 1 | 0 | 0 | 0 | 0 | 1 | 0 | 0 |
| Boston Celtics | 1 | — | 1 | 1 | 1 | 0 | 1 | 1 | 1 | 1 | 0 | 0 | 1 | 0 | 0 | 1 |
| Brooklyn Nets | 0 | 0 | — | 1 | 1 | 0 | 1 | 0 | 1 | 1 | 0 | 1 | 1 | 0 | 1 | 1 |
| Chicago Bulls | 1 | 0 | 0 | — | 1 | 1 | 0 | 0 | 0 | 0 | 1 | 0 | 1 | 1 | 1 | 0 |
| Cleveland Cavaliers | 0 | 0 | 0 | 0 | — | 1 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 1 | 1 | 0 |
| Dallas Mavericks | 1 | 1 | 1 | 0 | 0 | — | 0 | 0 | 0 | 0 | 1 | 1 | 1 | 0 | 1 | 0 |
| Denver Nuggets | 0 | 0 | 1 | 1 | 1 | 1 | — | 1 | 0 | 0 | 0 | 1 | 0 | 0 | 1 | 1 |
| Golden State Warriors | 1 | 1 | 1 | 1 | 0 | 1 | 0 | — | 0 | 1 | 1 | 0 | 0 | 0 | 1 | 1 |
| Houston Rockets | 0 | 0 | 0 | 1 | 1 | 1 | 1 | 1 | — | 0 | 1 | 0 | 0 | 0 | 0 | 1 |
| Los Angeles Lakers | 1 | 0 | 0 | 1 | 0 | 1 | 1 | 0 | 1 | — | 1 | 1 | 1 | 1 | 0 | 0 |
| Miami Heat | 1 | 1 | 1 | 0 | 1 | 1 | 1 | 0 | 0 | 0 | — | 1 | 0 | 0 | 0 | 1 |
| Milwaukee Bucks | 1 | 1 | 0 | 1 | 1 | 0 | 0 | 1 | 1 | 0 | 0 | — | 0 | 0 | 0 | 1 |
| New York Knicks | 1 | 0 | 0 | 0 | 1 | 0 | 1 | 1 | 1 | 1 | 1 | 1 | — | 1 | 1 | 0 |
| Philadelphia 76ers | 0 | 1 | 1 | 0 | 0 | 1 | 1 | 1 | 1 | 0 | 1 | 1 | 0 | — | 1 | 0 |
| Phoenix Suns | 1 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 1 | 1 | 1 | 0 | 1 | — | 1 |
| Toronto Raptors | 1 | 0 | 0 | 1 | 1 | 1 | 0 | 0 | 1 | 1 | 0 | 0 | 1 | 1 | 0 | — |

---

## Question 2 — Integer Programming Formulation

We formulate a feasibility Integer Program (IP) whose feasible solutions are exactly the valid schedules — those that preserve all structural properties of the original draft while potentially reassigning which teams play each other on each date. No objective function is included; we seek any feasible solution.

### Sets and Indices

$$T = \text{set of all teams} \quad (|T| = 16)$$
$$D = \text{set of all game dates} \quad (|D| = 16)$$
$$i, j \in T \text{ denote teams } (i \neq j), \quad d \in D \text{ denotes a game date}$$

### Parameters

From Question 1:

- $H(i)$ = set of dates on which team $i$ plays at home
- $A(i)$ = set of dates on which team $i$ plays away
- $c^H(i,j)$ = number of times team $i$ hosts team $j$ [part (b)]
- $c^A(i,j)$ = number of times team $i$ visits team $j$'s arena [part (c)]

### Decision Variables

Define binary variables:

$$x_{ijd} \in \{0,1\} \quad \forall\, i \neq j \in T,\; d \in D$$

where $x_{ijd} = 1$ if and only if team $i$ hosts team $j$ on date $d$.

### Constraints

**(e) Home Dates Preserved.** For each team $i$ and date $d$, team $i$ plays exactly one home game if $d$ is a home date, and none otherwise:

$$\sum_{j \neq i} x_{ijd} = \mathbf{1}[d \in H(i)] \quad \forall\, i \in T,\; d \in D$$

**(f) Away Dates Preserved.** For each team $i$ and date $d$, team $i$ plays exactly one away game if $d$ is an away date, and none otherwise:

$$\sum_{j \neq i} x_{jid} = \mathbf{1}[d \in A(i)] \quad \forall\, i \in T,\; d \in D$$

**(g) Home Matchup Counts Preserved.** Over all dates, team $i$ hosts team $j$ exactly $c^H(i,j)$ times:

$$\sum_{d \in D} x_{ijd} = c^H(i,j) \quad \forall\, i \neq j \in T$$

**(h) Away Matchup Counts Preserved.** Over all dates, team $i$ visits team $j$'s arena exactly $c^A(i,j)$ times:

$$\sum_{d \in D} x_{jid} = c^A(i,j) \quad \forall\, i \neq j \in T$$

### Complete Formulation

$$\textbf{Find} \quad x_{ijd} \in \{0,1\} \quad \forall\, i \neq j \in T,\; d \in D$$

subject to:

$$\sum_{j \neq i} x_{ijd} = \mathbf{1}[d \in H(i)] \quad \forall\, i \in T,\; d \in D \tag{e}$$

$$\sum_{j \neq i} x_{jid} = \mathbf{1}[d \in A(i)] \quad \forall\, i \in T,\; d \in D \tag{f}$$

$$\sum_{d \in D} x_{ijd} = c^H(i,j) \quad \forall\, i \neq j \in T \tag{g}$$

$$\sum_{d \in D} x_{jid} = c^A(i,j) \quad \forall\, i \neq j \in T \tag{h}$$

$$x_{ijd} \in \{0,1\} \quad \forall\, i \neq j \in T,\; d \in D$$

### Model Size and Result

- **Variables:** $16 \times 15 \times 16 = 3{,}840$ binary variables
- **Constraints:** $2 \times 16 \times 16 + 2 \times 16 \times 15 = 992$ linear constraints

Gurobi returns `Status: Optimal`, confirming the constraint system is consistent and at least one valid reassignment of opponents exists.

---

## Question 3 — Timezone Travel Constraint

### Additional Constraint

Building on the feasibility model of Question 2, we add a constraint limiting how much timezone shift a team can experience across three consecutive games. For each team $t$ and each triple of consecutive game dates $(d_1, d_2, d_3)$, the sum of consecutive timezone differences must not reach 4 or more:

$$|tz(t,d_2) - tz(t,d_1)| + |tz(t,d_3) - tz(t,d_2)| \leq 3 \quad \forall\, t \in T,\; \text{consecutive triples } (d_1,d_2,d_3)$$

where $tz(t,d)$ denotes the UTC offset of the arena where team $t$ plays on date $d$. Each arena is assigned to one of four timezone groups: Eastern (UTC−5), Central (UTC−6), Mountain (UTC−7), or Pacific (UTC−8).

### Linearization

Since $tz(t,d)$ is itself a linear expression in the $x$ variables, the absolute-value terms are linearized in the standard way. Let $\delta_{12} = tz(t,d_2) - tz(t,d_1)$ and $\delta_{23} = tz(t,d_3) - tz(t,d_2)$. Introduce auxiliary variables $p_{12}, p_{23} \geq 0$:

$$p_{12} \geq \delta_{12}, \quad p_{12} \geq -\delta_{12} \implies p_{12} = |\delta_{12}|$$
$$p_{23} \geq \delta_{23}, \quad p_{23} \geq -\delta_{23} \implies p_{23} = |\delta_{23}|$$
$$p_{12} + p_{23} \leq 3$$

### Result: Infeasibility

The model is **infeasible** — Gurobi proves that no schedule satisfying all constraints (e)–(h) from Question 2 can simultaneously satisfy the timezone constraint for every team.

To diagnose this, we compute the IIS (Irreducible Inconsistent Subsystem) — the smallest subset of constraints that is still collectively infeasible. The IIS identifies that the root cause lies with Western Conference teams, particularly **Golden State Warriors**, **Los Angeles Lakers**, and **Phoenix Suns**, which are forced by the fixed home/away date structure to make large coast-to-coast timezone jumps within consecutive game triples. Because the timezone spread from Pacific (UTC−8) to Eastern (UTC−5) is 3 hours, any back-to-back crossing of two such timezone boundaries produces a diff sum of 4 or more. The matchup-count constraints (g and h) then prevent any rearrangement of opponents from resolving all violations simultaneously.

### Relaxation via Slack Variables

Since the hard constraint is infeasible, we relax it by introducing a non-negative slack variable $s(t,k)$ for each team $t$ and consecutive triple $k$. The constraint becomes:

$$p_{12} + p_{23} \leq 3 + s(t,k), \quad s(t,k) \geq 0$$

We then minimize the total slack across all teams and triples:

$$\min \sum_{t,\,k} s(t,k)$$

This finds the schedule that comes closest to satisfying the timezone constraint. The optimal value is **27 hours**, distributed across 10 teams and 18 triples. Golden State Warriors accounts for the largest share (10 of the 27 excess hours across 5 triples), consistent with their position as the westernmost team in the schedule.

---

## Question 4 — Minimizing Maximum Travel Distance

For Question 4, we considered two different optimization objectives:

- **Method 1** minimizes the maximum total travel distance across teams.
- **Method 2** minimizes the maximum deviation from the league-average travel distance.

### Method 1 — Minimizing Maximum Travel Distance

#### Objective

We improve the original schedule by minimizing the maximum total travel distance among all teams. Introducing a scalar variable $T^*$, the objective is:

$$\min\; T^* \quad \text{subject to: } \mathrm{travel}(t) \leq T^* \;\; \forall\, t \in T$$

This is a minimax formulation: $T^*$ equals the worst-case team travel distance, and we minimize it. The schedule is otherwise constrained by (e)–(h) from Question 2.

#### Computing Travel Distance

Travel distances are computed as great-circle (Haversine) distances in miles between consecutive arena locations for each team. Four cases arise:

- **Home → Home (HH):** zero travel — both games at the team's own arena.
- **Home → Away (HA):** $\mathrm{dist}(\mathrm{arena}(t),\,\mathrm{arena}(h_2))$ where $h_2$ is the host on $d_2$. Linear in $x$.
- **Away → Home (AH):** $\mathrm{dist}(\mathrm{arena}(h_1),\,\mathrm{arena}(t))$ where $h_1$ is the host on $d_1$. Linear in $x$.
- **Away → Away (AA):** $\mathrm{dist}(\mathrm{arena}(h_1),\,\mathrm{arena}(h_2))$ — a product of two binary variables (bilinear).

#### Linearization of Away→Away Legs

The bilinear AA terms are linearized by introducing auxiliary binary variables $w(t, d_1, d_2, h_1, h_2) \in \{0,1\}$:

$$w \leq x_{h_1,t,d_1}, \quad w \leq x_{h_2,t,d_2}, \quad w \geq x_{h_1,t,d_1} + x_{h_2,t,d_2} - 1$$

These three constraints enforce $w = x_{h_1,t,d_1} \cdot x_{h_2,t,d_2}$, the standard linearization of a binary product. The travel contribution of each AA leg is then $\mathrm{dist}(\mathrm{arena}(h_1), \mathrm{arena}(h_2)) \cdot w$, which is linear.

#### Model Size

- **Variables:** $3{,}840$ $x$-vars $+$ $\approx 13{,}275$ $w$-vars $+ 1$ $T^* \approx 17{,}116$ binary/continuous variables
- **Constraints:** 992 base $+$ per-team travel bounds $+$ $w$ linearization constraints

#### Results

Gurobi finds an optimal solution with a maximum team travel distance of **18,045 miles** (Golden State Warriors), compared to 20,400 miles in the original schedule — an **11.5% reduction**. The optimized schedule is saved to `optimized_schedule.csv`.

**Travel comparison: original vs. Method 1 optimized schedule**

| Team | Original (mi) | Optimized (mi) | Savings (mi) |
|---|---:|---:|---:|
| Atlanta Hawks | 7,308 | 7,308 | 0 |
| Boston Celtics | 15,756 | 17,676 | −1,919 |
| Brooklyn Nets | 12,385 | 14,380 | −1,995 |
| Chicago Bulls | 7,265 | 6,701 | +564 |
| Cleveland Cavaliers | 9,688 | 13,707 | −4,018 |
| Dallas Mavericks | 13,573 | 13,390 | +183 |
| Denver Nuggets | 9,922 | 10,406 | −484 |
| Golden State Warriors | 20,400 | 18,045 | +2,355 |
| Houston Rockets | 9,204 | 9,558 | −354 |
| Los Angeles Lakers | 16,166 | 17,712 | −1,546 |
| Miami Heat | 12,798 | 12,486 | +312 |
| Milwaukee Bucks | 8,458 | 9,060 | −603 |
| New York Knicks | 15,669 | 17,695 | −2,026 |
| Philadelphia 76ers | 13,977 | 14,867 | −890 |
| Phoenix Suns | 16,730 | 11,683 | +5,047 |
| Toronto Raptors | 10,789 | 11,386 | −597 |
| **MAX** | **20,400** | **18,045** | **+2,355** |

### Method 2 — Minimizing Maximum Deviation from League-Average Travel

#### Objective

As an alternative improvement criterion, we minimize the maximum deviation of any team's total travel distance from the league-average travel distance. Let $\mathrm{travel}(t)$ denote the total travel distance of team $t$, and let $\bar{D}$ denote the average travel distance across all teams. Introducing a scalar variable $\delta$, the objective is:

$$\min\;\delta$$

subject to:

$$\delta \geq \mathrm{travel}(t) - \bar{D} \quad \forall\, t \in T$$
$$\delta \geq \bar{D} - \mathrm{travel}(t) \quad \forall\, t \in T$$

where $\bar{D} = \dfrac{1}{|T|}\displaystyle\sum_{t \in T} \mathrm{travel}(t)$.

This is a min-max fairness formulation: $\delta$ measures the largest absolute deviation from the league-average travel, and minimizing it makes team travel as balanced as possible.

#### Model Size

- **Variables:** $3{,}840$ $x$-vars $+$ $\approx 13{,}275$ $w$-vars $+$ 16 travel-vars $+$ 1 $\delta \approx 17{,}132$ variables
- **Constraints:** 992 base $+$ 16 travel-definition $+$ 32 fairness-deviation $+$ $w$ linearization

#### Results

Gurobi finds an optimal solution for Method 2. Under this fairness-based objective, the optimized schedule achieves a league-average travel distance of **13,140 miles** and reduces the maximum deviation from the league average from 7,895 miles to **5,000 miles** — a **36.7% improvement in fairness**. The travel range is also reduced from 13,135 miles to 9,882 miles.

**Team-by-team travel comparison: original vs. Method 2 optimized schedule**

| Team | Original | Optimized | vs. Orig Avg | vs. Opt Avg |
|---|---:|---:|---:|---:|
| Golden State Warriors | 20,400 | 18,045 | +7,895 | +4,905 |
| Phoenix Suns | 16,730 | 15,532 | +4,225 | +2,392 |
| Los Angeles Lakers | 16,166 | 14,909 | +3,660 | +1,769 |
| Boston Celtics | 15,756 | 18,140 | +3,251 | +5,000 |
| New York Knicks | 15,669 | 17,681 | +3,163 | +4,542 |
| Philadelphia 76ers | 13,977 | 14,614 | +1,472 | +1,474 |
| Dallas Mavericks | 13,573 | 13,390 | +1,067 | +251 |
| Miami Heat | 12,798 | 12,986 | +292 | −154 |
| Brooklyn Nets | 12,385 | 13,881 | −120 | +741 |
| Toronto Raptors | 10,789 | 11,665 | −1,716 | −1,474 |
| Denver Nuggets | 9,922 | 9,376 | −2,583 | −3,764 |
| Cleveland Cavaliers | 9,688 | 13,727 | −2,817 | +587 |
| Houston Rockets | 9,204 | 11,274 | −3,302 | −1,866 |
| Milwaukee Bucks | 8,458 | 8,258 | −4,048 | −4,881 |
| Atlanta Hawks | 7,308 | 8,313 | −5,198 | −4,827 |
| Chicago Bulls | 7,265 | 8,443 | −5,241 | −4,697 |
| **League average** | **12,505** | **13,140** | | |

**Fairness summary: original vs. Method 2 optimized schedule**

| Metric | Original | Optimized | Improvement |
|---|---:|---:|---:|
| Max deviation from avg ($\delta$) | 7,895 | 5,000 | +2,894 |
| Range (max − min travel) | 13,135 | 9,882 | +3,254 |
| Max travel | 20,400 | 18,140 | +2,260 |
| Min travel | 7,265 | 8,258 | −994 |
| Total travel | 200,087 | 210,232 | −10,146 |

### Q4 Comparison Summary

Method 1 and Method 2 improve the schedule in different ways. Method 1 reduces the maximum team travel distance, while Method 2 improves fairness by reducing the gap between teams' travel burdens. In our results, Method 1 performs better in terms of worst-case travel, whereas Method 2 performs better in terms of balance across teams. This demonstrates a fundamental trade-off between **efficiency** and **fairness** in schedule design.
