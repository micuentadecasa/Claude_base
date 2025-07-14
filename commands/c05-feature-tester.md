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

## 🧪 Conversational Agent Testing Requirements

**CRITICAL**: For features with conversational chat interfaces, implement these mandatory test patterns to prevent conversation memory failures:

### Required Environment Variables
```bash
# Add to .env file for testing
GEMINI_API_KEY=your_gemini_api_key_here
LANGSMITH_API_KEY=your_langsmith_key_here  # Optional for tracing
OPENAI_API_KEY=your_openai_key_here        # Required for LangWatch user simulation
```

### Conversation Memory Testing Patterns
```python
@pytest.mark.conversation_memory
def test_multi_turn_information_accumulation():
    """Test agent accumulates information across multiple messages"""
    messages = [
        {"role": "user", "content": "We have a plan"},
        {"role": "assistant", "content": "Tell me more about the plan details"},
        {"role": "user", "content": "RTO is 2 hours"},
        {"role": "assistant", "content": "Good, what about testing frequency?"},
        {"role": "user", "content": "We test annually"}
    ]
    
    state = {"messages": messages}
    result = agent_function(state, config)
    
    # Agent should recognize complete answer from multiple turns
    assert "2 hours" in result.get("accumulated_info", "")
    assert "annually" in result.get("accumulated_info", "")

@pytest.mark.conversation_memory  
def test_no_repetitive_questions():
    """Test agent doesn't ask for already provided information"""
    messages = [
        {"role": "user", "content": "We use authentication system X"},
        {"role": "assistant", "content": "Great! Any additional security measures?"},
        {"role": "user", "content": "Yes, we have MFA enabled"}
    ]
    
    state = {"messages": messages}
    result = agent_function(state, config)
    
    # Agent should NOT ask about authentication again
    response = result["messages"][-1]["content"].lower()
    assert "authentication" not in response

@pytest.mark.conversation_memory
def test_real_world_conversation_flow():
    """Test agent with actual conversation patterns that previously failed"""
    messages = [
        {"role": "assistant", "content": "How can I help you today?"},
        {"role": "user", "content": "We have users and passwords"},
        {"role": "assistant", "content": "Tell me more about authentication"},
        {"role": "user", "content": "We use Entra ID for authentication"},
        {"role": "assistant", "content": "Do you have MFA configured?"},
        {"role": "user", "content": "Yes, MFA is enabled for all critical systems"},
        {"role": "assistant", "content": "What privilege management policies do you have?"},
        {"role": "user", "content": "Privileges are reviewed quarterly"},
        # User asks about different topic - should NOT repeat previous questions
        {"role": "user", "content": "What content is usually included in security training?"}
    ]
    
    state = {"messages": messages}
    result = agent_function(state, config)
    
    # CRITICAL: Agent should NOT ask about authentication details again
    response = result["messages"][-1]["content"].lower()
    assert "entra id" not in response, "Agent should not ask about Entra ID again"
    assert "mfa" not in response, "Agent should not ask about MFA again"
    assert "authentication" not in response, "Agent should not ask about authentication again"
    
    # Should respond appropriately to the training content question
    assert any(keyword in response for keyword in ["content", "training", "security"]), \
        "Agent should address the training content question"
```

### LangWatch Scenario Testing Integration
```bash
# Install LangWatch Scenario for sophisticated agent testing
pip install langwatch-scenario pytest

# Configure scenario testing
python -c "
import scenario
scenario.configure(
    default_model='openai/gpt-4o-mini',  # For user simulation
    cache_key='your-domain-tests',
    verbose=True
)
"
```

### LangWatch Scenario Test Example
```python
import scenario
from your_agent import YourAgentAdapter

@pytest.mark.agent_test
@pytest.mark.asyncio
async def test_domain_expertise_scenario():
    """Test agent demonstrates domain expertise in realistic conversation"""
    
    result = await scenario.run(
        name="domain_expertise_validation",
        description="""
            Test that agent demonstrates expert knowledge in your domain.
            User provides incomplete information and agent should identify gaps
            and ask for specific missing details according to domain standards.
        """,
        agents=[
            YourAgentAdapter(),
            scenario.UserSimulatorAgent(),
            scenario.JudgeAgent(
                criteria=[
                    "Agent should communicate in appropriate language",
                    "Agent should demonstrate domain expertise",
                    "Agent should identify incomplete information",
                    "Agent should request specific missing details",
                    "Agent should maintain professional tone"
                ]
            ),
        ],
        max_turns=8,
        set_id="your-domain-tests",
    )
    
    assert result.success, f"Domain expertise test failed: {result.failure_reason}"
```

