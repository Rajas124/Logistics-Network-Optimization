# Operation Wrath of Muggles: Logistics Optimization

![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![Optimization](https://img.shields.io/badge/Optimization-GAMSPy-orange.svg)
![Solver](https://img.shields.io/badge/Solver-CPLEX-red.svg)
![License](https://img.shields.io/badge/License-MIT-green.svg)

## Project Overview
This repository contains an advanced mixed-integer programming (MIP) model designed to optimize a multi-commodity logistical network. The primary objective is to strategically manage and streamline the transport of critical commodities—including fuel, troops, ammunition, and weapons—from primary Supply Locations (SL) through intermediate Forward Stations (FS) to terminal Delivery Locations (DL).

Built with **GAMSPy** (the Python API for GAMS) and powered by the high-performance **CPLEX** solver, these models evaluate cost minimization, time-to-delivery acceleration, multi-scenario stochastic demand management, and multi-objective Pareto trade-offs.

## Strategic & Operational Impact
Military  logistics require robust models that balance financial expenditure against operational speed. In critical operations, time and cost are inherently competing objectives. This project models five crucial operational scenarios:

*   **Scenario A (Flow Maximization):** Maximizes commodity delivery (e.g., critical ammunition shortage management) to terminal points within a strict 15-hour threshold.
*   **Scenario B (Time Minimization):** Minimizes total throughput time required to deliver high-priority commodities to the frontlines.
*   **Scenario C (Pareto Trade-off Analysis):** Maps the mathematical trade-off frontier between total supply chain cost and operational time under varying fleet sizes.
*   **Scenario D (Stochastic Demand):** Evaluates network cost efficiencies against expected demand across five distinct high-uncertainty scenarios.
*   **Scenario E (Robust Optimization):** Minimizes expenditures under absolute worst-case maximum demand thresholds.

---

## Network Architecture & Geography
The optimization model calculates geographic distances over the Earth's surface using the **Haversine formula**. The logistical nodes represent actual and hypothetical strategic locations:

| Node Type | Node Code | Location / Strategic Role | Coordinates (Lat, Lon) |
| :--- | :--- | :--- | :--- |
| **Supply Location** | SL1 | Hawthorne Ammunition Base, NV (Ammo/Weapons) | `38.5657, -118.7198` |
| **Supply Location** | SL2 | New Orleans Troop Reserve, LA (Troops) | `29.9509, -90.0758` |
| **Supply Location** | SL3 | Houston Fuel Reserve, TX (Fuel) | `29.7601, -95.3701` |
| **Forward Station** | FS1 | Seattle, WA | `47.6061, -122.3328` |
| **Forward Station** | FS2 | San Diego, CA | `32.7157, -117.1611` |
| **Forward Station** | FS3 | Miami, FL | `25.7752, -80.2086` |
| **Forward Station** | FS4 | New York, NY | `40.7168, -74.0060` |
| **Delivery Location** | DL1 | Mt. Orizaba (Island Impact Area) | `33.3752, -118.4266` |
| **Delivery Location** | DL2 | Silver Peak (Island Impact Area) | `33.4599, -118.5686` |
| **Delivery Location** | DL3 | Long Point (Island Impact Area) | `33.4061, -118.3680` |
| **Delivery Location** | DL4 | Avalon (Island Impact Area) | `33.3398, -118.3290` |

### Fleet Capabilities (Connectors)
The model restricts routing based on realistic vehicle dynamics, tracking variable transport costs, fixed maintenance costs, volumetric caps, and weight limits (tons):
*   🚢 **Boats:** Restricted to sea routes (FS $\rightarrow$ DL if water boundary exists).
*   ✈️ **Planes:** High speed, high cost; mathematically banned from carrying volatile fuel.
*   🚂 **Trains:** Restricted to land networks between domestic Supply Locations and Forward Stations (SL $\rightarrow$ FS).
*   🚛 **Trailers:** Standard overland road transportation.

---

## Mathematical Formulation


### Objective Functions

**1. Total Cost Minimization ($J_1$):**

$$\min \sum_{c, k, (i,j) \in A} \beta_{c,k,i,j} \cdot w_c \cdot \mathrm{cost}_k \cdot \mathrm{dist}_{i,j} + \sum_{k, i, j} \mathrm{maintenance}_k \cdot \alpha_{k,i,j}$$

> Where $\beta$ represents the quantity of commodity $c$ moved by connector $k$ on arc $(i,j)$, $w_c$ is the commodity unit weight, $\alpha$ is a binary decision variable indicating vehicle use, and $A$ represents valid arcs.

**2. Total Time Minimization ($J_2$):**

$$\min t_1 + t_2$$

> Where $t_1$ is constrained to be $\ge$ the travel duration of the slowest active connector between any SL and FS, and $t_2$ handles the slowest active connector between any FS and DL.

### Primary Constraints
*   **Flow Balance Constraints:** Ensures strict incoming-outgoing conservation at intermediate Forward Stations.
*   **Demand Fulfillment:** Guarantees that the sum of arrivals at each DL meets or exceeds command-specified targets.
*   **Capacity Bounds:** Enforces maximum weight thresholds across mixed cargo combinations loaded onto any individual vehicle.
*   **Geographical Hard Links:** Eliminates land vehicles on open ocean legs and watercraft on cross-country terrain.

---

## Key Results & Insights
*   **Baseline Minimum Cost:** Fulfills standard structural demand at an optimal cost of **$872,898.94**, leveraging cost-efficient ground/rail networks where available.
*   ⏱️ **Baseline Minimum Time:** Urgent throughput reduces delivery time to **13.05 hours**, though the aggressive reliance on aviation networks spikes costs to **$9,103,212.00**.
*   **Pareto Frontier Sensitivity:** Plotting cost vs. time reveals a distinct knee-bend behavior. Restricting total vehicle availability shifts the frontier, providing crucial data for selecting fleet scales that avoid cost inflation while meeting time-critical thresholds.

---

## Installation & Usage

### Prerequisites
*   Python 3.8+
*   GAMS License / Solver Access (Requires a local GAMS installation with CPLEX or an academic license).

### Quickstart
1. Clone this repository:
   ```bash
   git clone [https://github.com/yourusername/logistics-network-optimization.git](https://github.com/yourusername/logistics-network-optimization.git)
   cd logistics-network-optimization
