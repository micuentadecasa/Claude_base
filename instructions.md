## 🔧 Step-by-Step Implementation

### Step 1: Create Features from PRD
```bash
# Generate feature breakdown from PRD
claude-code commands/feature-creator.md

# This creates:
# - features/feature_001.md (Document Processing)
# - features/feature_002.md (NES Validation Engine) 
# - features/feature_003.md (Chat Interface)
# - progress/ directories for each feature
```

**Expected Output:**
```
✅ Created 3 features from PRD:
📄 feature_001.md - Document Processing System
🔍 feature_002.md - NES Compliance Validation Engine  
💬 feature_003.md - Spanish Chat Interface

📊 Progress tracking initialized in progress/ directory
🧪 Test specifications generated for each feature
```

### Step 2: Generate Test Datasets
```bash
# Create comprehensive test datasets for document processing
claude-code commands/dataset-generator.md --feature=feature_001

# Create NES compliance test scenarios
claude-code commands/dataset-generator.md --feature=feature_002 --type=validation

# Create conversation flow test data
claude-code commands/dataset# Complete Usage Instructions & Examples

## 🚀 Quick Start: Document Analysis System

This guide shows how to use all the Claude Code commands to build your Spanish NES document analysis system.

## 📁 Initial Setup

### 1. Complete Environment Setup
```bash
# First, setup the complete development environment
claude-code environment-setup.md --project-name=spanish-nes-analyzer

# This automatically creates:
# - Python virtual environment in backend/venv/
# - Node.js setup for frontend (if needed)
# - Complete Makefile with all commands
# - .env template with required variables
# - .gitignore with proper exclusions
# - Project directory structure
# - Logging and monitoring setup
```

### 2. Install Dependencies and Start Development
```bash
# Setup and install everything
make setup && make install

# Verify environment health
make health

# Start development servers
make dev

# Monitor logs in real-time (in another terminal)
make logs
```

### 3. Copy Command Files
```bash
# Copy all Claude Code commands to commands folder
cp feature-creator.md commands/
cp feature-developer.md commands/
cp dataset-generator.md commands/
cp feature-tester.md commands/
cp progress-tracker.md commands/
cp context-manager.md commands/
cp environment-setup.md commands/
```

### 4. Configure Environment Variables
```bash
# Edit .env file with your actual API keys
nano .env

# Required variables:
GEMINI_API_KEY=your_actual_gemini_key_here
LANGWATCH_API_KEY=your_actual_langwatch_key_here
OPENAI_API_KEY=your_actual_openai_key_here  # For LangWatch user simulation
```

### 2. Create Initial PRD
```bash
# Create your Product Requirements Document
cat > prd.md << 'EOF'
# Spanish NES Document Analysis System

## Overview
An intelligent document analysis system that validates documents against Spanish NES (Esquema Nacional de Seguridad) compliance standards.

## Core Requirements

### Document Processing
- Support PDF and DOCX formats
- Extract text content reliably
- Handle corrupted or malformed files gracefully
- Process documents up to 10MB efficiently

### NES Compliance Validation
- **Backups**: Validate backup procedures, frequency, verification, remote storage
- **Access Control**: Check authentication systems, MFA, privilege management, audit logs
- **Monitoring**: Verify network monitoring, intrusion detection, incident response procedures

### User Interface
- Chat-based interaction in Spanish
- Progressive document analysis workflow
- Clear validation results with specific NES recommendations
- Support for multiple document uploads

### Technical Requirements
- Use Google Gemini via LiteLLM for analysis
- Implement LangWatch monitoring for all LLM calls
- Follow SOLID architecture principles
- Comprehensive error handling and logging
- 90%+ test coverage with realistic scenarios

## Success Criteria
- Accurately identifies NES compliance gaps
- Processes documents within 30 seconds
- Provides actionable recommendations
- Handles edge cases gracefully
- Maintains conversation context across interactions
EOF
```

## 🔧 Step-by-Step Implementation

### Step 1: Create Features from PRD
```bash
# Generate feature breakdown from PRD
claude-code commands/feature-creator.md

