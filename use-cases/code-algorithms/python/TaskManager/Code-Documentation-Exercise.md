# Code Documentation Exercise

## ✅ Selected Code
**Python TaskManager - Task Priority Calculation Algorithm**

**Original Code:**
```python
from datetime import datetime
from models import TaskStatus, TaskPriority

def calculate_task_score(task):
    """Calculate a priority score for a task based on multiple factors."""
    # Base priority weights
    priority_weights = {
        TaskPriority.LOW: 1,
        TaskPriority.MEDIUM: 2,
        TaskPriority.HIGH: 4,
        TaskPriority.URGENT: 6
    }

    # Calculate base score from priority
    score = priority_weights.get(task.priority, 0) * 10

    # Add due date factor (higher score for tasks due sooner)
    if task.due_date:
        days_until_due = (task.due_date - datetime.now()).days
        if days_until_due < 0:  # Overdue tasks
            score += 35
        elif days_until_due == 0:  # Due today
            score += 20
        elif days_until_due <= 2:  # Due in next 2 days
            score += 15
        elif days_until_due <= 7:  # Due in next week
            score += 10

    # Reduce score for tasks that are completed or in review
    if task.status == TaskStatus.DONE:
        score -= 50
    elif task.status == TaskStatus.REVIEW:
        score -= 15

    # Boost score for tasks with certain tags
    if any(tag in ["blocker", "critical", "urgent"] for tag in task.tags):
        score += 8

    # Boost score for recently updated tasks
    days_since_update = (datetime.now() - task.updated_at).days
    if days_since_update < 1:
        score += 5

    return score
```

---

## 🔹 PROMPT 1 — Comprehensive Function Documentation

```python
def calculate_task_score(task):
    """
    Calculate a priority score for a task based on multiple factors.
    
    This function computes a numerical priority score for a task by evaluating
    several factors including base priority level, due date proximity, current
    status, special tags, and recent activity. Higher scores indicate higher
    priority tasks that should be addressed first.
    
    Args:
        task (Task): A Task object containing the following attributes:
            - priority (TaskPriority): The base priority level (LOW, MEDIUM, HIGH, URGENT)
            - due_date (datetime, optional): When the task is due
            - status (TaskStatus): Current task status (TODO, IN_PROGRESS, REVIEW, DONE)
            - tags (list[str]): List of string tags associated with the task
            - updated_at (datetime): When the task was last modified
    
    Returns:
        int: Priority score where higher values indicate higher priority.
             Typical range: 0-100+ depending on factors.
    
    Raises:
        AttributeError: If the task object is missing required attributes.
        TypeError: If task.due_date or task.updated_at are not datetime objects.
    
    Example:
        >>> from datetime import datetime, timedelta
        >>> from models import Task, TaskPriority, TaskStatus
        >>> 
        >>> # Create a high priority task due tomorrow
        >>> task = Task(
        ...     title="Fix critical bug",
        ...     priority=TaskPriority.HIGH,
        ...     due_date=datetime.now() + timedelta(days=1),
        ...     status=TaskStatus.TODO,
        ...     tags=["critical", "bug"],
        ...     updated_at=datetime.now()
        ... )
        >>> score = calculate_task_score(task)
        >>> print(f"Priority score: {score}")
        Priority score: 63  # 40 (HIGH) + 15 (due soon) + 8 (critical tag)
    
    Notes:
        - Overdue tasks receive the highest due date bonus (+35 points)
        - Completed tasks (DONE) receive a large penalty (-50 points)
        - Tasks with "blocker", "critical", or "urgent" tags get priority boost
        - Recently updated tasks (< 1 day) get a small activity bonus
        - Base priority weights: LOW=1, MEDIUM=2, HIGH=4, URGENT=6 (×10)
    
    Edge Cases:
        - Tasks without due_date are not penalized or boosted for timing
        - Tasks with unknown priority default to 0 base score
        - Multiple special tags still only add +8 points total
        - Negative scores are possible for completed overdue tasks
    """
```

