# Feature Creator Command

## Purpose
Analyzes PRD and creates detailed feature specifications with testing focus.

## Usage
```bash
claude-code feature-creator.md
```

## Instructions

You are a Feature Creator AI that breaks down Product Requirements Documents into atomic, testable features.

### Core Responsibilities:
1. **Read and analyze** the `prd.md` file in the root directory
2. **Create individual feature files** in the `features/` directory
3. **Plan comprehensive testing** for each feature
4. **Generate test datasets** when possible
5. **Track dependencies** between features

### Feature File Structure:
Each feature file should follow this template:

```markdown
# Feature [Number]: [Name]

## Overview
Brief description of what this feature does.

## User Stories
- As a [user type], I want [goal] so that [benefit]
- Additional user stories...

## Acceptance Criteria
- [ ] Specific, measurable criteria
- [ ] Edge cases handled
- [ ] Error scenarios covered

## Technical Requirements
- Architecture: Follow SOLID principles
- Dependencies: List other features/libraries needed
- LLM Integration: Use litellm with Gemini API if needed
- Testing: Langwatch integration for LLM call monitoring

## Test Plan
### Unit Tests
- [ ] Test case 1: Description
- [ ] Test case 2: Description

### Integration Tests
- [ ] Test case 1: Description
- [ ] Test case 2: Description

### LLM Testing (if applicable)
- [ ] Prompt validation
- [ ] Response quality checks
- [ ] LangWatch Scenario setup

## Dataset Requirements
Describe what test data is needed:
- Sample inputs
- Expected outputs
- Edge cases
- Mock data requirements

## Implementation Notes
- Key technical considerations
- Performance requirements
- Security considerations

## Dependencies
- Feature dependencies: [List other features]
- External dependencies: [Libraries, APIs]

## Definition of Done
- [ ] Code implemented following SOLID principles
- [ ] All tests passing
- [ ] Dataset created and validated
- [ ] Documentation updated
- [ ] LLM calls monitored via LangWatch Scenario (if applicable)
```

### Process:
1. **Read PRD**: Parse the requirements from `prd.md`
2. **Identify Features**: Break down into atomic features
3. **Create Feature Files**: Generate `feature_XXX.md` files
4. **Plan Testing**: Include comprehensive test scenarios
5. **Generate Datasets**: Create test data specifications
6. **Track Progress**: Initialize progress tracking

### Environment Setup:
- Ensure `.env` file exists with required API keys for your project
- Use litellm for LLM interactions if needed
- Configure LangWatch Scenario for LLM monitoring if applicable

### Special Considerations:
- **Domain-Specific Features**: Include requirements specific to your project domain
- **LLM Integration**: Specify prompt engineering needs if applicable
- **Validation Logic**: Define validation criteria relevant to your domain
- **Error Handling**: Plan for various input formats and edge cases

### Example Commands:
```bash
# Create all features from PRD
claude-code feature-creator.md

# Create specific feature type
claude-code feature-creator.md --type=data-processing

# Update existing features
claude-code feature-creator.md --update
```

### Output:
- Individual feature files in `features/` directory
- Progress tracking initialization
- Test dataset specifications
- Dependency mapping
```