### Critical Testing Checklist for Conversational Agents
- [ ] **Full Context Processing**: Agent processes conversation history, not just latest message
- [ ] **Information Accumulation**: Combines partial answers across multiple turns  
- [ ] **No Repetitive Questions**: Doesn't ask for already provided information
- [ ] **Correct Association**: Saves information to appropriate topics/entities
- [ ] **Context Instructions**: Explicit prompt instructions for conversation memory
- [ ] **Memory Tests**: Comprehensive test coverage for conversation scenarios
- [ ] **Real-World Testing**: Tests with exact user conversation patterns
- [ ] **Server Validation**: Always test actual LangGraph server execution, not just mocks
- [ ] **LangWatch Integration**: Use scenario testing for realistic user simulation

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
import scenario
from src.shared.llm.litellm_client import LiteLLMClient

class TestDomainSpecificScenarios:
    def setup_method(self):
        # Configure LangWatch Scenario library
        scenario.configure(
            default_model="openai/gpt-4",
            api_key=os.getenv('LANGWATCH_API_KEY')
        )
        self.llm_client = LiteLLMClient()
    
    @pytest.mark.agent_test
    @pytest.mark.asyncio
    async def test_domain_validation_scenario(self):
        """Test domain-specific validation using scenario library"""
        
        class DomainValidationAgent(scenario.AgentAdapter):
            async def call(self, input: scenario.AgentInput) -> scenario.AgentReturnTypes:
                return domain_validation_agent(input.messages)
        
        result = await scenario.run(
            name="domain specific validation",
            description="""
                User provides domain-specific data that needs validation
                against business rules and quality standards.
            """,
            agents=[
                DomainValidationAgent(),
                scenario.UserSimulatorAgent(),
                scenario.JudgeAgent(criteria=[
                    "Agent should identify missing required fields",
                    "Agent should provide specific improvement recommendations", 
                    "Agent should maintain domain terminology consistency",
                    "Agent should not flag valid data as problematic",
                    "Response should be structured and actionable",
                ])
            ],
        )
        
        assert result.success

@scenario.cache()
def domain_validation_agent(messages) -> scenario.AgentReturnTypes:
    response = litellm.completion(
        model="openai/gpt-4",
        messages=[
            {
                "role": "system",
                "content": """
                    You are a domain validation agent.
                    Analyze data for compliance with business rules and quality standards.
                    Provide specific, actionable recommendations.
                """,
            },
            *messages,
        ],
    )
    return response.choices[0].message
    
    @pytest.mark.agent_test
    @pytest.mark.asyncio
    async def test_business_rule_scenario(self):
        """Test business rule validation using scenario library"""
        
        class BusinessRuleAgent(scenario.AgentAdapter):
            async def call(self, input: scenario.AgentInput) -> scenario.AgentReturnTypes:
                return business_rule_agent(input.messages)
        
        result = await scenario.run(
            name="business rule validation",
            description="""
                User submits business data that needs validation against
                specific business rules and compliance requirements.
            """,
            agents=[
                BusinessRuleAgent(),
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

@scenario.cache()
def business_rule_agent(messages) -> scenario.AgentReturnTypes:
    response = litellm.completion(
        model="openai/gpt-4", 
        messages=[
            {
                "role": "system",
                "content": """
                    You are a business rule compliance agent.
                    Validate data against business rules and regulatory requirements.
                    Provide detailed compliance reports with specific citations.
                """,
            },
            *messages,
        ],
    )
    return response.choices[0].message
    
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

# Environment setup for running scenarios
# Required environment variables:
# export OPENAI_API_KEY=<your-openai-api-key>
# export LANGWATCH_API_KEY=<your-langwatch-api-key>  # optional

# Install dependencies:
# pip install langwatch-scenario pytest
# or
# uv add langwatch-scenario pytest
```

#### Running LangWatch Scenario Tests:
```bash
# Run all agent scenario tests
pytest -s -m agent_test

# Run specific scenario test
pytest -s test_domain_validation_scenarios.py::test_domain_validation_scenario

# Run with verbose output and reporting
pytest -v -s -m agent_test --langwatch-report
```

#### Example Test Execution:
```python
# tests/llm_validation/test_feature_002/scenario_tests.py
import pytest
import scenario
import json

@pytest.mark.agent_test
@pytest.mark.asyncio
async def test_domain_scenarios_from_dataset():
    """Run domain-specific scenarios from dataset"""
    
    # Load scenario datasets
    with open("datasets/feature_002/domain_scenarios.json") as f:
        scenario_data = json.load(f)
    
    class DomainAgent(scenario.AgentAdapter):
        async def call(self, input: scenario.AgentInput) -> scenario.AgentReturnTypes:
            return domain_agent(input.messages)
    
    for test_scenario in scenario_data["domain_validation_scenarios"]:
        result = await scenario.run(
            name=test_scenario["name"],
            description=test_scenario["description"],
            agents=[
                DomainAgent(),
                scenario.UserSimulatorAgent(),
                scenario.JudgeAgent(criteria=test_scenario["success_criteria"])
            ],
        )
        
        assert result.success

@scenario.cache()
def domain_agent(messages) -> scenario.AgentReturnTypes:
    import litellm
    response = litellm.completion(
        model="openai/gpt-4",
        messages=[
            {"role": "system", "content": "You are a domain validation expert."},
            *messages,
        ],
    )
    return response.choices[0].message
```