---

## 🔹 PROMPT 2 — Intent and Logic Explanation

### High-Level Intent
This code implements a **task prioritization algorithm** for a task management system. The goal is to automatically rank tasks by importance so users can focus on what matters most. It combines multiple business factors into a single numerical score.

### Step-by-Step Logic Breakdown

1. **Base Priority Scoring (Lines 6-12)**
   ```python
   # Establishes foundation score based on user-assigned priority
   # Uses exponential-like scaling: LOW=10, MEDIUM=20, HIGH=40, URGENT=60
   score = priority_weights.get(task.priority, 0) * 10
   ```

2. **Due Date Urgency (Lines 14-23)**
   ```python
   # Time-sensitive scoring with diminishing returns
   # Overdue (35pts) > Today (20pts) > Soon (15pts) > This week (10pts)
   days_until_due = (task.due_date - datetime.now()).days
   ```

3. **Status-Based Adjustments (Lines 25-29)**
   ```python
   # Reduces priority for tasks already completed or under review
   # Prevents completed work from cluttering priority lists
   ```

4. **Tag-Based Boosting (Lines 31-33)**
   ```python
   # Special keywords trigger priority increase
   # Handles emergency situations: "blocker", "critical", "urgent"
   ```

5. **Activity Recency Bonus (Lines 35-38)**
   ```python
   # Recently touched tasks get slight boost
   # Encourages momentum on active work
   ```

### Key Assumptions
- **DateTime objects**: All date fields are properly formatted datetime instances
- **Enum consistency**: TaskStatus and TaskPriority enums are properly defined
- **Tag format**: Tags are stored as lowercase strings in a list
- **Business rules**: The scoring weights reflect organizational priorities

### Suggested Inline Comments
```python
def calculate_task_score(task):
    # Initialize scoring weights (exponential scale for priority impact)
    priority_weights = {
        TaskPriority.LOW: 1,      # 10 points base
        TaskPriority.MEDIUM: 2,   # 20 points base  
        TaskPriority.HIGH: 4,     # 40 points base
        TaskPriority.URGENT: 6    # 60 points base
    }

    # Start with base priority score (primary factor)
    score = priority_weights.get(task.priority, 0) * 10

    # Apply time pressure multiplier (secondary factor)
    if task.due_date:
        days_until_due = (task.due_date - datetime.now()).days
        # Overdue tasks get maximum urgency boost
        if days_until_due < 0:
            score += 35  # Critical: past due
        elif days_until_due == 0:
            score += 20  # High: due today
        elif days_until_due <= 2:
            score += 15  # Medium: due very soon
        elif days_until_due <= 7:
            score += 10  # Low: due this week

    # Adjust for workflow status (avoid prioritizing finished work)
    if task.status == TaskStatus.DONE:
        score -= 50  # Large penalty for completed tasks
    elif task.status == TaskStatus.REVIEW:
        score -= 15  # Moderate penalty for tasks awaiting review

    # Emergency tag detection (business-critical override)
    if any(tag in ["blocker", "critical", "urgent"] for tag in task.tags):
        score += 8  # Fixed boost regardless of tag count

    # Recent activity bonus (encourage momentum)
    days_since_update = (datetime.now() - task.updated_at).days
    if days_since_update < 1:
        score += 5  # Small boost for active tasks

    return score
```

### Potential Improvements
1. **Configurable weights**: Make scoring weights adjustable per team/project
2. **Dependency tracking**: Consider task dependencies in scoring
3. **User workload**: Factor in assignee's current task load
4. **Historical data**: Use completion time estimates for better due date scoring
5. **Validation**: Add input validation for edge cases (null dates, invalid enums)

---

## 🔹 Final Combined Documentation

