# Production Line Optimization Using DES and Reinforcement Learning

This project presents a **Discrete-Event Simulation (DES)** and **Q-learning-based optimization model** for a manufacturing production line. The system was developed in Python using **SimPy** and evaluated under variable demand, machine failures, wear, repair operations, finite buffer capacity, and preventive maintenance decisions.

The main objective is to maximize the number of successfully completed products while reducing failures, waiting times, unnecessary maintenance, and machine downtime.

## Production Line Structure

The simulated production line consists of three machines:

* **M1 and M2:** Parallel machines used in the first production stage.
* **Buffer:** A finite-capacity intermediate storage area.
* **M3:** The final production station receiving products from the buffer.
* **Scrap exit:** Products that fail at any machine are removed from the production line.


## Dataset

The simulation parameters were developed by examining the **Manufacturing Production Data** dataset.

The dataset contains information such as:

* Job and machine identifiers
* Operation types
* Processing times
* Machine availability
* Energy consumption
* Scheduled and actual operation times
* Job status
* Material usage

During data analysis, missing values, data types, category distributions, and numerical variables were examined. Kolmogorov–Smirnov, Chi-square, Kruskal–Wallis, and one-way ANOVA tests were also used to evaluate selected distributions and relationships.

Dataset file used by the notebook:

```text
data/hybrid_manufacturing_categorical.csv
```

## Discrete-Event Simulation Model

The production line was modelled with SimPy using entities, resources, queues, events, and a simulation clock.

The final simulation includes:

* A one-year simulation horizon of **525,600 minutes**
* Parallel processing on M1 and M2
* Serial processing on M3
* A finite buffer with a capacity of five products
* Machine queues and waiting times
* Machine wear and failure probability
* Repair and preventive maintenance operations
* Failed-product removal
* Time-based machine state tracking
* Work-in-process calculation at the end of the simulation


A failed product is classified as scrap and removed from the production line. Repair restores the machine but does not recover the failed product.

## Decision Methods

Three production control methods were compared:

### 1. Normal-Speed Baseline

All machines operate continuously at normal speed. Products are distributed randomly between M1 and M2.

### 2. Adaptive-Speed Heuristic

Machine speed is increased when the corresponding queue exceeds a predefined threshold. This method represents a rule-based production control strategy.

### 3. Q-Learning Policy

The Q-learning model makes two types of decisions:

* Routing arriving products to M1 or M2
* Selecting the operating action for each machine



The station state includes:

```text
(machine wear, queue length, buffer occupancy, station ID)
```

The routing state includes the current queue lengths of M1 and M2.

During training, an epsilon-greedy strategy was used to balance exploration and exploitation. Ten independently trained candidate policies were evaluated using validation seeds that were not used during training. The policy with the highest average number of successfully completed products was selected for the final comparison.

## Performance Metrics

The following metrics were used to evaluate production performance:

* Successfully completed product count
* Scrap count and scrap rate
* Production yield
* Work-in-process at the simulation horizon
* Average flow time
* Queue waiting time
* Good-product throughput
* Productive machine utilization
* Repair downtime
* Preventive maintenance time
* Blocking time
* Idle time

The model also verifies the following consistency conditions:

```text
Generated products = Completed products + Scrapped products + WIP
```

```text
Processing + Repair + Maintenance + Blocking + Idle = Simulation horizon
```

## Results

The final evaluation was performed using **30 different random seeds** under the same one-year dynamic-demand scenario.

| Control method | Mean yield | Yield standard deviation | Mean completed products | Completed-product standard deviation |
| -------------- | ---------: | -----------------------: | ----------------------: | -----------------------------------: |
| Normal speed   |     75.71% |                     0.33 |                 4696.17 |                                20.52 |
| Adaptive speed |     75.36% |                     0.42 |                 4674.73 |                                26.40 |
| Q-learning     | **88.14%** |                 **0.45** |             **5466.50** |                            **27.67** |

The Q-learning policy increased the mean number of successfully completed products by approximately **16.4%** compared with the normal-speed baseline. It also improved mean production yield by approximately **12.43 percentage points**.

The results show that simply increasing machine speed does not guarantee better production performance. Machine wear, failure probability, maintenance time, queue conditions, and routing decisions must be considered together.

## Project Structure

```text
two-machine-DES-Reinforcement-Learning/
├── data/
│   └── hybrid_manufacturing_categorical.csv
├── DES_RL_Uretim_Hatti_Optimizasyonu_ANA.ipynb
└── README.md
```

## Installation

Install the required Python libraries:

```bash
pip install pandas numpy scipy matplotlib simpy gymnasium jupyter
```

## Running the Project

1. Download or clone the repository.
2. Keep the dataset inside the `data` directory.
3. Open the Jupyter Notebook:

```bash
jupyter notebook DES_RL_Uretim_Hatti_Optimizasyonu_ANA.ipynb
```

4. Run the notebook cells sequentially.

The notebook performs data analysis, builds the simulation model, trains the Q-learning policies, evaluates the control methods, and generates performance graphs.

## Limitations

* Hourly demand regimes are synthetic assumptions because hourly demand data were not available in the dataset.
* Preventive maintenance durations and machine wear parameters are modelling assumptions.
* The routing policy is implemented as a contextual decision mechanism.
* Candidate policy selection uses the mean completed-product count as its primary criterion.
* The model is a simulation-based research prototype and has not been validated using real-time production-line sensor data.

## Technologies

* Python
* Jupyter Notebook
* SimPy
* Pandas
* NumPy
* SciPy
* Matplotlib
* Q-learning
* Discrete-Event Simulation
