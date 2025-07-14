# Dataset Generator Command

## Purpose
Creates comprehensive test datasets for feature validation and LLM testing.

## Usage
```bash
claude-code dataset-generator.md --feature=feature_001
```

## Instructions

You are a Dataset Generator AI that creates realistic, comprehensive test datasets for feature validation.

### Core Responsibilities:
1. **Generate test datasets** based on feature specifications
2. **Create mock data** for various scenarios
3. **Build validation datasets** for LLM testing
4. **Generate edge case scenarios**
5. **Create Langwatch scenario test**

### Dataset Structure:
```
datasets/
├── feature_XXX/
│   ├── test_data.json          # Main test dataset
│   ├── mock_responses.json     # Mock API/LLM responses
│   ├── validation_cases.json   # Validation test cases
│   ├── edge_cases.json         # Edge case scenarios
│   ├── performance_data.json   # Performance testing data
│   └── langwatch_scenarios.json # LLM monitoring scenarios
└── shared/
    ├── common_fixtures.json
    └── user_profiles.json
```

### Dataset Generation Process:

#### Step 1: Analyze Feature Requirements
```python
# Read feature specification
feature_spec = read_feature_file("features/feature_XXX.md")
test_requirements = extract_test_requirements(feature_spec)
```

#### Step 2: Generate Core Test Data
```python
# Example for document analysis feature
def generate_document_analysis_dataset():
    return {
        "valid_documents": [
            {
                "id": "doc_001",
                "content": "Sample business contract with all required clauses...",
                "file_type": "pdf",
                "expected_validation": "pass",
                "validation_rules": ["contract_completeness", "legal_compliance"]
            },
            {
                "id": "doc_002", 
                "content": "Invoice with proper formatting and required fields...",
                "file_type": "pdf",
                "expected_validation": "pass",
                "validation_rules": ["invoice_format", "required_fields"]
            }
        ],
        "invalid_documents": [
            {
                "id": "doc_003",
                "content": "Contract missing essential clauses...",
                "file_type": "pdf",
                "expected_validation": "fail",
                "validation_errors": ["missing_termination_clause", "missing_payment_terms"]
            }
        ]
    }
```

#### Step 3: Create LangWatch Scenario Tests
```python
# LangWatch Scenario library implementation
import scenario
import pytest

def generate_llm_scenario_tests():
    """Generate scenario test files using LangWatch Scenario library"""
    
    # Example scenario test for business validation
    scenario_test = """
import pytest
import scenario
import litellm

# Configure the default model for simulations
scenario.configure(default_model="openai/gpt-4")

@pytest.mark.agent_test
@pytest.mark.asyncio
async def test_business_validation_agent():
    # 1. Create your agent adapter
    class BusinessValidationAgent(scenario.AgentAdapter):
        async def call(self, input: scenario.AgentInput) -> scenario.AgentReturnTypes:
            return business_validation_agent(input.messages)

    # 2. Run the scenario
    result = await scenario.run(
        name="business rule validation",
        description=\"\"\"
            User submits business data that needs validation against 
            domain-specific rules and compliance requirements.
        \"\"\",
        agents=[
            BusinessValidationAgent(),
            scenario.UserSimulatorAgent(),
            scenario.JudgeAgent(criteria=[
                "Agent should identify all validation issues",
                "Agent should provide specific recommendations",
                "Agent should maintain domain terminology consistency",
                "Agent should not hallucinate non-existent issues",
                "Response should be structured and actionable",
            ])
        ],
    )

    # 3. Assert the result
    assert result.success

# Example agent implementation using litellm
@scenario.cache()
def business_validation_agent(messages) -> scenario.AgentReturnTypes:
    response = litellm.completion(
        model="openai/gpt-4",
        messages=[
            {
                "role": "system",
                "content": \"\"\"
                    You are a business validation agent.
                    Analyze the provided data for compliance and quality issues.
                    Provide specific, actionable recommendations.
                \"\"\",
            },
            *messages,
        ],
    )
    return response.choices[0].message
"""
    
    return scenario_test
```

### Document Analysis Dataset Example:

#### Valid Test Documents:
```json
{
  "contracts": [
    {
      "id": "contract_001",
      "type": "service_agreement",
      "content": "SERVICE AGREEMENT\n\nThis Service Agreement is entered into...",
      "file_type": "pdf",
      "validation_rules": ["contract_completeness", "legal_compliance"],
      "expected_result": {
        "status": "valid",
        "score": 95,
        "issues": [],
        "recommendations": ["Add force majeure clause"]
      }
    }
  ],
  "invoices": [
    {
      "id": "invoice_001",
      "type": "standard_invoice",
      "content": "INVOICE #INV-2024-001\nDate: 2024-07-11\nTo: ABC Corp...",
      "file_type": "pdf",
      "validation_rules": ["invoice_format", "required_fields", "calculation_accuracy"],
      "expected_result": {
        "status": "valid",
        "score": 100,
        "issues": [],
        "calculations_correct": true
      }
    }
  ]
}
```

#### Invalid Test Documents:
```json
{
  "contracts": [
    {
      "id": "contract_002",
      "type": "incomplete_contract",
      "content": "Basic agreement without proper terms...",
      "file_type": "pdf",
      "validation_rules": ["contract_completeness"],
      "expected_result": {
        "status": "invalid",
        "score": 45,
        "issues": [
          "missing_termination_clause",
          "missing_payment_terms",
          "missing_liability_clause"
        ],
        "critical_issues": ["missing_payment_terms"]
      }
    }
  ],
  "invoices": [
    {
      "id": "invoice_002",
      "type": "malformed_invoice",
      "content": "Invoice without proper formatting...",
      "file_type": "pdf",
      "validation_rules": ["invoice_format", "required_fields"],
      "expected_result": {
        "status": "invalid",
        "score": 25,
        "issues": [
          "missing_invoice_number",
          "missing_date",
          "incorrect_total_calculation"
        ]
      }
    }
  ]
}
```

#### Edge Cases:
```json
{
  "edge_cases": [
    {
      "id": "edge_001",
      "description": "Empty document",
      "content": "",
      "file_type": "pdf",
      "expected_behavior": "handle_gracefully",
      "expected_error": "document_empty"
    },
    {
      "id": "edge_002",
      "description": "Corrupted PDF",
      "content": "corrupted_binary_data",
      "file_type": "pdf",
      "expected_behavior": "handle_gracefully",
      "expected_error": "document_corrupted"
    },
    {
      "id": "edge_003",
      "description": "Very large document",
      "content_size": "50MB",
      "file_type": "pdf",
      "expected_behavior": "process_or_reject",
      "performance_threshold": "30_seconds"
    }
  ]
}
```

### LLM Mock Responses:
```json
{
  "llm_responses": [
    {
      "scenario": "contract_analysis",
      "input": "Analyze this service agreement...",
      "mock_response": {
        "analysis": "This service agreement contains most essential clauses...",
        "validation_score": 85,
        "missing_clauses": ["force_majeure", "intellectual_property"],
        "compliance_issues": [],
        "recommendations": ["Add IP clause", "Clarify termination conditions"]
      }
    },
    {
      "scenario": "invoice_validation",
      "input": "Validate this invoice...",
      "mock_response": {
        "validation_result": "valid",
        "required_fields_present": true,
        "calculation_accuracy": true,
        "format_compliance": true,
        "issues": []
      }
    }
  ]
}
```

