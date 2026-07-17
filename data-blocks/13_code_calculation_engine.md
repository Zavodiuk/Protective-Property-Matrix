# Block 13: Architectural Calculation Engine (Python)

## Purpose
Implementation of the calculation engine for architectural metrics: costs, risks, viability dynamics, and conflict probability analysis.

## Core Components
- **Cost**: Aggregation of money, time, risk, and loss into a single metric.
- **TimeSeries**: Analytical tools for velocity (V) and acceleration (A) of architectural development.
- **Risk**: Monetary evaluation of potential threats.
- **Architectural Overlap (Ka)**: Jaccard index for calculating the probability of conflict between architectures.
- **Gain (G)**: Strategic decision-making formula (Gc > Cf).

## Implementation
```python
from dataclasses import dataclass, field
from typing import List
import bisect

# 1. Стоимость (C)
@dataclass
class Cost:
    """C = C_money + C_time + C_risk + C_loss"""
    money: float = 0.0
    time_cost: float = 0.0
    risk_cost: float = 0.0
    loss_cost: float = 0.0

    @property
    def total(self) -> float:
        return self.money + self.time_cost + self.risk_cost + self.loss_cost

# 2-4. Время (T), Темп (V), Ускорение (A)
@dataclass
class TimeSeries:
    t: List[float] = field(default_factory=list)
    x: List[float] = field(default_factory=list)

    def velocity(self) -> List[float]:
        v = []
        for i in range(1, len(self.t)):
            dt = self.t[i] - self.t[i - 1]
            dx = self.x[i] - self.x[i - 1]
            v.append(dx / dt if dt != 0 else 0.0)
        return v

    def acceleration(self) -> List[float]:
        v = self.velocity()
        a = []
        for i in range(1, len(v)):
            dt = self.t[i + 1] - self.t[i]
            dv = v[i] - v[i - 1]
            a.append(dv / dt if dt != 0 else 0.0)
        return a

# 7. Коэффициент архитектурного пересечения (Ka)
def architectural_overlap(features_a: set, features_b: set) -> float:
    if not features_a and not features_b: return 0.0
    intersection = features_a & features_b
    union = features_a | features_b
    return len(intersection) / len(union)

# 8. Выгода (G)
@dataclass
class Gain:
    revenue: float
    cost: Cost
    @property
    def value(self) -> float:
        return self.revenue - self.cost.total
