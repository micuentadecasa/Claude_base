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
3. **Validate LLM performance** using LangWatch Scenario library
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
    │   └── scenario_tests.py
```

### Test Execution Framework:

#### Unit Testing Implementation:
```python
# tests/unit/test_feature_001/test_document_analyzer.py
import pytest
from unittest.mock import Mock, patch
from src.features.feature_001.domain.services.data_analyzer import DataAnalyzer
from src.features.feature_001.domain.entities.data_item import DataItem

class TestDataAnalyzer:
    def setup_method(self):
        self.llm_service = Mock()
        self.processor = Mock()
        self.analyzer = DataAnalyzer(self.llm_service, self.processor)
    
    def test_analyze_valid_data(self):
        # Given
        data_item = DataItem("1", "sample data content", "text", {})
        self.llm_service.analyze.return_value = {"score": 95, "issues": []}
        self.processor.process.return_value = True
        
        # When
        result = self.analyzer.analyze(data_item, ["data_quality"])
        
        # Then
        assert result.score == 95
        assert result.is_valid is True
        self.llm_service.analyze.assert_called_once()
    
    def test_analyze_invalid_data(self):
        # Given
        data_item = DataItem("2", "incomplete data", "text", {})
        self.llm_service.analyze.return_value = {
            "score": 45, 
            "issues": ["missing_required_fields"]
        }
        self.processor.process.return_value = False
        
        # When
        result = self.analyzer.analyze(data_item, ["data_completeness"])
        
        # Then
        assert result.score == 45
        assert result.is_valid is False
        assert "missing_required_fields" in result.issues
    
    def test_analyze_handles_llm_failure(self):
        # Given
        data_item = DataItem("3", "content", "text", {})
        self.llm_service.analyze.side_effect = Exception("LLM service unavailable")
        
        # When & Then
        with pytest.raises(DataAnalysisError):
            self.analyzer.analyze(data_item, ["data_quality"])
    
    @pytest.mark.parametrize("data_type,expected_score", [
        ("structured", 95),
        ("unstructured", 85),
        ("mixed", 90)
    ])
    def test_analyze_different_data_types(self, data_type, expected_score):
        # Given
        data_item = DataItem("1", f"{data_type} content", "text", {"type": data_type})
        self.llm_service.analyze.return_value = {"score": expected_score, "issues": []}
        
        # When
        result = self.analyzer.analyze(data_item, [f"{data_type}_validation"])
        
        # Then
        assert result.score == expected_score
```

#### Integration Testing Implementation:
```python
# tests/integration/test_feature_001/test_data_processing_flow.py
import pytest
from src.features.feature_001.application.use_cases.process_data import ProcessDataUseCase
from src.features.feature_001.infrastructure.clients.llm_client import LiteLLMClient
from langwatch_scenario import Scenario

class TestDataProcessingFlow:
    def setup_method(self):
        # Initialize real dependencies
        self.llm_client = LiteLLMClient()
        self.scenario = Scenario(
            api_key=os.getenv('LANGWATCH_API_KEY'),
            project_id="your-project-integration-tests",
            environment="test"
        )
        
        # Load test dataset
        with open("datasets/feature_001/test_data.json") as f:
            self.test_data = json.load(f)
    
    def test_end_to_end_data_processing(self):
        # Given
        test_data = self.test_data["data_samples"][0]
        
        # When
        with self.scenario.test_case("data_processing_integration") as test:
            test.input(
                data_id=test_data["id"],
                processing_rules=test_data["processing_rules"]
            )
            
            result = self.process_use_case.execute(
                test.input.data_id,
                test.input.processing_rules
            )
            
            test.expect(result).to_match_expected_status(test_data["expected_result"]["status"])
            test.expect(result).to_have_quality_score_within(test_data["expected_result"]["score"], tolerance=5)
    
    def test_complex_data_validation_flow(self):
        # Given
        complex_data = self.test_data["complex_samples"][0]
        
        # When
        with self.scenario.test_case("complex_data_validation") as test:
            test.input(
                data_id=complex_data["id"],
                validation_rules=complex_data["validation_rules"]
            )
            
            result = self.process_use_case.execute(
                test.input.data_id,
                test.input.validation_rules
            )
            
            test.expect(result).to_match_expected_status(complex_data["expected_result"]["status"])
            test.expect(result).to_have_valid_processing_results(complex_data["expected_result"]["processing_correct"])
    
    def test_error_handling_corrupted_data(self):
        # Given
        corrupted_data = self.test_data["edge_cases"][1]  # Corrupted data
        
        # When & Then
        with pytest.raises(DataCorruptedException):
            self.process_use_case.execute(
                corrupted_data["id"],
                ["basic_processing"]
            )
