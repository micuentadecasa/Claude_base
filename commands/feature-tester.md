# Feature Tester Command

## Purpose
Executes comprehensive testing for implemented features with LLM monitoring.

## Usage
```bash
claude-code feature-tester.md --feature=feature_001
```

## Instructions

You are a Feature Tester AI that executes comprehensive testing suites for implemented features.

### Core Responsibilities:
1. **Execute unit tests** with comprehensive coverage
2. **Run integration tests** with real dependencies
3. **Validate LLM performance** using Langwatch
4. **Execute performance tests** with realistic loads
5. **Generate test reports** with actionable insights

### Testing Architecture:
```
tests/
├── unit/
│   ├── test_feature_XXX/
│   │   ├── test_entities.py
│   │   ├── test_services.py
│   │   ├── test_use_cases.py
│   │   └── test_validators.py
├── integration/
│   ├── test_feature_XXX/
│   │   ├── test_document_processing.py
│   │   ├── test_llm_integration.py
│   │   └── test_api_endpoints.py
├── e2e/
│   ├── test_feature_XXX/
│   │   ├── test_user_workflows.py
│   │   └── test_system_integration.py
├── performance/
│   ├── test_feature_XXX/
│   │   ├── test_load_handling.py
│   │   └── test_response_times.py
└── llm_validation/
    ├── test_feature_XXX/
    │   ├── test_prompt_quality.py
    │   ├── test_response_accuracy.py
    │   └── langwatch_scenarios.py
```

### Test Execution Framework:

#### Unit Testing Implementation:
```python
# tests/unit/test_feature_001/test_document_analyzer.py
import pytest
from unittest.mock import Mock, patch
from src.features.feature_001.domain.services.document_analyzer import DocumentAnalyzer
from src.features.feature_001.domain.entities.document import Document

class TestDocumentAnalyzer:
    def setup_method(self):
        self.llm_service = Mock()
        self.validator = Mock()
        self.analyzer = DocumentAnalyzer(self.llm_service, self.validator)
    
    def test_analyze_valid_contract(self):
        # Given
        document = Document("1", "contract content", "pdf", {})
        self.llm_service.analyze.return_value = {"score": 95, "issues": []}
        self.validator.validate.return_value = True
        
        # When
        result = self.analyzer.analyze(document, ["contract_completeness"])
        
        # Then
        assert result.score == 95
        assert result.is_valid is True
        self.llm_service.analyze.assert_called_once()
    
    def test_analyze_invalid_contract(self):
        # Given
        document = Document("2", "incomplete contract", "pdf", {})
        self.llm_service.analyze.return_value = {
            "score": 45, 
            "issues": ["missing_payment_terms"]
        }
        self.validator.validate.return_value = False
        
        # When
        result = self.analyzer.analyze(document, ["contract_completeness"])
        
        # Then
        assert result.score == 45
        assert result.is_valid is False
        assert "missing_payment_terms" in result.issues
    
    def test_analyze_handles_llm_failure(self):
        # Given
        document = Document("3", "content", "pdf", {})
        self.llm_service.analyze.side_effect = Exception("LLM service unavailable")
        
        # When & Then
        with pytest.raises(DocumentAnalysisError):
            self.analyzer.analyze(document, ["contract_completeness"])
    
    @pytest.mark.parametrize("document_type,expected_score", [
        ("contract", 95),
        ("invoice", 98),
        ("receipt", 85)
    ])
    def test_analyze_different_document_types(self, document_type, expected_score):
        # Given
        document = Document("1", f"{document_type} content", "pdf", {"type": document_type})
        self.llm_service.analyze.return_value = {"score": expected_score, "issues": []}
        
        # When
        result = self.analyzer.analyze(document, [f"{document_type}_validation"])
        
        # Then
        assert result.score == expected_score
```

#### Integration Testing Implementation:
```python
# tests/integration/test_feature_001/test_document_processing_flow.py
import pytest
from src.features.feature_001.application.use_cases.analyze_document import AnalyzeDocumentUseCase
from src.features.feature_001.infrastructure.clients.llm_client import LiteLLMClient
from langwatch import langwatch

class TestDocumentProcessingFlow:
    def setup_method(self):
        # Initialize real dependencies
        self.llm_client = LiteLLMClient()
        langwatch.api_key = os.getenv('LANGWATCH_API_KEY')
        langwatch.environment = "test"
        
        # Load test dataset
        with open("datasets/feature_001/test_data.json") as f:
            self.test_data = json.load(f)
    
    def test_end_to_end_contract_analysis(self):
        # Given
        contract_data = self.test_data["contracts"][0]
        
        # When
        with langwatch.trace(
            name="contract_analysis_test",
            tags=["integration_test", "contract_analysis"]
        ):
            result = self.analyze_use_case.execute(
                contract_data["id"],
                contract_data["validation_rules"]
            )
        
        # Then
        assert result.status == contract_data["expected_result"]["status"]
        assert result.score >= contract_data["expected_result"]["score"] - 5  # Allow 5% tolerance
    
    def test_invoice_validation_flow(self):
        # Given
        invoice_data = self.test_data["invoices"][0]
        
        # When
        with langwatch.trace(
            name="invoice_validation_test",
            tags=["integration_test", "invoice_validation"]
        ):
            result = self.analyze_use_case.execute(
                invoice_data["id"],
                invoice_data["validation_rules"]
            )
        
        # Then
        assert result.status == invoice_data["expected_result"]["status"]
        assert result.calculations_correct == invoice_data["expected_result"]["calculations_correct"]
    
    def test_error_handling_corrupted_document(self):
        # Given
        corrupted_doc = self.test_data["edge_cases"][1]  # Corrupted PDF
        
        # When & Then
        with pytest.raises(DocumentCorruptedException):
            self.analyze_use_case.execute(
                corrupted_doc["id"],
                ["basic_validation"]
            )
```

#### LLM Validation Testing:
```python
# tests/llm_validation/test_feature_001/test_prompt_quality.py
import pytest
from langwatch import langwatch
from src.shared.llm.litellm_client import LiteLLMClient