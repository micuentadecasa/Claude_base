# Context Manager Command

## Purpose
Optimizes Claude conversation context and manages conversation state efficiently.

## Usage
```bash
claude-code context-manager.md --feature=feature_001
```

## Instructions

You are a Context Manager AI that optimizes Claude conversations by managing context efficiently and preventing conversation bloat.

### Core Responsibilities:
1. **Compact conversation history** while preserving essential context
2. **Load relevant context** for current work
3. **Manage conversation state** across sessions
4. **Optimize Claude interactions** for efficiency
5. **Preserve architectural decisions** and key insights

### Context Structure:
```
context/
├── conversations/
│   ├── feature_001_session_001.md
│   ├── feature_001_session_002.md
│   └── global_decisions.md
├── summaries/
│   ├── feature_001_progress.md
│   ├── architectural_decisions.md
│   └── lessons_learned.md
├── templates/
│   ├── session_start_template.md
│   └── context_load_template.md
└── active/
    ├── current_context.md
    └── next_session_prep.md
```

### Context Optimization Strategies:

#### 1. Smart Context Compression:
```python
def compress_conversation_context(conversation_history: List[Dict]) -> str:
    """Compress conversation while preserving key decisions"""
    
    # Extract key information
    decisions = extract_architectural_decisions(conversation_history)
    code_changes = extract_significant_code_changes(conversation_history)
    blockers_resolved = extract_resolved_blockers(conversation_history)
    insights = extract_key_insights(conversation_history)
    
    compressed = f"""
# Session Summary

## Key Architectural Decisions
{format_decisions(decisions)}

## Significant Code Changes
{format_code_changes(code_changes)}

## Blockers Resolved
{format_resolved_blockers(blockers_resolved)}

## Key Insights Discovered
{format_insights(insights)}

## Current State
{get_current_implementation_state()}
"""
    
    return compressed
```

#### 2. Context Loading for New Sessions:
```python
def prepare_new_session_context(feature_id: str) -> str:
    """Prepare optimized context for starting new Claude session"""
    
    # Load essential context
    feature_status = load_feature_status(feature_id)
    architectural_decisions = load_architectural_decisions(feature_id) 
    current_blockers = get_active_blockers(feature_id)
    priority_tasks = get_priority_tasks(feature_id)
    
    # Load relevant code snippets
    current_code_state = get_current_code_state(feature_id)
    
    # Generate focused context
    context = f"""
# New Session Context: {feature_status['name']}

## Where We Are
- Status: {feature_status['status']} ({feature_status['completion_percentage']}%)
- Current Phase: {feature_status['phase']}
- Last Session: {get_last_session_summary(feature_id)}

## Architectural Foundation
{format_architectural_foundation(architectural_decisions)}

## Current Code State
{format_current_code_state(current_code_state)}

## Immediate Priorities
{format_priority_tasks(priority_tasks)}

## Active Blockers
{format_active_blockers(current_blockers)}

## Ready to: {determine_next_action(feature_id)}

## Context7 Commands to Run First
{generate_context7_commands(feature_id)}
"""
    
    return context
```

#### 3. Architectural Decision Preservation:
```python
ARCHITECTURAL_PATTERNS = {
    "solid_principles": {
        "pattern": "SOLID Architecture",
        "key_decisions": [
            "Single Responsibility: Each class has one reason to change",
            "Dependency Inversion: Depend on abstractions, not concretions",
            "Interface Segregation: Many specific interfaces over one general"
        ]
    },
    "llm_integration": {
        "pattern": "LLM Integration with LiteLLM",
        "key_decisions": [
            "Use litellm for all LLM calls",
            "Gemini API via .env configuration", 
            "Langwatch for monitoring and testing"
        ]
    },
    "testing_strategy": {
        "pattern": "Comprehensive Testing",
        "key_decisions": [
            "Unit tests with real LLM calls",
            "Langwatch scenarios for LLM validation",
            "Performance testing with realistic loads"
        ]
    }
}

def preserve_architectural_decisions(conversation: List[Dict], feature_id: str):
    """Extract and preserve architectural decisions from conversation"""
    
    decisions_found = []
    
    for message in conversation:
        content = message.get("content", "")
        
        # Look for architectural patterns
        for pattern_key, pattern_info in ARCHITECTURAL_PATTERNS.items():
            if any(keyword in content.lower() for keyword in pattern_info["keywords"]):
                decision = {
                    "pattern": pattern_info["pattern"],
                    "context": extract_decision_context(content),
                    "timestamp": message.get("timestamp"),
                    "rationale": extract_rationale(content)
                }
                decisions_found.append(decision)
    
    # Save to architectural decisions file
    save_architectural_decisions(feature_id, decisions_found)
```

### Session Management:

#### Start New Session:
```python
def start_new_session(feature_id: str, session_type: str = "development") -> str:
    """Start new optimized Claude session"""
    
    # Archive previous session if exists
    archive_current_session(feature_id)
    
    # Prepare optimized context
    context = prepare_new_session_context(feature_id)
    
    # Add session-specific instructions
    if session_type == "development":
        context += get_development_instructions(feature_id)
    elif session_type == "testing":
        context += get_testing_instructions(feature_id)
    elif session_type == "debugging":
        context += get_debugging_instructions(feature_id)
    
    # Save as current session context
    save_current_session_context(feature_id, context)
    
    return context

def get_development_instructions(feature_id: str) -> str:
    """Get development-specific instructions"""
    return f"""
## Development Session Instructions

### Before Writing Code:
1. Run Context7 commands to get latest documentation
2. Review current architectural decisions above
3. Check for existing implementations in progress/

### Code Standards to Follow:
- SOLID architecture principles
- Use dependency injection
- Implement comprehensive error handling
- Add type hints and docstrings
- Follow existing code patterns

### LLM Integration Requirements:
- Use litellm for all LLM calls
- Load GEMINI_API_KEY from .env
- Implement Langwatch monitoring
- Handle LLM errors gracefully

### Testing Requirements:
- Write tests alongside implementation
- Include Langwatch scenarios for LLM features
- Test error conditions and edge cases
- Verify performance requirements
"""

def get_testing_instructions(feature_id: str) -> str:
    """Get testing-specific instructions"""
    return f"""
## Testing Session Instructions

### Testing Priorities:
1. Run existing test suite first
2. Identify failing tests and root causes
3. Add missing test coverage
4. Validate LLM performance with Langwatch

### Testing Standards:
- Unit tests: Mock external dependencies, test business logic
- Integration tests: Real LLM calls with monitoring
- Performance tests: Realistic data loads
- Scenario tests: End-to-end user workflows

### LLM Testing Focus:
- Prompt accuracy and consistency
- Response quality validation
- Error handling under various conditions
- Performance under load
"""
```

#### Context Switching Between Features:
```python
def switch_feature_context(from_feature: str, to_feature: str) -> str:
    """Switch context between different features"""
    
    # Save current session state
    save_session_checkpoint(from_feature)
    
    # Load new feature context
    new_context = prepare_new_session_context(to_feature)
    
    # Add context switch summary
    switch_summary = f"""
# Context Switch: {from_feature} → {to_feature}

## Previous Feature State Saved
{get_quick_status(from_feature)}

## New Feature Context Loading
{new_context}

## Cross-Feature Dependencies
{check_cross_feature_dependencies(from_feature, to_feature)}
"""
    
    return switch_summary
```

### Conversation Optimization:

#### 4. Smart Context Loading:
```python
def load_relevant_context(feature_id: str, task_type: str) -> str:
    """Load only relevant context for specific task type"""
    
    base_context = get_base_feature_context(feature_id)
    
    if task_type == "implementation":
        return base_context + get_implementation_context(feature_id)
    elif task_type == "testing":
        return base_context + get_testing_context(feature_id)
    elif task_type == "debugging":
        return base_context + get_debugging_context(feature_id)
    elif task_type == "architecture":
        return base_context + get_architecture_context(feature_id)
    
    return base_context

def get_implementation_context(feature_id: str) -> str:
    """Get context specific to implementation tasks"""
    return f"""
## Implementation Context

### Current Code Structure
{get_current_file_structure(feature_id)}

### Incomplete Implementations
{get_incomplete_implementations(feature_id)}

### Available Dependencies
{get_available_dependencies()}

### Architecture Patterns to Follow
{get_architecture_patterns()}
"""
```

#### 5. Session Continuity:
```python
def prepare_session_continuation(feature_id: str) -> str:
    """Prepare context for continuing interrupted session"""
    
    last_session = get_last_session_state(feature_id)
    
    continuation = f"""
# Session Continuation: {feature_id}

## Last Session Summary
{last_session["summary"]}

## What Was Accomplished
{last_session["completed_tasks"]}

## What Was In Progress
{last_session["in_progress_tasks"]}

## Next Immediate Steps
{last_session["next_steps"]}

## Context Restored
- Code state: {last_session["code_state"]}
- Test status: {last_session["test_status"]}
- Blockers: {last_session["active_blockers"]}

## Ready to Continue With: {last_session["next_action"]}
"""
    
    return continuation
```

### Context Management Commands:
```bash
# Start new optimized session
claude-code context-manager.md --feature=feature_001 --new-session

# Start specific session type
claude-code context-manager.md --feature=feature_001 --session-type=testing

# Switch between features
claude-code context-manager.md --switch-from=feature_001 --switch-to=feature_002

# Continue interrupted session
claude-code context-manager.md --feature=feature_001 --continue-session

# Compress current conversation
claude-code context-manager.md --feature=feature_001 --compress

# Load task-specific context
claude-code context-manager.md --feature=feature_001 --task=debugging

# Archive completed session
claude-code context-manager.md --feature=feature_001 --archive-session
```

### Context Templates:

#### New Session Template:
```markdown
# 🚀 New Session: {feature_name}

## 📊 Current Status
- **Phase**: {current_phase}
- **Progress**: {completion_percentage}%
- **Last Update**: {last_update}

## 🏗️ Architecture Foundation
{architectural_decisions}

## 💻 Code State
{current_implementations}

## 🎯 Session Goals
{session_objectives}

## 🚧 Known Blockers
{active_blockers}

## ▶️ Ready to Start: {next_action}
```

### Integration with Development Workflow:
- **Before Coding**: Load optimized context with architectural decisions
- **During Development**: Track decisions and preserve insights
- **Session Breaks**: Compress and save current state
- **New Sessions**: Load relevant context without conversation bloat
- **Context Switching**: Efficient feature-to-feature transitions