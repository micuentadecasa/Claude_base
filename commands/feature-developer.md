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
from langwatch_scenario import Scenario

class LLMService:
    def __init__(self):
        self.model = "your-preferred-model"  # e.g., "gpt-4", "gemini/gemini-pro", etc.
        self.scenario = Scenario(
            api_key=os.getenv('LANGWATCH_API_KEY'),
            project_id="your-project-name"
        )
    
    def analyze_content(self, content: str, analysis_type: str) -> dict:
        # Use scenario testing for LLM calls
        with self.scenario.trace_call("content_analysis") as trace:
            trace.input(content=content, analysis_type=analysis_type)
            
            response = completion(
                model=self.model,
                messages=[
                    {"role": "system", "content": "You are an intelligent assistant..."},
                    {"role": "user", "content": f"Analyze: {content}"}
                ],
                api_key=os.getenv('YOUR_LLM_API_KEY')
            )
            
            result = response.choices[0].message.content
            trace.output(result=result)
            return result
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
        self.scenario = Scenario(
            api_key=os.getenv('LANGWATCH_API_KEY'),
            project_id="your-project-name"
        )
    
    def complete(self, messages: List[Dict]) -> str:
        with self.scenario.trace_call("llm_completion") as trace:
            trace.input(messages=messages, model=self.model)
            
            response = completion(
                model=self.model,
                messages=messages,
                api_key=self.api_key
            )
            
            result = response.choices[0].message.content
            trace.output(result=result)
            return result
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
from langwatch_scenario import Scenario

class TestDocumentAnalysisIntegration:
    def setup_method(self):
        # Initialize LangWatch Scenario for testing
        self.scenario = Scenario(
            api_key=os.getenv('LANGWATCH_API_KEY'),
            project_id="your-project-integration-tests",
            environment="test"
        )
    
    def test_end_to_end_data_processing(self):
        # Test actual LLM calls with scenario monitoring
        with self.scenario.test_case("integration_data_processing") as test:
            test.input(
                data_content="Sample data for processing",
                processing_type="your_processing_type"
            )
            
            # Test actual implementation with scenario tracing
            result = self.data_processor.process(
                test.input.data_content,
                test.input.processing_type
            )
            
            test.expect(result).to_have_expected_structure()
            test.expect(result).to_meet_business_requirements()
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