```

#### LLM Validation Testing:
```python
# tests/llm_validation/test_feature_001/test_prompt_quality.py
import pytest
from langwatch_scenario import Scenario
from src.shared.llm.litellm_client import LiteLLMClient

class TestDomainSpecificScenarios:
    def setup_method(self):
        # Initialize LangWatch Scenario library
        self.scenario = Scenario(
            api_key=os.getenv('LANGWATCH_API_KEY'),
            project_id="your-domain-validation"
        )
        self.llm_client = LiteLLMClient()
    
    def test_domain_validation_scenario(self):
        """Test domain-specific validation using scenario library"""
        # Define scenario for domain validation
        validation_scenario = self.scenario.create_scenario(
            name="domain_specific_validation",
            description="Validate data against domain-specific standards",
            tags=["domain", "validation", "quality"]
        )
        
        # Test case: Incomplete data validation
        with validation_scenario.test_case("incomplete_data_validation") as test:
            test.input(
                data_content="Sample incomplete data for validation",
                validation_type="quality_check"
            )
            
            response = self.llm_client.analyze_domain_compliance(
                test.input.data_content,
                test.input.validation_type
            )
            
            # Validate response using scenario expectations
            test.expect(response).to_identify_missing_elements([
                "required_field_1",
                "required_field_2",
                "validation_criteria_3"
            ])
            
            test.expect(response).to_provide_improvement_recommendations([
                "Add missing required fields",
                "Improve data structure",
                "Implement validation rules"
            ])
    
    def test_business_rule_scenario(self):
        """Test business rule validation using scenario library"""
        business_scenario = self.scenario.create_scenario(
            name="business_rule_validation",
            description="Validate data against business rules and requirements",
            tags=["business_rules", "validation", "compliance"]
        )
        
        with business_scenario.test_case("rule_compliance_check") as test:
            test.input(
                data_content="Sample data for business rule validation",
                validation_type="business_rules"
            )
            
            response = self.llm_client.analyze_domain_compliance(
                test.input.data_content,
                test.input.validation_type
            )
            
            test.expect(response).to_identify_rule_violations([
                "rule_violation_1",
                "rule_violation_2",
                "missing_requirement_3"
            ])
            
            test.expect(response).to_have_compliance_score().greater_than(70)
    
    def test_monitoring_compliance_scenario(self):
        """Test NES monitoring requirements using scenario library"""
        monitoring_scenario = self.scenario.create_scenario(
            name="nes_monitoring_compliance",
            description="Validate monitoring and incident response procedures",
            tags=["nes", "monitoring", "incident_response"]
        )
        
        with monitoring_scenario.test_case("basic_monitoring") as test:
            test.input(
                document_content="Monitorizamos la red con herramientas básicas de SNMP",
                validation_type="monitoring_procedures"
            )
            
            response = self.llm_client.analyze_nes_compliance(
                test.input.document_content,
                test.input.validation_type
            )
            
            test.expect(response).to_recommend_improvements([
                "Implementar detección de intrusiones (IDS/IPS)",
                "Establecer procedimientos de respuesta a incidentes",
                "Configurar alertas automatizadas",
                "Definir métricas de seguridad"
            ])
    
    def test_spanish_language_accuracy_scenario(self):
        """Test Spanish language accuracy in NES compliance responses"""
        language_scenario = self.scenario.create_scenario(
            name="spanish_language_accuracy",
            description="Validate Spanish language quality in compliance responses",
            tags=["spanish", "language", "nes_terminology"]
        )
        
        with language_scenario.test_case("technical_terminology") as test:
            test.input(
                document_content="Política de seguridad de la información",
                validation_type="general_compliance"
            )
            
            response = self.llm_client.analyze_nes_compliance(
                test.input.document_content,
                test.input.validation_type
            )
            
            # Validate proper Spanish technical terminology
            test.expect(response).to_use_proper_spanish_grammar()
            test.expect(response).to_include_nes_technical_terms([
                "Esquema Nacional de Seguridad",
                "medidas de seguridad",
                "análisis de riesgos",
                "continuidad del servicio"
            ])
    
    def test_performance_scenario(self):
        """Test LLM response performance using scenario library"""
        performance_scenario = self.scenario.create_scenario(
            name="llm_performance_validation",
            description="Validate LLM response times and quality",
            tags=["performance", "response_time", "quality"]
        )
        
        with performance_scenario.test_case("response_time_validation") as test:
            start_time = time.time()
            
            test.input(
                document_content="Large document content for performance testing...",
                validation_type="full_nes_compliance"
            )
            
            response = self.llm_client.analyze_nes_compliance(
                test.input.document_content,
                test.input.validation_type
            )
            
            response_time = time.time() - start_time
            
            test.expect(response_time).to_be_less_than(30)  # 30 second requirement
            test.expect(response).to_have_quality_score().greater_than(0.85)
    
    def test_error_handling_scenario(self):
        """Test error handling scenarios using scenario library"""
        error_scenario = self.scenario.create_scenario(
            name="error_handling_validation",
            description="Validate error handling in various failure scenarios",
            tags=["error_handling", "resilience", "fallback"]
        )
        
        with error_scenario.test_case("llm_service_unavailable") as test:
            # Mock LLM service failure
            with patch.object(self.llm_client, 'analyze_nes_compliance') as mock_analyze:
                mock_analyze.side_effect = Exception("LLM service unavailable")
                
                test.input(
                    document_content="Test document content",
                    validation_type="backup_procedures"
                )
                
                # Expect graceful error handling
                with pytest.raises(NESValidationServiceError) as exc_info:
                    self.llm_client.analyze_nes_compliance(
                        test.input.document_content,
                        test.input.validation_type
                    )
                
                test.expect(str(exc_info.value)).to_be_in_spanish()
                test.expect(str(exc_info.value)).to_provide_helpful_guidance()