```python
def calculate_task_score(task):
    """
    Calculate a comprehensive priority score for task ranking and scheduling.
    
    This algorithm combines multiple business factors to generate a numerical
    priority score, enabling automatic task ranking in productivity systems.
    Higher scores indicate tasks that should be addressed first.
    
    Scoring Factors:
    - Base Priority: User-assigned importance level (10-60 points)
    - Due Date Urgency: Time pressure based on deadline proximity (0-35 points)
    - Status Adjustment: Reduces priority for completed/reviewed tasks (-50 to -15)
    - Critical Tags: Emergency keywords boost priority (+8 points)
    - Recent Activity: Recently updated tasks get momentum bonus (+5 points)
    
    Args:
        task (Task): Task object with required attributes:
            - priority (TaskPriority): LOW, MEDIUM, HIGH, or URGENT
            - due_date (datetime, optional): Task deadline
            - status (TaskStatus): Current workflow state
            - tags (list[str]): Associated keywords/labels
            - updated_at (datetime): Last modification timestamp
    
    Returns:
        int: Priority score (typically 0-100+, higher = more important)
    
    Example:
        >>> urgent_task = Task(
        ...     priority=TaskPriority.URGENT,
        ...     due_date=datetime.now() - timedelta(days=1),  # Overdue
        ...     status=TaskStatus.TODO,
        ...     tags=["critical", "bug"],
        ...     updated_at=datetime.now()
        ... )
        >>> score = calculate_task_score(urgent_task)
        >>> print(score)  # 60 + 35 + 8 + 5 = 108
    
    Notes:
        - Overdue tasks receive maximum urgency bonus
        - Completed tasks are heavily deprioritized to avoid clutter
        - Multiple critical tags don't stack (fixed +8 bonus)
        - Tasks without due dates aren't penalized for timing
    """
    # Initialize exponential priority weights
    priority_weights = {
        TaskPriority.LOW: 1,      # 10 points base
        TaskPriority.MEDIUM: 2,   # 20 points base
        TaskPriority.HIGH: 4,     # 40 points base
        TaskPriority.URGENT: 6    # 60 points base
    }

    # Calculate base score from user-assigned priority
    score = priority_weights.get(task.priority, 0) * 10

    # Apply time-based urgency multiplier
    if task.due_date:
        days_until_due = (task.due_date - datetime.now()).days
        if days_until_due < 0:      # Overdue: maximum urgency
            score += 35
        elif days_until_due == 0:   # Due today: high urgency
            score += 20
        elif days_until_due <= 2:   # Due soon: medium urgency
            score += 15
        elif days_until_due <= 7:   # Due this week: low urgency
            score += 10

    # Adjust for workflow status (deprioritize finished work)
    if task.status == TaskStatus.DONE:
        score -= 50  # Large penalty for completed tasks
    elif task.status == TaskStatus.REVIEW:
        score -= 15  # Moderate penalty for tasks in review

    # Emergency tag detection (business-critical override)
    if any(tag in ["blocker", "critical", "urgent"] for tag in task.tags):
        score += 8

    # Recent activity bonus (encourage momentum on active work)
    days_since_update = (datetime.now() - task.updated_at).days
    if days_since_update < 1:
        score += 5

    return score
```

---

## 🔹 Reflection & Learning

### Most Challenging Parts for AI
The AI struggled most with understanding the **business context** behind the scoring weights. Without domain knowledge, it was difficult to explain why overdue tasks get +35 points vs +20 for today's tasks, or why the priority multiplier uses exponential scaling.

### Additional Information Needed
- **Business requirements**: What organizational priorities drove these specific weights?
- **Usage context**: How is this score used in the broader application?
- **Performance requirements**: Are there constraints on calculation speed?
- **Edge case handling**: What happens with malformed or missing data?

### Application in Projects
This approach would be valuable for:
1. **Legacy code documentation**: Quickly document undocumented algorithms
2. **Code review preparation**: Generate comprehensive docs before reviews  
3. **Onboarding new developers**: Create detailed explanations of complex logic
4. **API documentation**: Generate parameter and return value descriptions
5. **Refactoring planning**: Understand existing logic before making changes