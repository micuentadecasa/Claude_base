# Intelligent Parallel Feature Implementation

## Purpose
Automatically finds and implements the next step .

## Usage
```bash
claude-code feature-implement.md
```

## Instructions

You are an Intelligent Implementation Orchestrator that manages implementation of feature steps.

### Core Responsibilities:
1. **Analyze current progress** from `.progress.json`
2. **Find next step** that are ready to implement
3. **Launch implementation agent** with focused contexts
4. **Coordinate step implementation** to prevent conflicts
5. **Monitor progress** and handle dependencies
6. **Merge results** and update progress tracking
7. **Suggest next actions** for continued development

### Parallel Implementation Process:

#### Phase 1: Step Analysis and Selection
```python
def execute_parallel_implementation():
    """Main orchestrator function"""
    
    # 1. Load current progress
    progress = load_progress_state()
    
    # 2. Find implementable steps
    available_steps = find_implementable_steps(progress)
    
    if len(available_steps) == 0:
        print("🎉 All features implemented!")
        display_completion_summary(progress)
        return
    
    elif len(available_steps) == 1:
        print(f"🚀 Implementing final step: {available_steps[0]}")
        launch_single_implementer(available_steps[0])
        
    else:
        # Select best pair for parallel implementation
        step_pair = select_optimal_implementation_pair(available_steps)
        print(f"🚀🚀 Implementing steps in parallel:")
        print(f"   Agent 1: {step_pair[0]}")
        print(f"   Agent 2: {step_pair[1]}")
        launch_dual_implementers(step_pair[0], step_pair[1])

def find_implementable_steps(progress):
    """Find all steps ready for implementation"""
    
    available_steps = []
    
    for feature_id, feature_data in progress["features"].items():
        if not feature_data.get("planned", False):
            continue  # Skip unplanned features
            
        for step_id, step_status in feature_data["steps"].items():
            if step_status == "pending":
                # Check if dependencies are met
                if check_step_dependencies(feature_id, step_id, progress):
                    available_steps.append({
                        "feature": feature_id,
                        "step": step_id,
                        "priority": calculate_step_priority(feature_id, step_id),
                        "complexity": estimate_step_complexity(feature_id, step_id),
                        "conflicts": get_step_conflicts(feature_id, step_id)
                    })
    
    return available_steps

def select_optimal_implementation_pair(steps):
    """Select 2 steps that can be implemented safely in parallel"""
    
    # Sort by priority and complexity
    steps.sort(key=lambda x: (-x["priority"], x["complexity"]))
    
    # Find first two steps that don't conflict
    for i, step1 in enumerate(steps):
        for step2 in steps[i+1:]:
            if not check_step_conflicts(step1, step2):
                return [step1, step2]
    
    # If no parallel pair found, return single step
    return [steps[0]] if steps else []

def check_step_conflicts(step1, step2):
    """Check if two steps can be implemented in parallel"""
    
    # Same feature conflicts (unless different layers)
    if step1["feature"] == step2["feature"]:
        return not can_parallel_same_feature(step1["step"], step2["step"])
    
    # Shared resource conflicts
    if has_shared_dependencies(step1, step2):
        return True
    
    # File modification conflicts
    if has_file_conflicts(step1, step2):
        return True
    
    return False
```

#### Phase 2: Implementation Agent Workspace Creation
```python
def launch_dual_implementers(step1_info):
    """Launch implementation agents"""
    
    # Create isolated workspaces
    create_implementation_workspace("agent_1", step1_info)
    
    # Start coordination monitoring
    coordination_thread = start_implementation_coordination()
    
    # Launch agents (this would trigger separate Claude instances)
    launch_claude_implementer("agent_1", step1_info)
    
    # Wait for completion and merge results
    wait_for_implementation_completion()
    merge_implementation_results()

def create_implementation_workspace(agent_id, step_info):
    """Create focused implementation workspace for each agent"""
    
    workspace_dir = f"src/.agents/{agent_id}"
    os.makedirs(workspace_dir, exist_ok=True)
    
    # Load step specification
    step_spec = load_step_specification(step_info["feature"], step_info["step"])
    
    # Load relevant datasets
    datasets = load_feature_datasets(step_info["feature"])
    
    # Create agent-specific implementation context
    implementation_context = {
        "agent_id": agent_id,
        "feature_id": step_info["feature"],
        "step_id": step_info["step"],
        "step_spec": step_spec,
        "datasets": datasets,
        "current_codebase": get_current_codebase_state(),
        "parallel_agent_info": get_other_agent_context(agent_id)
    }
    
    # Generate implementation task file
    create_implementation_task_file(workspace_dir, implementation_context)
```

#### Phase 3: Implementation Agent Task Structure
Each implementation agent receives a focused task file:

