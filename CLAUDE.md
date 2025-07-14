# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose
This is a template repository for building domain-specific applications using Claude Code. It provides a structured approach to feature development with comprehensive testing and LLM monitoring, adaptable to any domain or use case.

## Command-Based Development Workflow

### Core Development Commands (in execution order)
```bash
# c00: Enhance pre-PRD into comprehensive PRD
claude-code commands/c00-prd-enhancer.md

# c01: Initial environment setup
claude-code commands/c01-environment-setup.md

# c02: Create feature specifications from PRD
claude-code commands/c02-feature-creator.md

# c03: Generate test datasets for specific features
claude-code commands/c03-dataset-generator.md --feature=feature_001

# c04: Implement features with SOLID architecture
claude-code commands/c04-feature-developer.md --feature=feature_001

# c05: Execute comprehensive testing with LLM monitoring
claude-code commands/c05-feature-tester.md --feature=feature_001
```

### Utility Commands (used internally by other commands)
```bash
# c08: Manage conversation context efficiently (high-level utility)
claude-code commands/c08-context-manager.md --feature=feature_001

# c09: Track progress across all features (high-level utility)
claude-code commands/c09-progress-tracker.md
```

### Feature-Specific Development
```bash
# Work on specific feature types (customize for your domain)
claude-code commands/c04-feature-developer.md --feature=feature_001 --type=data-processing
claude-code commands/c04-feature-developer.md --feature=feature_002 --type=business-validation
claude-code commands/c04-feature-developer.md --feature=feature_003 --type=user-interface

# Run targeted test suites
claude-code commands/c05-feature-tester.md --feature=feature_001 --suite=unit
claude-code commands/c05-feature-tester.md --feature=feature_002 --suite=integration
claude-code commands/c05-feature-tester.md --feature=feature_003 --suite=e2e
```

## Architecture Overview

### SOLID Architecture Pattern
The codebase follows Clean Architecture principles with clear separation of concerns:

```
src/
├── features/
│   └── feature_XXX/
│       ├── domain/          # Business logic and entities
│       │   ├── entities/
│       │   ├── repositories/
│       │   └── services/
│       ├── application/      # Use cases and application logic
│       │   ├── use_cases/
│       │   ├── dto/
│       │   └── interfaces/
│       ├── infrastructure/   # External dependencies
│       │   ├── adapters/
│       │   ├── clients/
│       │   └── config/
│       └── presentation/     # Controllers and UI
│           ├── controllers/
│           ├── middleware/
│           └── validators/
└── shared/
    ├── llm/                 # LLM integration layer
    ├── utils/
    └── exceptions/
```

### LLM Integration Standards
- **LiteLLM**: All LLM calls use LiteLLM with configurable LLM providers
- **LangWatch Scenario**: Comprehensive testing and monitoring for LLM interactions
- **Environment Variables**: API keys stored in `.env` file
- **Error Handling**: Graceful fallbacks for LLM service failures
- **Testing**: Agent-based scenario testing with automatic simulation

### Data Processing Architecture
- **Data Ingestion**: Configurable processors for various data formats (JSON, CSV, XML, etc.)
- **Business Logic**: Domain-specific validation and processing rules
- **Quality Assurance**: Data validation and quality checks
- **Error Handling**: Comprehensive handling of corrupted/malformed data

## Testing Strategy

### Test Structure
```
tests/
├── unit/           # Isolated component tests
├── integration/    # Service integration tests
├── e2e/           # End-to-end workflow tests
├── performance/   # Load and performance tests
└── llm_validation/ # LLM prompt and response quality tests
```

### LLM Testing Requirements
- **LangWatch Scenario Integration**: Monitor all LLM calls during testing using scenario library
- **Prompt Validation**: Ensure prompts produce consistent results
- **Response Quality**: Validate accuracy against expected outcomes
- **Domain-Specific Testing**: Specific testing for your domain terminology and requirements

### Coverage Requirements
- **Unit Tests**: 90%+ code coverage
- **Integration Tests**: All API endpoints and service interactions
- **E2E Tests**: Complete user workflows
- **Performance Tests**: Data processing under load

## Development Guidelines

### Feature Development Process
1. **Analyze PRD**: Use `feature-creator.md` to break down requirements
2. **Generate Tests**: Create comprehensive test datasets
3. **Implement**: Follow SOLID principles with dependency injection
4. **Test**: Execute full test suite with LLM monitoring
5. **Track**: Update progress and documentation

### Code Quality Standards
- **SOLID Principles**: Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion
- **Dependency Injection**: All dependencies injected through constructors
- **Error Handling**: Comprehensive exception handling with custom exceptions
- **Logging**: Structured logging for debugging and monitoring

### LLM Development Best Practices
- **API Key Management**: Always use environment variables
- **Monitoring**: Enable LangWatch for all LLM calls
- **Fallback Handling**: Graceful degradation when LLM services fail
- **Prompt Engineering**: Validate prompts against test datasets

## Context Management

### Efficient Development Sessions
- Use `context-manager.md` to load relevant context for specific features
- Switch between features efficiently without conversation bloat
- Compress context when sessions become too large
- Start fresh sessions with essential context only

### Progress Tracking
- Real-time progress monitoring across all features
- Quality gates for code coverage, LLM accuracy, and performance
- Blocker identification and resolution tracking
- Automated progress reporting

## Environment Setup

### Required Environment Variables
```bash
YOUR_LLM_API_KEY=your_preferred_llm_api_key  # e.g., OPENAI_API_KEY, GEMINI_API_KEY, etc.
LANGWATCH_API_KEY=your_langwatch_api_key
# Add other API keys relevant to your domain
```

### Development Dependencies
- Python 3.8+
- Domain-specific processing libraries (adapt to your use case)
- LiteLLM for LLM integration
- LangWatch Scenario library for LLM testing and monitoring
- pytest for testing framework

### Installation Commands
```bash
# Install LangWatch Scenario library
pip install langwatch-scenario pytest
# or with uv
uv add langwatch-scenario pytest
```

## Project Structure

### Key Directories
- `commands/`: Development workflow commands
- `features/`: Feature specifications and implementations
- `datasets/`: Test data and validation scenarios
- `progress/`: Progress tracking and quality gates
- `context/`: Context management for efficient development
- `src/`: Source code following Clean Architecture
- `tests/`: Comprehensive test suites

### Documentation Files
- `prd.md`: Product Requirements Document
- `instructions.md`: Detailed development instructions
- `README.md`: Complete usage guide with examples

This repository provides a complete framework for building robust, testable, and maintainable domain-specific applications using Claude Code's command-based development approach.