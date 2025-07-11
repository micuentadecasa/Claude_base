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
5. **Create Langwatch test scenarios**

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

#### Step 3: Create LLM Test Scenarios
```python
# Langwatch scenarios for LLM testing
def generate_llm_scenarios():
    return {
        "document_validation_scenarios": [
            {
                "scenario_id": "contract_analysis_1",
                "prompt": "Analyze this contract for completeness and compliance",
                "input_document": "Contract content here...",
                "expected_output_type": "validation_report",
                "success_criteria": [
                    "identifies_missing_clauses",
                    "flags_compliance_issues",
                    "provides_actionable_feedback"
                ],
                "langwatch_tags": ["contract_analysis", "compliance_check"]
            },
            {
                "scenario_id": "invoice_validation_1",
                "prompt": "Validate this invoice for required fields and format",
                "input_document": "Invoice content here...",
                "expected_output_type": "validation_result",
                "success_criteria": [
                    "checks_all_required_fields",
                    "validates_format",
                    "calculates_totals"
                ],
                "langwatch_tags": ["invoice_validation", "format_check"]
            }
        ]
    }
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

### Langwatch Test Scenarios:
```json
{
  "langwatch_scenarios": [
    {
      "name": "Document Validation Quality",
      "description": "Test LLM accuracy in document validation",
      "test_cases": [
        {
          "input": {
            "document_content": "Contract with missing payment terms...",
            "validation_type": "contract_completeness"
          },
          "expected_output": {
            "should_identify": ["missing_payment_terms"],
            "should_not_identify": ["missing_signatures"],
            "confidence_threshold": 0.8
          },
          "evaluation_criteria": {
            "accuracy": ">=90%",
            "precision": ">=85%",
            "recall": ">=85%"
          }
        }
      ]
    },
    {
      "name": "Response Consistency",
      "description": "Test LLM consistency across similar documents",
      "test_cases": [
        {
          "input_variants": [
            "Contract A with specific missing clause",
            "Contract B with same missing clause but different wording"
          ],
          "expected_behavior": "consistent_identification",
          "tolerance": 0.1
        }
      ]
    }
  ]
}
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

# Generate LLM test scenarios only
claude-code dataset-generator.md --feature=feature_001 --llm-only

# Generate performance test data
claude-code dataset-generator.md --feature=feature_001 --performance

# Update existing dataset
claude-code dataset-generator.md --feature=feature_001 --update
```

### Integration with Testing:
- **Unit Tests**: Use mock_responses.json
- **Integration Tests**: Use test_data.json
- **LLM Tests**: Use langwatch_scenarios.json
- **Performance Tests**: Use performance_data.json
- **E2E Tests**: Use validation_cases.json

### Quality Metrics:
- Dataset coverage: 100% of feature requirements
- Edge case coverage: 95% of identified scenarios
- LLM scenario coverage: All critical user journeys
- Performance scenario coverage: All expected load patterns