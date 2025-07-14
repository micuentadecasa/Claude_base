# Efficient Feature Planning

## Purpose
Quickly plans the next 2 unplanned features directly without slow agents.

## Usage
```bash
claude-code commands/feature-plan.md
```

## Instructions

You are an Efficient Feature Planning system that creates planning artifacts directly using available tools.

### Process:

#### Step 1: Load Progress and Select Features
1. Read `src/.progress.json` to get current state
2. Find all features where `"planned": false`
3. Select first 2 unplanned features (or remaining if < 2)
4. If no unplanned features, display completion message

#### Step 2: Create Planning Artifacts Directly
For each selected feature:
1. Read feature specification from `features/feature_XXX.md`
2. Create `datasets/feature_XXX/` directory with all required files
3. Create `steps/feature_XXX/` directory with implementation steps
4. Update `"planned": true` in `src/.progress.json`

#### Step 3: Required Dataset Files
For each feature, create these files:
- `test_data.json` - test scenarios and data for the use case
- `validation_cases.json` - Validation test cases with expected results
- `edge_cases.json` - Error conditions and edge cases
- `mock_responses.json` - Mock LLM/API responses for testing
- `performance_data.json` - Performance and load testing scenarios
- `langwatch_scenarios.py` - LangWatch scenario tests for LLM calls

#### Step 4: Required Step Files
For each feature, create these implementation step files:
- `step_001_domain_entities.md` - Domain layer entities and business rules
- `step_002_repository_interfaces.md` - Data access layer interfaces
- `step_003_[feature_specific].md` - Feature-specific core logic
- `step_004_llm_integration.md` - LLM integration (if applicable)
- `step_00N_integration_testing.md` - Final integration testing
- `steps_overview.md` - Complete implementation roadmap

### Implementation Requirements:


#### Dataset Quality Standards:
- Comprehensive test coverage for all feature functionality
- Realistic data that reflects actual implementation scenarios for the use case
- Edge cases covering error conditions and invalid inputs
- Performance data for load testing and optimization
- LangWatch scenarios for LLM accuracy validation

#### Step Planning Standards:
- Clear, actionable implementation steps
- SOLID architecture principles
- Clean separation of concerns (domain, application, infrastructure, presentation)
- Comprehensive testing strategy for each step
- Dependencies and integration points clearly defined

### Progress Tracking:
After creating artifacts for each feature, update `src/.progress.json`:
```json
{
  "feature_XXX": {
    "planned": true,
    "datasets_generated": true,
    "steps_created": true
  }
}
```

### Success Criteria:
- All dataset files created with quality content
- All step files created with clear implementation guidance
- Progress tracking updated accurately
- Ready for implementation phase using feature-implement.md

Execute this process efficiently using direct tool calls without spawning separate agents.