# Progress Tracker Command

## Purpose
Manages feature development progress and state across conversations.

## Usage
```bash
claude-code progress-tracker.md --feature=feature_001
```

## Instructions

You are a Progress Tracker AI that maintains development state and enables efficient context management.

### Core Responsibilities:
1. **Track feature progress** through all development phases
2. **Maintain development state** between conversations
3. **Generate context summaries** for efficient Claude interactions
4. **Identify next steps** and blockers
5. **Manage dependencies** between features

### Progress Structure:
```
progress/
├── feature_XXX/
│   ├── status.json          # Current status and metrics
│   ├── implementation.md    # Implementation details
│   ├── tests.md            # Test execution results
│   ├── next_steps.md       # What to do next
│   ├── blockers.md         # Current blockers and dependencies
│   └── context_summary.md  # Conversation context summary
├── global/
│   ├── project_status.json # Overall project health
│   ├── dependencies.json   # Feature dependency graph
│   └── completion_report.md # Progress report
└── templates/
    ├── status_template.json
    └── summary_template.md
```

### Status Tracking Schema:
```json
{
  "feature_id": "feature_001",
  "name": "Document Analysis System",
  "status": "in_progress",
  "phase": "implementation",
  "completion_percentage": 75,
  "created_date": "2024-07-11T09:00:00Z",
  "last_updated": "2024-07-11T14:30:00Z",
  "estimated_completion": "2024-07-12T16:00:00Z",
  "phases": {
    "planning": {
      "status": "completed",
      "completion_date": "2024-07-11T10:00:00Z",
      "deliverables": ["feature_001.md", "test_plan.md"]
    },
    "dataset_generation": {
      "status": "completed", 
      "completion_date": "2024-07-11T12:00:00Z",
      "deliverables": ["test_data.json", "validation_cases.json"]
    },
    "implementation": {
      "status": "in_progress",
      "completion_percentage": 80,
      "deliverables_completed": ["domain_layer", "application_layer"],
      "deliverables_pending": ["infrastructure_layer", "presentation_layer"]
    },
    "testing": {
      "status": "pending",
      "blockers": ["implementation_incomplete"]
    },
    "deployment": {
      "status": "not_started"
    }
  },
  "metrics": {
    "lines_of_code": 1250,
    "test_coverage": 85,
    "tests_passing": 23,
    "tests_failing": 2,
    "performance_score": 92,
    "llm_accuracy": 87
  },
  "blockers": [
    {
      "id": "block_001",
      "description": "PDF processing library integration",
      "severity": "medium",
      "assigned_to": "infrastructure_team",
      "created_date": "2024-07-11T13:00:00Z"
    }
  ],
  "dependencies": {
    "depends_on": ["shared_llm_service", "document_storage"],
    "blocks": ["feature_002_audit_reports"]
  },
  "next_steps": [
    "Complete infrastructure layer implementation",
    "Integrate PDF processing with error handling",
    "Run comprehensive test suite",
    "Performance optimization for large documents"
  ]
}
```

### Progress Commands:

#### Update Feature Progress:
```python
def update_feature_progress(feature_id: str, updates: Dict[str, Any]):
    """Update feature progress with new information"""
    status_file = f"progress/{feature_id}/status.json"
    
    # Load current status
    with open(status_file, 'r') as f:
        status = json.load(f)
    
    # Apply updates
    status.update(updates)
    status["last_updated"] = datetime.now().isoformat()
    
    # Save updated status
    with open(status_file, 'w') as f:
        json.dump(status, f, indent=2)
    
    # Update global project status
    update_global_status()
```

#### Generate Context Summary:
```python
def generate_context_summary(feature_id: str) -> str:
    """Generate concise context summary for Claude conversations"""
    
    status = load_feature_status(feature_id)
    
    summary = f"""
# Context Summary: {status['name']}

## Current State
- **Status**: {status['status']} ({status['completion_percentage']}% complete)
- **Phase**: {status['phase']}
- **Last Updated**: {status['last_updated']}

## What's Been Done
{format_completed_deliverables(status)}

## Current Focus
{format_current_work(status)}

## Next Steps
{format_next_steps(status['next_steps'])}

## Blockers
{format_blockers(status['blockers'])}

## Architecture Notes
{load_architecture_decisions(feature_id)}

## Recent Code Changes
{get_recent_changes(feature_id)}
"""
    
    # Save summary for easy access
    with open(f"progress/{feature_id}/context_summary.md", 'w') as f:
        f.write(summary)
    
    return summary
```

### Implementation Tracking:
```markdown
# Implementation Progress: Feature 001

## Architecture Completed
- [x] Domain entities (Document, ValidationResult)
- [x] Repository interfaces (DocumentRepository)
- [x] Service interfaces (LLMService, DocumentValidator)
- [x] Use case implementation (AnalyzeDocumentUseCase)
- [ ] Infrastructure adapters (PDFProcessor, DOCXProcessor)
- [ ] API controllers and middleware

## Code Quality Metrics
- SOLID Principles: ✅ Applied
- Dependency Injection: ✅ Implemented  
- Error Handling: ✅ Comprehensive
- Documentation: ⚠️ Needs improvement
- Test Coverage: 85% (target: 90%)

## LLM Integration Status
- [x] LiteLLM client configuration
- [x] Gemini API integration
- [x] Langwatch monitoring setup
- [ ] Prompt optimization
- [ ] Response validation

## Current Implementation Details
```python
# Latest architectural decision: Repository pattern implementation
class DocumentRepository(ABC):
    @abstractmethod
    def save(self, document: Document) -> str: pass
    
    @abstractmethod  
    def get_by_id(self, doc_id: str) -> Optional[Document]: pass