### LangWatch Scenario Test Files:
```python
# test_data_quality_scenarios.py
import pytest
import scenario
import litellm

# Configure the default model for simulations
scenario.configure(default_model="openai/gpt-4")

@pytest.mark.agent_test
@pytest.mark.asyncio
async def test_data_quality_validation():
    """Test agent's ability to identify data quality issues"""
    
    class DataQualityAgent(scenario.AgentAdapter):
        async def call(self, input: scenario.AgentInput) -> scenario.AgentReturnTypes:
            return data_quality_agent(input.messages)

    result = await scenario.run(
        name="data quality assessment",
        description="""
            User provides data that has various quality issues.
            Agent must identify problems and suggest improvements.
        """,
        agents=[
            DataQualityAgent(),
            scenario.UserSimulatorAgent(),
            scenario.JudgeAgent(criteria=[
                "Agent should identify all data quality issues",
                "Agent should categorize issues by severity",
                "Agent should provide specific remediation steps",
                "Agent should not flag valid data as problematic",
                "Response should include confidence scores",
            ])
        ],
    )
    
    assert result.success

@pytest.mark.agent_test
@pytest.mark.asyncio
async def test_business_rule_compliance():
    """Test agent's ability to validate business rules"""
    
    class ComplianceAgent(scenario.AgentAdapter):
        async def call(self, input: scenario.AgentInput) -> scenario.AgentReturnTypes:
            return compliance_agent(input.messages)

    result = await scenario.run(
        name="business rule compliance check",
        description="""
            User submits business data that needs validation against
            specific domain rules and regulatory requirements.
        """,
        agents=[
            ComplianceAgent(),
            scenario.UserSimulatorAgent(),
            scenario.JudgeAgent(criteria=[
                "Agent should validate all applicable business rules",
                "Agent should cite specific rule violations",
                "Agent should provide compliant alternatives",
                "Agent should handle edge cases gracefully",
                "Response should be audit-ready",
            ])
        ],
    )
    
    assert result.success

# Agent implementations
@scenario.cache()
def data_quality_agent(messages) -> scenario.AgentReturnTypes:
    response = litellm.completion(
        model="openai/gpt-4",
        messages=[
            {
                "role": "system", 
                "content": """
                    You are a data quality validation agent.
                    Analyze data for completeness, accuracy, consistency, and validity.
                    Provide structured feedback with specific improvement recommendations.
                """,
            },
            *messages,
        ],
    )
    return response.choices[0].message

@scenario.cache()
def compliance_agent(messages) -> scenario.AgentReturnTypes:
    response = litellm.completion(
        model="openai/gpt-4",
        messages=[
            {
                "role": "system",
                "content": """
                    You are a business compliance validation agent.
                    Check data against business rules and regulatory requirements.
                    Provide detailed compliance reports with citations.
                """,
            },
            *messages,
        ],
    )
    return response.choices[0].message
```

### Performance Test Data:
```json
{
  "performance_scenarios": [
    {
      "scenario": "high_volume_processing",
      "document_count": 100,
      "document_sizes": ["1KB", "10KB", "100KB", "1MB"],
      "concurrent_requests": 10,
      "expected_response_time": "< 5 seconds per document",
      "memory_limit": "512MB"
    },
    {
      "scenario": "large_document_processing",
      "document_size": "10MB",
      "expected_response_time": "< 30 seconds",
      "memory_limit": "1GB"
    }
  ]
}
```

### Dataset Quality Assurance:

#### Validation Rules:
1. **Completeness**: All required fields present
2. **Realism**: Data reflects real-world scenarios
3. **Coverage**: Edge cases and error conditions included
4. **Consistency**: Similar scenarios produce similar results
5. **Traceability**: Clear mapping to feature requirements

#### Generation Commands:
```bash
# Generate full dataset for feature
claude-code dataset-generator.md --feature=feature_001

# Generate specific dataset type
claude-code dataset-generator.md --feature=feature_001 --type=validation

# Generate LangWatch Scenario tests only
claude-code dataset-generator.md --feature=feature_001 --scenario-tests

# Generate performance test data
claude-code dataset-generator.md --feature=feature_001 --performance

# Update existing dataset
claude-code dataset-generator.md --feature=feature_001 --update
```

#### Environment Setup for LangWatch Scenarios:
```bash
# Required environment variables
export OPENAI_API_KEY=<your-openai-api-key>
export LANGWATCH_API_KEY=<your-langwatch-api-key>  # optional, for simulation reporting

# Install dependencies
pip install langwatch-scenario pytest
# or
uv add langwatch-scenario pytest
```

### Integration with Testing:
- **Unit Tests**: Use mock_responses.json
- **Integration Tests**: Use test_data.json
- **LangWatch Scenario Tests**: Use generated test_*_scenarios.py files
- **Performance Tests**: Use performance_data.json
- **E2E Tests**: Use validation_cases.json

#### Running LangWatch Scenario Tests:
```bash
# Run all scenario tests
pytest -s test_*_scenarios.py

# Run specific scenario test
pytest -s test_data_quality_scenarios.py::test_data_quality_validation

# Run with verbose output
pytest -v -s test_*_scenarios.py
```

### Quality Metrics:
- Dataset coverage: 100% of feature requirements
- Edge case coverage: 95% of identified scenarios
- LLM scenario coverage: All critical user journeys
- Performance scenario coverage: All expected load patterns