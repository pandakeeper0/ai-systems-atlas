---
type: concept
name: Pareto Optimality
aliases:
  - Pareto Efficiency
related:
  - Pareto Dominance
  - Pareto Frontier
---

# Pareto Optimality

**English:** Pareto Optimality / Pareto Efficiency  
**中文:** 帕累托最优 / 帕累托效率

## Core idea

A state or solution is **Pareto optimal** if there is no other feasible alternative that can make at least one objective or participant better off without making any other objective or participant worse off.

In multi-objective optimization, this means a Pareto-optimal solution cannot be improved in one objective without sacrificing at least one other objective.

## Related concepts

### Pareto Dominance

**Pareto Dominance（帕累托支配）** describes the relation between two candidate solutions.

A solution **A dominates B** if:

1. A is no worse than B on every objective; and
2. A is strictly better than B on at least one objective.

A solution that is dominated by another feasible solution is a **dominated solution**. A solution that is not dominated by any other feasible solution is a **non-dominated solution** and is Pareto optimal.

### Pareto Frontier

**Pareto Frontier / Pareto Front（帕累托前沿）** is the set of Pareto-optimal solutions.

It represents the efficient trade-off boundary among competing objectives. Moving from one point on the frontier to another generally improves some objective only by worsening another.

## Origin

The concept is named after **Vilfredo Pareto (1848–1923)**, an Italian economist, sociologist, and engineer. Pareto's work on economic efficiency and welfare provided the foundation for what later became known as Pareto efficiency or Pareto optimality.

The modern concepts of Pareto optimality and the Pareto frontier are widely used in welfare economics, game theory, engineering design, operations research, and multi-objective optimization.
