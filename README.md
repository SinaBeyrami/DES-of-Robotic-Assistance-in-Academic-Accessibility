# DES of Robotic Assistance in Academic Accessibility

Discrete-event simulation (DES) exploring how an assistant robot impacts accessibility and flow in an academic building.
Phase 1 is a literature presentation; Phase 2 is a full Python simulation comparing a **Standard** (human-only) mode vs a **Robotic** mode with a priority queue for disabled users and stochastic service/error dynamics.

## Repository structure

```
DES-of-Robotic-Assistance-in-Academic-Accessibility/
├─ Phase 1 Presentation/
│  ├─ Enhancing_Accessibility_in_Academic_Buildings_A_Discrete_Event_Simulation.pdf
│  └─ Lecture_Beyrami_400105433.pptx
└─ Phase 2 Implementation/
   └─ CS_proj_P2_Beyrami_400105433.ipynb
```

## Phase 1 — Literature talk

Slide deck + paper summary:

* **Slides:** `Phase 1 Presentation/Lecture_Beyrami_400105433.pptx`
* **Paper discussed:** *Enhancing Accessibility in Academic Buildings: A Discrete Event Simulation Approach for Robotic Assistance* (IEEE Access, 2024). Key takeaway: robot assistance can cut travel time across student groups (e.g., \~35–52% in one building; aggregate ≈43% for students with disabilities and ≈27% for non-disabled; similar gains in a second building).&#x20;

## Phase 2 — Simulation (Python)

Notebook: `Phase 2 Implementation/CS_proj_P2_Beyrami_400105433.ipynb`

### What it models

* **Arrivals:** Poisson (λ = `LAMBD`)
* **Heterogeneity:** disability status (`X` probability), two destinations with `P1`, different base service times by disability×destination
* **Two modes:**

  * **STANDARD\_MODE**: two human servers (FIFO)
  * **ROBOTIC\_MODE**: one human + one **robot** server

    * Robot prioritizes disabled patients (heap-based priority queue)
    * Robot speeds up service by factor `ALPHA` but may require repeated attempts with probability `P_ERROR`
* **Metrics:** max queue length, avg wait/system time (global & by group), per-server queue length and waiting time, utilizations, arrivals by group

### Key parameters (defaults in notebook)

```python
LAMBD = 0.5      # arrival rate
P1 = 0.6         # destination-1 probability
P_ERROR = 0.15   # robot retry probability
ALPHA = 0.5      # robot speedup multiplier (<1 is faster)
SIM_TIME = 20000 # total simulated time units
X = 0.2          # probability of a disabled arrival
```

### Quick start

1. Open the notebook in Jupyter/Colab.
2. Run all cells to produce:

   * One “sample run” for **Standard** and **Robotic** modes (fixed seed for reproducibility)
   * Sensitivity analyses with Matplotlib plots:

     * `P_ERROR` → Avg System Time
     * Simulation Time `T` → Max Queue Length
     * Disabled share `X` → Avg System Time
     * Speedup `ALPHA` → Avg System Time
     * `P1` → Avg Wait Time
     * 3D surface & scatter: `ALPHA`×`P_ERROR` → Avg System Time (robotic mode)

> Dependencies: `numpy`, `matplotlib`. (Notebook uses only standard Python libs otherwise.)

### How the robot changes the system

* **Priority discipline:** disabled users get priority on the robot server.
* **Service dynamics:** robot reduces base service time by `ALPHA` but may loop with probability `P_ERROR` to model guidance corrections.
* **Routing:** in robotic mode, disabled users are directed to the robot when available; others use the human server unless the robot queue is better.

Absolutely—here’s a clean, drop-in README section you can paste into your repo. It embeds the six figures and adds short, decision-oriented interpretations.

> Tip: put the images into `Phase 2 Implementation/figures/` with the filenames used below (or update the paths if you choose different names).

---

# Results & Interpretation (DES of Robotic Assistance)

This phase compares two service policies for moving students inside an academic building:

* **Standard mode:** two human operators.
* **Robotic mode:** one human + one robot (robot prioritizes disabled users and may need reattempts when an error occurs).

Experiments are run with the simulator in `Phase 2 Implementation/CS_proj_P2_Beyrami_400105433.ipynb`.

