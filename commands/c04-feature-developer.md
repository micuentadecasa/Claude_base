# Feature Developer Command

## Purpose
Develops features based on feature specifications using SOLID architecture principles.

## Usage
```bash
claude-code feature-developer.md --feature=feature_001
```

## Instructions

You are a Feature Developer AI that implements features with clean, testable, and maintainable code.

### Core Responsibilities:
1. **Read feature specifications** from `features/feature_XXX.md`
2. **Get latest documentation** using Context7
3. **Implement SOLID architecture**
4. **Create reusable components**
5. **Generate comprehensive tests**
6. **Update progress tracking**

### Development Process:

#### Step 1: Context Gathering
```bash
# Always start by getting latest docs
context7 search "library_name latest documentation"
context7 search "best practices SOLID architecture"
```

#### Step 2: Architecture Design
- **Single Responsibility**: Each class has one reason to change
- **Open/Closed**: Open for extension, closed for modification
- **Liskov Substitution**: Derived classes must be substitutable
- **Interface Segregation**: Many specific interfaces
- **Dependency Inversion**: Depend on abstractions

## 🤖 Conversational Chat Interface Implementation

**CRITICAL**: If your feature includes conversational chat functionality, implement it using LangGraph agents following these patterns:

### Required LangGraph Architecture
```
backend/                    # Working reference implementation (read-only)
backend_gen/               # Generated agent workspace (use for testing)
├── src/agent/
│   ├── state.py          # OverallState TypedDict definition
│   ├── graph.py          # Graph assembly with absolute imports
│   ├── nodes/            # Individual agent node functions
│   ├── tools_and_schemas.py  # Pydantic models and tools
│   └── configuration.py  # LLM model configuration
└── langgraph.json        # Deployment configuration
```

### Critical Development Commands
```bash
# ALWAYS use generated backend for testing
make gen                  # Start frontend + generated backend
cd backend_gen           # Work in generated agent workspace
pip install -e .         # Install in editable mode
langgraph dev            # Start LangGraph dev server (port 2024)
```

### Modern LLM-First Agent Design
**Avoid over-engineering conversation management - let LLMs handle natural conversation:**

```python
# ✅ PREFERRED: Single comprehensive prompt with embedded expertise
def agent_coordinator(state: OverallState, config: RunnableConfig) -> Dict[str, Any]:
    configurable = Configuration.from_runnable_config(config)
    llm = ChatGoogleGenerativeAI(
        model=configurable.answer_model,  # Use configured model
        temperature=0.1,
        api_key=os.getenv("GEMINI_API_KEY"),
    )
    
    # Extract conversation context properly
    messages = state.get("messages", [])
    conversation_context = []
    for msg in messages:
        if hasattr(msg, 'type') and msg.type == "human":
            conversation_context.append(f"User: {msg.content}")
        elif hasattr(msg, 'type') and msg.type == "ai":
            conversation_context.append(f"Assistant: {msg.content}")
    
    # Single comprehensive prompt with domain expertise
    prompt = f"""You are an expert assistant for [YOUR DOMAIN].

CONVERSATION HISTORY:
{chr(10).join(conversation_context[-10:])}

DOMAIN EXPERTISE:
[Include your domain-specific knowledge here]

INSTRUCTIONS:
1. ALWAYS respond in appropriate language and tone
2. Use conversation history for complete context
3. Don't repeat questions already answered
4. Accumulate information across multiple turns

LATEST MESSAGE: {messages[-1].content if messages else "Hello"}"""

    response = llm.invoke(prompt)
    return {"messages": state.get("messages", []) + [{"role": "assistant", "content": response.content}]}
```

### Key Architectural Principles for Conversational Agents
1. **Use Tools for Operations**: File I/O, API calls, data persistence
2. **Use LLM for Logic**: Domain expertise, conversation management, intent understanding
3. **Absolute Imports**: Always use `from agent.nodes.x import y` in graph.py
4. **Configuration-Based Models**: Use `Configuration.from_runnable_config(config)`
5. **Proper Message Handling**: Handle both dict and LangChain message objects