# This creates:
# - features/feature_001.md (Document Processing)
# - features/feature_002.md (NES Validation Engine) 
# - features/feature_003.md (Chat Interface)
# - progress/ directories for each feature
```

**Expected Output:**
```
✅ Created 3 features from PRD:
📄 feature_001.md - Document Processing System
🔍 feature_002.md - NES Compliance Validation Engine  
💬 feature_003.md - Spanish Chat Interface

📊 Progress tracking initialized in progress/ directory
🧪 Test specifications generated for each feature
```

### Step 2: Generate Test Datasets
```bash
# Create comprehensive test datasets for document processing
claude-code commands/dataset-generator.md --feature=feature_001

# Create NES compliance test scenarios
claude-code commands/dataset-generator.md --feature=feature_002 --type=validation

# Create conversation flow test data
claude-code commands/dataset-generator.md --feature=feature_003 --type=conversation
```

**Expected Output:**
```
📁 datasets/feature_001/
├── test_data.json          # PDF/DOCX samples
├── validation_cases.json   # Expected outcomes
├── edge_cases.json         # Corrupted files, large docs
└── performance_data.json   # Load testing data

📁 datasets/feature_002/
├── nes_compliance_examples.json  # Valid/invalid examples
├── langwatch_scenarios.json      # LLM testing scenarios
└── mock_responses.json           # Expected AI responses

📁 datasets/feature_003/
├── conversation_flows.json       # Multi-turn conversations
├── memory_test_scenarios.json    # Context preservation tests
└── spanish_language_tests.json   # Language-specific validation
```

### Step 3: Implement Core Features
```bash
# Start with document processing (foundational)
claude-code commands/feature-developer.md --feature=feature_001

# Then implement NES validation engine
claude-code commands/feature-developer.md --feature=feature_002

# Finally the chat interface
claude-code commands/feature-developer.md --feature=feature_003
```

**Implementation follows this pattern for each feature:**

```python
# Generated structure for feature_001 (Document Processing)
src/
├── features/
│   └── feature_001/
│       ├── domain/
│       │   ├── entities/
│       │   │   ├── document.py
│       │   │   └── processing_result.py
│       │   ├── repositories/
│       │   │   └── document_repository.py
│       │   └── services/
│       │       ├── pdf_processor.py
│       │       └── docx_processor.py
│       ├── application/
│       │   ├── use_cases/
│       │   │   └── process_document.py
│       │   └── dto/
│       │       └── document_dto.py
│       ├── infrastructure/
│       │   ├── adapters/
│       │   │   ├── pypdf_adapter.py
│       │   │   └── python_docx_adapter.py
│       │   └── clients/
│       │       └── file_storage_client.py
│       └── presentation/
│           ├── controllers/
│           │   └── document_controller.py
│           └── validators/
│               └── file_validator.py
└── shared/
    ├── llm/
    │   ├── litellm_client.py
    │   └── langwatch_setup.py
    └── exceptions/
        └── document_exceptions.py
```

### Step 4: Run Comprehensive Testing
```bash
# Test document processing with real files
claude-code commands/feature-tester.md --feature=feature_001 --suite=all

# Validate NES compliance engine with LLM scenarios
claude-code commands/feature-tester.md --feature=feature_002 --langwatch

# Test conversation memory and Spanish language handling
claude-code commands/feature-tester.md --feature=feature_003 --conversation-memory
```

### Step 5: Track Progress and Manage Context
```bash
# Check overall project status
claude-code commands/progress-tracker.md --global-status

# Generate context summary for efficient Claude conversations
claude-code commands/context-manager.md --feature=feature_001 --new-session
```

## 💡 Advanced Usage Patterns

### Working with Multiple Features
```bash
# Switch context between features efficiently
claude-code commands/context-manager.md --switch-from=feature_001 --switch-to=feature_002

# Check cross-feature dependencies
claude-code commands/progress-tracker.md --dependencies
```

### Debugging and Problem Resolution
```bash
# Start debugging session with focused context
claude-code commands/context-manager.md --feature=feature_002 --session-type=debugging

# Check for blockers and get resolution suggestions
claude-code commands/progress-tracker.md --feature=feature_002 --blockers
```

### Performance Optimization
```bash
# Run performance tests with realistic loads
claude-code commands/feature-tester.md --feature=feature_001 --suite=performance

