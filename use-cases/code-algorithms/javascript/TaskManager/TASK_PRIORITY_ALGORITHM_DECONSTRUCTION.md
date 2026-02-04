## Code Explanation: Task Priority Sorting Algorithm

This section explains how the Task Priority Sorting Algorithm is implemented in
`task_priority.js` and how each part of the code contributes to the final task ranking.

---

### 1. Overview of the Algorithm Structure

The algorithm is composed of three main responsibilities:

1. **Calculating a score for each task** based on multiple attributes
2. **Sorting tasks** using the calculated scores
3. **Selecting the highest-priority tasks** from the sorted list

The core logic resides in the function responsible for calculating the task score.

---

### 2. Priority Weight Calculation

The algorithm begins by defining a mapping between task priority levels and numeric weights.
These weights establish a baseline importance for each task.

Higher priority values result in a higher base score, ensuring that urgent tasks start with
an advantage before other factors are considered.

This design ensures that priority remains the dominant factor in task ordering.

---

### 3. Due Date Evaluation

If a task has a due date, the algorithm calculates how many days remain until that date.

Based on this value, additional points are added:
- Overdue tasks receive the highest bonus
- Tasks due today or within a few days receive moderate bonuses
- Tasks due within a week receive a smaller bonus

This step introduces time sensitivity and ensures that urgent deadlines influence task order.

---

### 4. Status-Based Adjustments

The algorithm then checks the task’s status.

- Tasks marked as **DONE** receive a large penalty
- Tasks in **REVIEW** receive a smaller penalty

These deductions prevent completed or nearly completed tasks from appearing at the top of
the priority list.

---

### 5. Tag-Based Priority Boosts

The algorithm scans task tags for specific keywords such as *urgent*, *critical*, or *blocker*.

If any of these tags are present, the task receives an additional score boost.  
This allows contextual information to influence prioritization beyond formal priority levels.

---

### 6. Recency Boost Logic

To account for ongoing work, the algorithm checks when the task was last updated.

Tasks updated within the last day receive a small score increase, helping maintain focus on
active or recently modified tasks.

---

### 7. Final Score Aggregation

All bonuses and penalties are combined into a single numeric score.

This final score represents the overall importance of the task and is used as the basis
for comparison with other tasks.

---

### 8. Sorting Mechanism

After all task scores are calculated, tasks are sorted in descending order based on their
final scores.

This ensures that tasks with the highest calculated importance appear first in the list.

---

### 9. Task Selection

Once sorted, the algorithm can return:
- The full ordered task list, or
- A limited subset containing only the top-priority tasks

This makes the algorithm flexible for different use cases, such as dashboards or summaries.

---

### Summary

The Task Priority Sorting Algorithm translates multiple real-world decision factors into a
single numeric score. By combining priority, urgency, status, context, and recency, the
algorithm produces a ranked task list that closely resembles human decision-making while
remaining deterministic and easy to extend.


## Reflection on AI Explanation

### How did AI explanation change your understanding?
The AI explanation helped clarify that the Task Priority Sorting Algorithm is a weighted scoring system rather than a simple priority sort. Instead of relying on a single attribute, the algorithm evaluates multiple task properties and combines them into a single numerical score that represents overall importance.

Each task is processed independently and assigned points based on:

Its declared priority level

How close or overdue its due date is

Its current status (active, review, or completed)

The presence of contextual tags such as urgent or blocker

How recently the task was updated

Once all scores are calculated, tasks are sorted in descending order so that the most important tasks appear first.

This explanation reframed the algorithm as a decision-making model, similar to how a human would prioritize work, rather than a collection of unrelated condition checks.

           ┌─────────────┐
           │   Task      │
           └──────┬──────┘
                  │
        ┌─────────▼─────────┐
        │ Base Priority     │
        │ (LOW → URGENT)    │
        └─────────┬─────────┘
                  │
        ┌─────────▼─────────┐
        │ Due Date Urgency  │
        │ (Overdue, Today)  │
        └─────────┬─────────┘
                  │
        ┌─────────▼─────────┐
        │ Status Adjustment │
        │ (DONE / REVIEW)   │
        └─────────┬─────────┘
                  │
        ┌─────────▼─────────┐
        │ Tag-Based Boost   │
        │ (urgent, blocker)│
        └─────────┬─────────┘
                  │
        ┌─────────▼─────────┐
        │ Recency Boost     │
        │ (Recently updated)│
        └─────────┬─────────┘
                  │
           ┌──────▼──────┐
           │ Final Score │
           └──────┬──────┘
                  │
        ┌─────────▼─────────┐
        │ Sort Tasks (↓)    │
        └──────────────────┘


## Key Insights

1. **Scoring Systems Are Flexible but Subjective**  
   The effectiveness of the algorithm depends on the chosen weights. Small changes to scoring values can significantly alter task rankings, which means the system must be tuned carefully.

2. **Priority Alone Is Not Sufficient**  
   The algorithm shows that priority levels by themselves are not enough. Due dates, task status, and contextual tags all contribute to a more accurate representation of importance.

3. **Penalties Are as Important as Rewards**  
   Reducing scores for completed or review tasks ensures that finished or less urgent work does not dominate the top of the task list.

4. **Human Decision-Making Can Be Modeled Algorithmically**  
   The algorithm mirrors how humans prioritize work by combining urgency, importance, and context into a single decision-making process.

5. **Hardcoded Values Reduce Flexibility**  
   Using fixed numeric weights makes the algorithm harder to adapt. Making these values configurable would improve reusability and maintainability.

---

## Learning Points

- I learned how multiple task attributes can be combined into a single weighted score.
- I improved my ability to read and reason about non-trivial algorithms.
- AI explanations helped connect low-level conditional logic to high-level intent.
- Visual diagrams made the flow of the algorithm easier to understand.
- I learned how to document and explain an algorithm without modifying its implementation.