#### Scenario Configuration:
```python
# tests/llm_validation/scenario_config.py
import os
from langwatch_scenario import ScenarioConfig

# Configure LangWatch Scenario library for NES compliance testing
scenario_config = ScenarioConfig(
    api_key=os.getenv('LANGWATCH_API_KEY'),
    project_id="nes-compliance-system",
    environment="test",
    default_tags=["nes", "spanish", "compliance"],
    quality_thresholds={
        "accuracy": 0.85,
        "response_time": 30.0,
        "spanish_grammar": 0.95,
        "nes_terminology": 0.90
    },
    scenario_library_path="datasets/llm_scenarios/",
    export_results=True,
    result_format="json"
)
```

#### Dataset Integration with Scenarios:
```python
# tests/llm_validation/test_feature_002/scenario_tests.py
import json
from langwatch_scenario import Scenario, load_scenario_dataset

class TestNESScenarioDatasets:
    def setup_method(self):
        # Load scenario datasets
        self.scenario_data = load_scenario_dataset("datasets/feature_002/nes_scenarios.json")
        self.scenario = Scenario(api_key=os.getenv('LANGWATCH_API_KEY'))
    
    def test_all_nes_compliance_scenarios(self):
        """Run all NES compliance scenarios from dataset"""
        for scenario_data in self.scenario_data["nes_compliance_scenarios"]:
            with self.scenario.test_case(scenario_data["scenario_id"]) as test:
                test.input(**scenario_data["input"])
                
                response = self.llm_client.analyze_nes_compliance(
                    test.input.document_content,
                    test.input.validation_type
                )
                
                # Apply scenario-specific expectations
                expected = scenario_data["expected_analysis"]
                test.expect(response).to_match_expected_structure(expected)
                test.expect(response).to_meet_quality_criteria(
                    scenario_data["quality_criteria"]
                )
```