# Generate performance optimization recommendations
claude-code commands/feature-developer.md --feature=feature_001 --optimize
```

## 🧪 LangWatch Scenario Testing Example

Here's how the system generates sophisticated testing for your document analysis:

```python
# Auto-generated in datasets/feature_002/langwatch_scenarios.json
{
  "nes_compliance_scenarios": [
    {
      "scenario_id": "backup_validation_incomplete",
      "description": "Test NES backup compliance validation with incomplete information",
      "input_document": "Tenemos copias de seguridad en un NAS QNAP de 8TB",
      "expected_analysis": {
        "compliance_gaps": [
          "missing_backup_frequency",
          "missing_verification_process", 
          "missing_recovery_procedures",
          "missing_offsite_backup"
        ],
        "nes_requirements_missing": [
          "Frecuencia de copias específica",
          "Proceso de verificación de integridad",
          "Plan de recuperación con RTO/RPO",
          "Ubicación remota de respaldo"
        ]
      },
      "langwatch_criteria": {
        "accuracy": ">=85%",
        "identifies_all_gaps": true,
        "provides_nes_guidance": true,
        "maintains_spanish_language": true
      }
    }
  ]
}
```

## 📊 Real-Time Progress Monitoring

The system provides continuous feedback:

```bash
# Get real-time status
claude-code commands/progress-tracker.md --feature=feature_001

# Output:
Feature 001: Document Processing System
├── Status: 🟡 In Progress (75% complete)
├── Phase: Implementation
├── Tests: 18/23 passing (78%)
├── Blockers: 1 medium priority
├── Next: Complete infrastructure layer
└── ETA: 2024-07-12 16:00

Quality Gates:
├── Code Coverage: 82% (target: 90%) ⚠️ 
├── LLM Accuracy: 89% (target: 85%) ✅
├── Performance: 94% (target: 90%) ✅
└── Architecture: SOLID compliant ✅
```

## 🔄 Conversation Context Management

Smart context loading prevents conversation bloat:

```bash
# Start optimized session
claude-code commands/context-manager.md --feature=feature_002 --new-session

# Output includes only relevant context:
# - Current implementation state
# - Active blockers needing resolution  
# - Next priority tasks
# - Architectural decisions made
# - Recent code changes
# - Context7 commands to run first
```

## 🎯 Integration with Claude Code

### Installation
```bash
# Install Claude Code if not already installed
npm install -g @anthropic-ai/claude-code

# Verify installation
claude-code --version
```

### Command Execution
```bash
# Commands can be run directly
claude-code commands/feature-creator.md

# Or with specific parameters
claude-code commands/feature-developer.md --feature=feature_001 --validate-standards

# Or as part of a workflow
claude-code commands/feature-tester.md --feature=feature_001 --generate-report
```

### Integration with Context7
```bash
# Commands automatically suggest Context7 usage
claude-code commands/feature-developer.md --feature=feature_001

# Auto-generates Context7 commands like:
# context7 search "PyPDF2 latest documentation error handling"
# context7 search "python-docx best practices large files"
# context7 search "LiteLLM Gemini API integration patterns"
```

## 🚨 Common Issues and Solutions

### Issue: LLM Calls Failing
```bash
# Check environment setup
claude-code commands/progress-tracker.md --feature=feature_001 --validate-env

# Verify .env file has required keys:
# GEMINI_API_KEY, LANGWATCH_API_KEY, OPENAI_API_KEY
```

### Issue: Tests Failing
```bash
# Run diagnostic testing
claude-code commands/feature-tester.md --feature=feature_001 --diagnose

# Check specific test categories
claude-code commands/feature-tester.md --feature=feature_001 --suite=unit --verbose
```

### Issue: Memory/Context Problems
```bash
# Compress current conversation context
claude-code commands/context-manager.md --feature=feature_001 --compress

# Switch to fresh session with essential context only
claude-code commands/context-manager.md --feature=feature_001 --new-session
```

## 📈 Success Metrics

The system tracks multiple success indicators:

- **Feature Completion**: Percentage and quality gates
- **Test Coverage**: Unit, integration, and scenario tests
- **LLM Performance**: Accuracy, consistency, response time
- **Code Quality**: SOLID compliance, maintainability
- **User Experience**: Conversation flow, memory retention

Use these commands to build, test, and maintain your Spanish NES document analysis system with confidence and efficiency!