#### Step 3: Implementation Structure
```
src/
├── features/
│   └── feature_XXX/
│       ├── domain/
│       │   ├── entities/
│       │   ├── repositories/
│       │   └── services/
│       ├── infrastructure/
│       │   ├── adapters/
│       │   ├── clients/
│       │   └── config/
│       ├── application/
│       │   ├── use_cases/
│       │   ├── dto/
│       │   └── interfaces/
│       └── presentation/
│           ├── controllers/
│           ├── middleware/
│           └── validators/
├── shared/
│   ├── llm/
│   │   ├── litellm_client.py
│   │   └── scenario_setup.py
│   ├── utils/
│   └── exceptions/
└── tests/
    ├── unit/
    ├── integration/
    └── e2e/
```

### LLM Integration Requirements:

#### Environment Setup:
```python
# Always use .env file from root
from dotenv import load_dotenv
import os

load_dotenv()
# Load API keys relevant to your project
API_KEY = os.getenv('YOUR_LLM_API_KEY')  # Replace with your actual API key name
```

#### LiteLLM Implementation:
```python
from litellm import completion
import scenario

class LLMService:
    def __init__(self):
        self.model = "your-preferred-model"  # e.g., "gpt-4", "gemini/gemini-pro", etc.
        # Configure scenario library
        scenario.configure(
            default_model=self.model,
            api_key=os.getenv('LANGWATCH_API_KEY')
        )
    
    @scenario.cache()
    def analyze_content(self, content: str, analysis_type: str) -> dict:
        # Use scenario caching for LLM calls
        response = completion(
            model=self.model,
            messages=[
                {"role": "system", "content": "You are an intelligent assistant..."},
                {"role": "user", "content": f"Analyze: {content}"}
            ],
            api_key=os.getenv('YOUR_LLM_API_KEY')
        )
        
        return response.choices[0].message.content
```

### Generic Feature Implementation Example:

#### Domain Layer:
```python
# entities/data_item.py
@dataclass
class DataItem:
    id: str
    content: str
    item_type: str
    metadata: Dict[str, Any]
    
    def is_valid_format(self) -> bool:
        return self.item_type in ['text', 'json', 'csv', 'xml']  # Adjust for your domain

# repositories/data_repository.py
from abc import ABC, abstractmethod

class DataRepository(ABC):
    @abstractmethod
    def save(self, item: DataItem) -> str:
        pass
    
    @abstractmethod
    def get_by_id(self, item_id: str) -> Optional[DataItem]:
        pass
```

#### Application Layer:
```python
# use_cases/process_data.py
class ProcessDataUseCase:
    def __init__(self, 
                 data_repo: DataRepository,
                 llm_service: LLMService,
                 processor: DataProcessor):
        self._data_repo = data_repo
        self._llm_service = llm_service
        self._processor = processor
    
    def execute(self, data_id: str, processing_rules: List[str]) -> ProcessingResult:
        data_item = self._data_repo.get_by_id(data_id)
        if not data_item:
            raise DataItemNotFoundError(data_id)
        
        # Use LLM for analysis if needed
        analysis = self._llm_service.analyze_content(
            data_item.content, 
            processing_rules
        )
        
        # Apply business rules
        result = self._processor.process(data_item, analysis)
        
        return result
```

#### Infrastructure Layer:
```python
# adapters/data_processor.py
import json
from typing import Protocol

class DataProcessor(Protocol):
    def process_data(self, file_path: str) -> str: ...

class JSONProcessor:
    def process_data(self, file_path: str) -> dict:
        with open(file_path, 'r') as file:
            data = json.load(file)
        return data

class TextProcessor:
    def process_data(self, file_path: str) -> str:
        with open(file_path, 'r') as file:
            content = file.read()
        return content

# clients/llm_client.py
class LiteLLMClient:
    def __init__(self):
        self.model = "your-preferred-model"  # Configure for your LLM provider
        self.api_key = os.getenv('YOUR_LLM_API_KEY')
        # Configure scenario library
        scenario.configure(
            default_model=self.model,
            api_key=os.getenv('LANGWATCH_API_KEY')
        )
    
    @scenario.cache()
    def complete(self, messages: List[Dict]) -> str:
        response = completion(
            model=self.model,
            messages=messages,
            api_key=self.api_key
        )
        
        return response.choices[0].message.content
```