### 1) Error probability vs. average system time

![Effect of P\_ERROR on Avg System Time](Phase%202%20Implementation/figures/p_error_avg_system_time.png)

**What it shows**

* Standard mode is almost flat (\~3.7–3.9k seconds).
* Robotic mode rises quickly as **P\_ERROR** increases; it’s **better than standard** only when **P\_ERROR ≲ 0.35–0.40**.

**Takeaway:** A robot helps only if the end-to-end failure/reattempt rate is low. Keep the robot’s error probability under \~35% (via better sensing/training/procedures).

---

### 2) Simulation time vs. max queue length

![Effect of Simulation Time on Max Queue Length](Phase%202%20Implementation/figures/simtime_max_queue_length.png)

**What it shows**

* Both queues grow with time (as expected).
* Robotic mode consistently has **smaller peak queues** (e.g., at 25k time units \~3.1k vs. \~3.95k for standard).

**Takeaway:** With stable demand, introducing the robot reduces worst-case congestion at entrances/elevators.

---

### 3) Share of disabled users (X) vs. average system time

![Effect of X on Avg System Time](Phase%202%20Implementation/figures/x_disabled_avg_system_time.png)

**What it shows**

* Standard mode degrades steeply as disabled share grows.
* Robotic mode increases more gently and stays **much lower** across the range (gap widens with higher X).

**Takeaway:** When a campus/building serves a **larger proportion of disabled users**, a robotic assistant yields **disproportionately higher benefits**.

---

### 4) Robot speedup factor (ALPHA) vs. average system time

![Effect of ALPHA on Avg System Time](Phase%202%20Implementation/figures/alpha_avg_system_time.png)

> (In code: **robot\_time = ALPHA × human\_time**, so **smaller ALPHA = faster robot**.)

**What it shows**

* Robotic mode is **better than standard** only when **ALPHA ≲ 0.55–0.60** (≈ **≥40–45% speedup** vs. human).
* If the robot is not fast enough (ALPHA → 1.0), the advantage disappears.

**Takeaway:** Target at least \~**40–50% time reduction** per task for the robot to pay off under these loads.

---

### 5) Destination mix (P1) vs. average wait time

![Effect of P1 on Wait Time](Phase%202%20Implementation/figures/p1_wait_time.png)

**What it shows**

* As probability of Destination 1 increases (generally shorter service), wait times drop in both modes.
* Robotic mode maintains **lower waits** across all mixes and sees larger gains as routing favors shorter jobs.

**Takeaway:** If demand can be steered toward shorter paths (e.g., better signage/dispatching), the robot amplifies the win.

---

### 6) Joint effect: ALPHA × P\_ERROR on robotic average system time

![3D: Avg System Time vs. ALPHA & P\_ERROR (Robot)](Phase%202%20Implementation/figures/surface_alpha_p_error_avg_system_time.png)

**What it shows**

* Performance worsens as **either** ALPHA (slower robot) **or** P\_ERROR (more retries) increases.
* **Safe operating region** for clear improvement: roughly **ALPHA ≤ 0.7** **and** **P\_ERROR ≤ 0.3**.

**Takeaway:** To reliably beat the human-only baseline, keep the robot **both fast and reliable**; small degradations in either dimension compound.

---

## Quick summary (rules of thumb)

* Keep **robot failure probability ≤ \~30–35%**.
* Aim for **≥40–50% service-time speedup** vs. human (ALPHA ≤ \~0.6).
* Benefits **grow** with a higher fraction of disabled users.
* Robotic mode **reduces peak queue lengths** and **average waits** under realistic mixes.


---


## Reproduce / extend

* Tweak `LAMBD`, `X`, `ALPHA`, `P_ERROR`, and `P1` at the top of the notebook.
* Increase `SIM_TIME` for steadier averages; seed is set to `12345` for comparability.
* Adapt `service_time_human` to fit other buildings or service policies.

## Acknowledgements & references

* Phase-1 paper discussed in this project: *Enhancing Accessibility in Academic Buildings: A Discrete Event Simulation Approach for Robotic Assistance* (IEEE Access, 2024).&#x20;

---

If you want, I can also add a tiny `requirements.txt` (`numpy`, `matplotlib`) and a preview image of one of the plots in the README.