##### Implementation Task Template:
```markdown
# Implementation Agent [N]: Feature [XXX] - Step [YYY]

## Your Implementation Mission
Implement this specific step following SOLID architecture principles.

## Step Specification
[Auto-inserted from steps/feature_XXX/step_YYY.md]

## Current Codebase State
[Auto-inserted current file structure and existing code]

## Available Datasets
[Auto-inserted from datasets/feature_XXX/]

## Scope (IMPLEMENT ONLY THIS STEP)
### Files to Create/Modify:
[Specific files listed from step specification]

### Key Components:
[Detailed component breakdown from step specification]

## Acceptance Criteria
[Auto-inserted from step specification]

## Test Strategy
### Unit Tests Required:
[Test cases from step specification]

### Integration Points:
[Dependencies and integration requirements]

### Dataset Usage:
- Use test_data.json for positive test cases
- Use edge_cases.json for edge case testing
- Use mock_responses.json for external API mocking
- Validate against validation_cases.json

## Parallel Implementation Safety
Parallel Agent Info: [Other agent's current task]
Shared Resources: [Any shared components to coordinate]
Conflict Prevention: [Specific guidelines to avoid conflicts]

## Implementation Guidelines
### SOLID Principles:
- Single Responsibility: Each class has one reason to change
- Open/Closed: Open for extension, closed for modification
- Liskov Substitution: Derived classes must be substitutable
- Interface Segregation: Many specific interfaces
- Dependency Inversion: Depend on abstractions


### LLM Integration (if applicable):
- Use litellm with Google Gemini
- Implement LangWatch Scenario monitoring
- Follow prompt engineering best practices
- Handle Spanish language processing correctly

## Definition of Done
- [ ] Code implemented following SOLID principles
- [ ] All unit tests passing
- [ ] Integration tests working
- [ ] Dataset scenarios validated
- [ ] No conflicts with parallel agent
- [ ] Documentation updated
- [ ] Step marked as completed in progress
```



#### Phase 5: Progress Management and Next Actions
```python
def suggest_next_implementation_steps():
    """Analyze progress and suggest next actions"""
    
    progress = load_progress_state()
    
    # Calculate overall progress
    total_steps = count_total_steps(progress)
    completed_steps = count_completed_steps(progress)
    progress_percentage = (completed_steps / total_steps) * 100
    
    print(f"📊 Overall Progress: {completed_steps}/{total_steps} steps ({progress_percentage:.1f}% complete)")
    
    # Find next available work
    next_steps = find_implementable_steps(progress)
    
    if len(next_steps) == 0:
        print("🎉 All implementation complete!")
        print("🚀 Ready for final testing and deployment")
    elif len(next_steps) == 1:
        print(f"🚀 Ready for final step: {next_steps[0]['feature']}.{next_steps[0]['step']}")
        print("💡 Run: claude-code feature-implement.md")
    else:
        print(f"🚀 Ready for parallel implementation:")
        for i, step in enumerate(next_steps[:2]):
            print(f"   Option {i+1}: {step['feature']}.{step['step']}")
        print("💡 Run: claude-code feature-implement.md")
    
    # Show any blocked steps
    blocked_steps = find_blocked_steps(progress)
    if blocked_steps:
        print(f"⏳ Blocked steps waiting for dependencies:")
        for step in blocked_steps[:3]:
            print(f"   {step['feature']}.{step['step']} (waiting for {step['dependency']})")
```

## Agent Workspace Structure
```
src/
├── .agents/                # Temporary implementation workspaces
│   ├── agent_1/
│   │   ├── current_task.md
│   │   ├── datasets.json
│   │   ├── codebase_state.json
│   │   └── progress.json
├── .progress.json          # Central progress tracking
├── datasets/               # Test datasets (read-only for agents)
├── steps/                  # Implementation steps (read-only for agents)
└── project_name/    # Main project code (modified by agents)
```

## Example Usage Flow
```bash
# User runs this repeatedly until all features implemented
claude-code feature-implement.md

# Example Output:
# 🚀🚀 Implementing steps in parallel:
#    Agent 1: feature_001.step_002 (Question Repository)
#    Agent 2: feature_004.step_001 (Data Models)
# ⏳ Agents working... (estimated 4 minutes)
# ✅ Both steps completed successfully!
# 📊 Progress: 4/28 steps (14% complete)
# 🚀 Ready for next parallel implementation
# 💡 Run: claude-code feature-implement.md
```

## Quality Assurance
- **Code Validation**: Each implementation validated against acceptance criteria
- **Test Execution**: All tests must pass before step completion
- **Dataset Validation**: Implementation tested against all dataset scenarios
- **Integration Checks**: Dependencies and integration points verified
- **SOLID Compliance**: Architecture principles enforced
- **Spanish ENS Standards**: Domain-specific requirements validated