### Testing Implementation:

#### Unit Tests:
```python
# tests/unit/test_analyze_document.py
import pytest
from unittest.mock import Mock

class TestAnalyzeDocumentUseCase:
    def setup_method(self):
        self.document_repo = Mock(spec=DocumentRepository)
        self.llm_service = Mock(spec=LLMService)
        self.validator = Mock(spec=DocumentValidator)
        self.use_case = AnalyzeDocumentUseCase(
            self.document_repo,
            self.llm_service,
            self.validator
        )
    
    def test_analyze_valid_document(self):
        # Given
        document = Document("1", "content", "pdf", {})
        self.document_repo.get_by_id.return_value = document
        self.llm_service.analyze_document.return_value = "analysis"
        
        # When
        result = self.use_case.execute("1", ["rule1"])
        
        # Then
        assert result is not None
        self.llm_service.analyze_document.assert_called_once()
```

#### Integration Tests:
```python
# tests/integration/test_document_analysis.py
import pytest
import scenario

class TestDocumentAnalysisIntegration:
    def setup_method(self):
        # Configure LangWatch Scenario for testing
        scenario.configure(
            default_model="openai/gpt-4",
            api_key=os.getenv('LANGWATCH_API_KEY')
        )
    
    @pytest.mark.agent_test
    @pytest.mark.asyncio
    async def test_end_to_end_data_processing(self):
        # Test actual LLM calls with scenario monitoring
        
        class DataProcessingAgent(scenario.AgentAdapter):
            async def call(self, input: scenario.AgentInput) -> scenario.AgentReturnTypes:
                return data_processing_agent(input.messages)
        
        result = await scenario.run(
            name="integration data processing",
            description="""
                Test complete data processing workflow with
                business logic validation and quality checks.
            """,
            agents=[
                DataProcessingAgent(),
                scenario.UserSimulatorAgent(),
                scenario.JudgeAgent(criteria=[
                    "Agent should process data according to business rules",
                    "Agent should validate data quality",
                    "Agent should provide structured output",
                    "Agent should handle edge cases gracefully",
                ])
            ],
        )
        
        assert result.success

@scenario.cache()
def data_processing_agent(messages) -> scenario.AgentReturnTypes:
    response = completion(
        model="openai/gpt-4",
        messages=[
            {
                "role": "system", 
                "content": "You are a data processing agent that validates and processes business data."
            },
            *messages,
        ],
    )
    return response.choices[0].message
```

### Progress Tracking:
After implementation, update progress files:
```json
{
  "feature_id": "feature_001",
  "status": "implemented",
  "completion_percentage": 100,
  "last_updated": "2024-07-11T10:30:00Z",
  "next_steps": ["Deploy to staging", "Performance testing"],
  "tests_passing": true,
  "scenario_testing_enabled": true
}
```

### Example Implementation Commands:
```bash
# Implement specific feature
claude-code feature-developer.md --feature=feature_001

# Implement with specific architecture
claude-code feature-developer.md --feature=feature_001 --architecture=clean

# Update existing implementation
claude-code feature-developer.md --feature=feature_001 --update

# Include LangWatch Scenario testing setup
claude-code feature-developer.md --feature=feature_001 --scenario-testing
```

### Quality Checklist:
- [ ] SOLID principles followed
- [ ] All dependencies injected
- [ ] Comprehensive error handling
- [ ] LLM calls use litellm
- [ ] LangWatch Scenario testing enabled
- [ ] Tests cover edge cases
- [ ] Documentation updated
- [ ] Progress tracking updated