# Current blocker: PDF processing error handling
class PDFProcessor:
    def extract_text(self, file_path: str) -> str:
        # TODO: Add robust error handling for corrupted PDFs
        # See progress/feature_001/blockers.md for details
```
```

### Test Execution Tracking:
```markdown
# Test Execution Results: Feature 001

## Test Suite Status
- **Unit Tests**: 23/25 passing (92%)
- **Integration Tests**: 8/10 passing (80%)
- **LLM Tests**: 5/5 passing (100%)
- **Performance Tests**: 2/3 passing (67%)

## Failing Tests
1. `test_large_document_processing` - Memory issue with 10MB+ files
2. `test_corrupted_pdf_handling` - Exception not properly caught

## Langwatch Scenarios
- Document validation accuracy: 87% (target: 85%) ✅
- Response consistency: 92% ✅
- Error handling: 95% ✅

## Performance Metrics
- Average response time: 3.2s (target: <5s) ✅
- Memory usage peak: 512MB (target: <1GB) ✅
- Throughput: 15 docs/minute (target: 10+) ✅
```

### Dependency Management:
```python
def check_dependencies(feature_id: str) -> Dict[str, str]:
    """Check status of feature dependencies"""
    
    deps = load_dependencies(feature_id)
    status = {}
    
    for dep in deps["depends_on"]:
        dep_status = load_feature_status(dep)
        if dep_status["status"] == "completed":
            status[dep] = "✅ Ready"
        elif dep_status["status"] == "in_progress":
            status[dep] = f"⚠️ In Progress ({dep_status['completion_percentage']}%)"
        else:
            status[dep] = "❌ Not Started"
    
    return status

def update_dependency_graph():
    """Update global dependency graph"""
    all_features = get_all_features()
    
    graph = {
        "nodes": [{"id": f["id"], "status": f["status"]} for f in all_features],
        "edges": []
    }
    
    for feature in all_features:
        for dep in feature.get("dependencies", {}).get("depends_on", []):
            graph["edges"].append({"from": dep, "to": feature["id"]})
    
    with open("progress/global/dependencies.json", 'w') as f:
        json.dump(graph, f, indent=2)
```

### Progress Commands:
```bash
# Update feature status
claude-code progress-tracker.md --feature=feature_001 --status=completed

# Generate context summary
claude-code progress-tracker.md --feature=feature_001 --summary

# Check dependencies
claude-code progress-tracker.md --feature=feature_001 --dependencies

# Generate progress report
claude-code progress-tracker.md --report

# Mark blocker resolved
claude-code progress-tracker.md --feature=feature_001 --resolve-blocker=block_001

# Add next steps
claude-code progress-tracker.md --feature=feature_001 --add-steps="Optimize PDF processing,Add error logging"

# Global project status
claude-code progress-tracker.md --global-status
```

### Context Management for Claude:
```python
def prepare_claude_context(feature_id: str) -> str:
    """Prepare optimized context for Claude conversations"""
    
    summary = generate_context_summary(feature_id)
    
    # Add relevant code snippets
    recent_code = get_recent_code_changes(feature_id, limit=5)
    
    # Add current blockers with details
    blockers = get_detailed_blockers(feature_id)
    
    # Add next priority items
    priorities = get_priority_next_steps(feature_id)
    
    context = f"""
{summary}

## Recent Code Changes
{recent_code}

## Current Blockers (Need Resolution)
{blockers}

## Priority Next Steps
{priorities}

## Ready for: {determine_next_action(feature_id)}
"""
    
    return context
```

### Quality Gates and Milestones:
```python
QUALITY_GATES = {
    "implementation": {
        "tests_passing": 80,
        "test_coverage": 85,
        "code_quality_score": 90,
        "blockers_critical": 0
    },
    "testing": {
        "tests_passing": 95,
        "llm_accuracy": 85,
        "performance_score": 90,
        "all_scenarios_pass": True
    },
    "deployment": {
        "tests_passing": 100,
        "security_scan": "passed",
        "performance_baseline": "met",
        "documentation": "complete"
    }
}

def check_quality_gates(feature_id: str, phase: str) -> Dict[str, bool]:
    """Check if feature meets quality gates for phase transition"""
    
    status = load_feature_status(feature_id)
    gates = QUALITY_GATES[phase]
    results = {}
    
    for gate, threshold in gates.items():
        current_value = status["metrics"].get(gate, 0)
        results[gate] = current_value >= threshold
    
    return results
```

### Integration with Other Commands:
- **Feature Creator**: Initialize progress tracking
- **Feature Developer**: Update implementation progress
- **Dataset Generator**: Track dataset creation
- **Feature Tester**: Update test results
- **Context Manager**: Provide conversation context