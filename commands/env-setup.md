# Intelligent Environment Setup

## Purpose
Sets up complete development environment with progress tracking and parallel agent support.

## Usage
```bash
claude-code env-setup.md
```

## Instructions

You are an Intelligent Environment Setup AI that creates development environments with built-in progress tracking and parallel execution support.

### Core Responsibilities:
1. **Check if already setup** - Skip if environment exists
2. **Create project structure** in src/ directory
3. **Initialize progress tracking** system
4. **Setup Python environment** with proper dependencies
5. **Generate configuration files** for development
6. **Create agent workspace directories** for parallel execution
7. **Update progress state** to mark environment as ready

### Environment Structure Created:
```
src/
├── .progress.json          # Central progress tracking
├── .parallel_matrix.json   # Parallel execution safety matrix
├── project-name/           # Main project (from PRD analysis)
│   ├── .env               # Environment variables
│   ├── .gitignore         # Git ignore patterns
│   ├── Makefile           # Build automation
│   ├── requirements.txt   # Python dependencies
│   ├── pyproject.toml     # Python project config
│   ├── backend/           # Python backend
│   │   ├── venv/         # Virtual environment
│   │   ├── app/          # Application code (SOLID structure)
│   │   ├── tests/        # Test files
│   │   └── logs/         # Application logs
│   └── frontend/         # React frontend (if needed)
├── datasets/              # Test datasets (created by planning)
├── steps/                 # Implementation steps (created by planning)
├── .agents/               # Temporary agent workspaces
│   ├── agent_1/          # Implementation agent 1
│   └── agent_2/          # Implementation agent 2
└── .planners/             # Temporary planner workspaces
    ├── planner_1/         # Planning agent 1
    └── planner_2/         # Planning agent 2
```

## Smart Environment Setup Process:

### Step 1: Environment Check
```python
def check_environment_status():
    """Check if environment is already set up"""
    
    if os.path.exists("src/.progress.json"):
        progress = load_json("src/.progress.json")
        if progress.get("environment_setup", False):
            print("✅ Environment already set up. Ready for feature planning.")
            display_current_status(progress)
            return True
    
    print("🚀 Setting up development environment...")
    return False

def display_current_status(progress):
    """Show current project status"""
    
    total_features = len(progress.get("features", {}))
    planned_features = sum(1 for f in progress["features"].values() if f.get("planned", False))
    
    if planned_features == 0:
        print("📋 Next step: Run claude-code feature-plan.md")
    elif planned_features < total_features:
        print(f"📋 Planning progress: {planned_features}/{total_features} features planned")
        print("📋 Next step: Run claude-code feature-plan.md")
    else:
        print(f"📋 All {total_features} features planned")
        print("📋 Next step: Run claude-code feature-implement.md")
```

### Step 2: Project Structure Creation
```python
def create_project_structure():
    """Create complete project structure"""
    
    # Determine project name from PRD
    project_name = extract_project_name_from_prd()
    
    # Create main directories
    directories = [
        f"src/{project_name}/backend/app",
        f"src/{project_name}/backend/tests", 
        f"src/{project_name}/backend/logs",
        f"src/{project_name}/frontend",
        "src/.agents/agent_1",
        "src/.agents/agent_2", 
        "src/.planners/planner_1",
        "src/.planners/planner_2",
        "src/datasets",
        "src/steps"
    ]
    
    for directory in directories:
        os.makedirs(directory, exist_ok=True)
    
    return project_name
```

### Step 3: Progress Tracking Initialization
```python
def initialize_progress_tracking(project_name):
    """Create progress tracking system"""
    
    # Extract features from PRD
    features = extract_features_from_prd()
    
    # Initialize progress state
    progress_state = {
        "project_name": project_name,
        "project_status": "environment_setup",
        "prd_enhanced": True,  # Assume PRD exists if env-setup is run
        "environment_setup": True,
        "total_features": len(features),
        "features": {},
        "parallel_capacity": 2,
        "active_agents": [],
        "last_updated": datetime.now().isoformat()
    }
    
    # Initialize feature tracking
    for feature_id, feature_name in features.items():
        progress_state["features"][feature_id] = {
            "name": feature_name,
            "planned": False,
            "datasets_generated": False,
            "steps_created": False,
            "steps": {},
            "implementation_started": False,
            "implementation_completed": False,
            "tests_passing": False
        }
    
    # Save progress state
    save_json("src/.progress.json", progress_state)
    
    # Initialize parallel execution matrix
    parallel_matrix = {
        "version": "1.0",
        "features": list(features.keys()),
        "parallel_safe_features": [],  # Will be populated during planning
        "parallel_safe_steps": [],     # Will be populated during planning
        "conflicts": [],
        "dependencies": {}
    }
    
    save_json("src/.parallel_matrix.json", parallel_matrix)
    
    return progress_state
```

### Step 4: Project Configuration Generation

**CRITICAL**: If your project includes conversational chat functionality, configure additional LangGraph environment:

### Required Environment Variables for Chat Interfaces
```bash
# Create .env file with required API keys
cat > .env << 'EOF'
# LLM APIs
GEMINI_API_KEY=your_gemini_api_key_here
LANGSMITH_API_KEY=your_langsmith_key_here  # Optional for tracing
OPENAI_API_KEY=your_openai_key_here        # Required for LangWatch user simulation

# LangWatch (for scenario testing)
LANGWATCH_API_KEY=your_langwatch_api_key_here
EOF
```

### LangGraph Development Commands Setup
```bash
# Add to Makefile for conversational agents
cat >> Makefile << 'EOF'

# LangGraph Development Commands
.PHONY: gen dev-backend-gen install-gen test-gen

# Start frontend + generated backend (USE FOR TESTING)
gen:
	@echo "🚀 Starting frontend and generated backend..."
	make install-gen
	cd backend_gen && langgraph dev &
	cd frontend && npm run dev

# Generated backend only
dev-backend-gen:
	@echo "🤖 Starting generated LangGraph backend..."
	cd backend_gen && pip install -e . && langgraph dev

# Install generated backend dependencies
install-gen:
	@echo "📦 Installing generated backend dependencies..."
	cd backend_gen && pip install -e .

# Test generated agents
test-gen:
	@echo "🧪 Testing generated agents..."
	cd backend_gen && python -m pytest tests/ -v
EOF
```

### LangGraph Agent Dependencies
```bash
# Add to requirements.txt for conversational interfaces
cat >> requirements.txt << 'EOF'

# LangGraph and Agent Dependencies
langgraph>=0.2.0
langchain-google-genai>=2.0.0
langchain-core>=0.3.0
langsmith>=0.1.0

# Testing and Scenario Libraries
langwatch-scenario>=0.7.0
pytest>=8.0.0
pytest-asyncio>=0.21.0
pytest-mock>=3.10.0
EOF
```

### Step 1: Python Environment Setup
```bash
# Create and activate virtual environment
python3 -m venv backend/venv

# Activation scripts for different platforms
# Linux/Mac
echo 'source backend/venv/bin/activate' > activate.sh
chmod +x activate.sh

# Windows
echo 'backend\\venv\\Scripts\\activate.bat' > activate.bat

# Install base dependencies
backend/venv/bin/pip install --upgrade pip setuptools wheel
```

### Step 2: Python Dependencies Template
```python
# requirements.txt
# Core dependencies
fastapi>=0.104.0
uvicorn[standard]>=0.24.0
pydantic>=2.4.0
python-dotenv>=1.0.0

# LLM Integration
litellm>=1.17.0
langwatch>=0.7.0

# Document Processing
PyPDF2>=3.0.0
python-docx>=1.1.0
python-multipart>=0.0.6

# Database (if needed)
sqlalchemy>=2.0.0
alembic>=1.12.0

# Testing
pytest>=7.4.0
pytest-asyncio>=0.21.0
pytest-mock>=3.11.0
httpx>=0.25.0

# Development
ruff>=0.1.0
black>=23.0.0
mypy>=1.6.0

# Monitoring
prometheus-client>=0.18.0
structlog>=23.2.0
```

### Step 3: Python Project Configuration
```toml
# pyproject.toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "spanish-nes-analyzer"
version = "0.1.0"
description = "Spanish NES Document Analysis System"
authors = [{name = "Your Name", email = "your.email@example.com"}]
license = {text = "MIT"}
dependencies = [
    "fastapi>=0.104.0",
    "uvicorn[standard]>=0.24.0",
    "litellm>=1.17.0",
    "langwatch>=0.7.0",
    "PyPDF2>=3.0.0",
    "python-docx>=1.1.0",
    "python-dotenv>=1.0.0",
    "pydantic>=2.4.0",
]

[project.optional-dependencies]
test = [
    "pytest>=7.4.0",
    "pytest-asyncio>=0.21.0",
    "pytest-mock>=3.11.0",
    "httpx>=0.25.0",
    "langwatch-scenario>=0.7.0",
]
dev = [
    "ruff>=0.1.0",
    "black>=23.0.0",
    "mypy>=1.6.0",
]

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = "-v --tb=short"
asyncio_mode = "auto"

[tool.ruff]
line-length = 88
target-version = "py311"

[tool.black]
line-length = 88
target-version = ['py311']

[tool.mypy]
python_version = "3.11"
strict = true
```

### Step 4: Frontend Setup (React + Vite)
```bash
# Only if frontend is needed
cd frontend

# Initialize with Vite
npm create vite@latest . -- --template react-ts

# Install additional dependencies
npm install axios @types/axios
npm install @tanstack/react-query
npm install lucide-react
npm install tailwindcss postcss autoprefixer
npm install -D @types/node
```

### Step 5: Frontend Package.json Additions
```json
{
  "scripts": {
    "dev": "vite --host 0.0.0.0 --port 5173",
    "build": "tsc && vite build",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "preview": "vite preview --host 0.0.0.0 --port 5173",
    "test": "vitest",
    "type-check": "tsc --noEmit"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "axios": "^1.6.0",
    "@tanstack/react-query": "^5.8.0",
    "lucide-react": "^0.294.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.37",
    "@types/react-dom": "^18.2.15",
    "@typescript-eslint/eslint-plugin": "^6.10.0",
    "@typescript-eslint/parser": "^6.10.0",
    "@vitejs/plugin-react": "^4.1.0",
    "eslint": "^8.53.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.4",
    "typescript": "^5.2.2",
    "vite": "^5.0.0",
    "vitest": "^0.34.0",
    "tailwindcss": "^3.3.0",
    "postcss": "^8.4.0",
    "autoprefixer": "^10.4.0"
  }
}
```

### Step 6: Comprehensive Makefile
```makefile
# Makefile for Spanish NES Analyzer
.PHONY: help setup install dev dev-backend dev-frontend build test clean logs status

# Default Python and Node versions
PYTHON := python3
NODE := node
NPM := npm

# Directories
BACKEND_DIR := backend
FRONTEND_DIR := frontend
VENV_DIR := $(BACKEND_DIR)/venv
LOGS_DIR := logs

# Colors for output
RED := \033[0;31m
GREEN := \033[0;32m
YELLOW := \033[1;33m
BLUE := \033[0;34m
NC := \033[0m # No Color

help: ## Show this help message
	@echo "$(BLUE)Spanish NES Analyzer - Development Commands$(NC)"
	@echo ""
	@echo "$(GREEN)Environment Setup:$(NC)"
	@echo "  setup          Create complete development environment"
	@echo "  install        Install all dependencies"
	@echo ""
	@echo "$(GREEN)Development:$(NC)"
	@echo "  dev            Start both backend and frontend"
	@echo "  dev-backend    Start only backend server"
	@echo "  dev-frontend   Start only frontend server"
	@echo "  dev-full       Start with hot reloading and monitoring"
	@echo ""
	@echo "$(GREEN)Building:$(NC)"
	@echo "  build          Build both backend and frontend"
	@echo "  build-backend  Build backend only"
	@echo "  build-frontend Build frontend only"
	@echo ""
	@echo "$(GREEN)Testing:$(NC)"
	@echo "  test           Run all tests"
	@echo "  test-backend   Run backend tests"
	@echo "  test-frontend  Run frontend tests"
	@echo "  test-llm       Run LLM integration tests"
	@echo "  test-scenarios Run LangWatch scenario tests"
	@echo ""
	@echo "$(GREEN)Monitoring:$(NC)"
	@echo "  logs           Show all logs in real-time"
	@echo "  logs-backend   Show backend logs"
	@echo "  logs-frontend  Show frontend logs"
	@echo "  status         Show service status"
	@echo "  health         Check system health"
	@echo ""
	@echo "$(GREEN)Maintenance:$(NC)"
	@echo "  clean          Clean build artifacts"
	@echo "  reset          Reset entire environment"
	@echo "  format         Format all code"
	@echo "  lint           Run linting"

setup: ## Create complete development environment
	@echo "$(BLUE)Setting up development environment...$(NC)"
	@mkdir -p $(LOGS_DIR) features datasets progress context commands
	@echo "$(GREEN)✓ Created project directories$(NC)"
	
	# Python environment
	@if [ ! -d "$(VENV_DIR)" ]; then \
		echo "$(YELLOW)Creating Python virtual environment...$(NC)"; \
		$(PYTHON) -m venv $(VENV_DIR); \
		echo "$(GREEN)✓ Virtual environment created$(NC)"; \
	fi
	
	# Check if frontend is needed
	@if [ -f "frontend/package.json" ] || [ -d "frontend/src" ]; then \
		echo "$(YELLOW)Frontend detected, checking Node.js...$(NC)"; \
		if ! command -v $(NODE) > /dev/null 2>&1; then \
			echo "$(RED)✗ Node.js not found. Please install Node.js 18+$(NC)"; \
			exit 1; \
		fi; \
		echo "$(GREEN)✓ Node.js available$(NC)"; \
	fi
	
	@echo "$(GREEN)✓ Environment setup complete$(NC)"
	@echo "$(BLUE)Run 'make install' to install dependencies$(NC)"

install: ## Install all dependencies
	@echo "$(BLUE)Installing dependencies...$(NC)"
	
	# Backend dependencies
	@echo "$(YELLOW)Installing Python dependencies...$(NC)"
	@$(VENV_DIR)/bin/pip install --upgrade pip setuptools wheel
	@$(VENV_DIR)/bin/pip install -r requirements.txt
	@if [ -f "pyproject.toml" ]; then \
		$(VENV_DIR)/bin/pip install -e .[test,dev]; \
	fi
	@echo "$(GREEN)✓ Python dependencies installed$(NC)"
	
	# Frontend dependencies (if exists)
	@if [ -f "$(FRONTEND_DIR)/package.json" ]; then \
		echo "$(YELLOW)Installing Node.js dependencies...$(NC)"; \
		cd $(FRONTEND_DIR) && $(NPM) install; \
		echo "$(GREEN)✓ Node.js dependencies installed$(NC)"; \
	fi
	
	@echo "$(GREEN)✓ All dependencies installed$(NC)"

dev: ## Start both backend and frontend in development mode
	@echo "$(BLUE)Starting development servers...$(NC)"
	@mkdir -p $(LOGS_DIR)
	@$(MAKE) --no-print-directory _start-backend &
	@if [ -f "$(FRONTEND_DIR)/package.json" ]; then \
		$(MAKE) --no-print-directory _start-frontend & \
	fi
	@echo "$(GREEN)✓ Development servers starting...$(NC)"
	@echo "$(BLUE)Backend: http://localhost:8000$(NC)"
	@if [ -f "$(FRONTEND_DIR)/package.json" ]; then \
		echo "$(BLUE)Frontend: http://localhost:5173$(NC)"; \
	fi
	@echo "$(YELLOW)Press Ctrl+C to stop all services$(NC)"
	@trap 'make _stop-all' INT; sleep infinity

_start-backend:
	@echo "$(YELLOW)Starting backend server...$(NC)"
	@cd $(BACKEND_DIR) && \
	source venv/bin/activate && \
	uvicorn src.main:app --host 0.0.0.0 --port 8000 --reload \
		--log-level info --access-log \
		> ../$(LOGS_DIR)/backend.log 2>&1 &
	@echo $$! > $(LOGS_DIR)/backend.pid

_start-frontend:
	@echo "$(YELLOW)Starting frontend server...$(NC)"
	@cd $(FRONTEND_DIR) && \
	$(NPM) run dev > ../$(LOGS_DIR)/frontend.log 2>&1 &
	@echo $$! > $(LOGS_DIR)/frontend.pid

dev-backend: ## Start only backend server
	@echo "$(BLUE)Starting backend server...$(NC)"
	@mkdir -p $(LOGS_DIR)
	@cd $(BACKEND_DIR) && \
	source venv/bin/activate && \
	uvicorn src.main:app --host 0.0.0.0 --port 8000 --reload \
		--log-level info --access-log
	@echo "$(GREEN)Backend running at http://localhost:8000$(NC)"

dev-frontend: ## Start only frontend server
	@if [ ! -f "$(FRONTEND_DIR)/package.json" ]; then \
		echo "$(RED)✗ Frontend not found$(NC)"; \
		exit 1; \
	fi
	@echo "$(BLUE)Starting frontend server...$(NC)"
	@cd $(FRONTEND_DIR) && $(NPM) run dev
	@echo "$(GREEN)Frontend running at http://localhost:5173$(NC)"

dev-full: ## Start with hot reloading and monitoring
	@echo "$(BLUE)Starting full development environment...$(NC)"
	@mkdir -p $(LOGS_DIR)
	@$(MAKE) --no-print-directory dev &
	@sleep 5
	@$(MAKE) --no-print-directory logs

build: build-backend build-frontend ## Build both backend and frontend

build-backend: ## Build backend
	@echo "$(BLUE)Building backend...$(NC)"
	@cd $(BACKEND_DIR) && \
	source venv/bin/activate && \
	python -m pytest tests/ --tb=short && \
	python -m ruff check src/ && \
	python -m black --check src/
	@echo "$(GREEN)✓ Backend build complete$(NC)"

build-frontend: ## Build frontend
	@if [ ! -f "$(FRONTEND_DIR)/package.json" ]; then \
		echo "$(YELLOW)⚠ Frontend not found, skipping$(NC)"; \
		exit 0; \
	fi
	@echo "$(BLUE)Building frontend...$(NC)"
	@cd $(FRONTEND_DIR) && \
	$(NPM) run type-check && \
	$(NPM) run lint && \
	$(NPM) run build
	@echo "$(GREEN)✓ Frontend build complete$(NC)"

test: test-backend test-frontend ## Run all tests

test-backend: ## Run backend tests
	@echo "$(BLUE)Running backend tests...$(NC)"
	@cd $(BACKEND_DIR) && \
	source venv/bin/activate && \
	python -m pytest tests/ -v --tb=short --cov=src --cov-report=term-missing
	@echo "$(GREEN)✓ Backend tests complete$(NC)"

test-frontend: ## Run frontend tests
	@if [ ! -f "$(FRONTEND_DIR)/package.json" ]; then \
		echo "$(YELLOW)⚠ Frontend not found, skipping$(NC)"; \
		exit 0; \
	fi
	@echo "$(BLUE)Running frontend tests...$(NC)"
	@cd $(FRONTEND_DIR) && $(NPM) run test
	@echo "$(GREEN)✓ Frontend tests complete$(NC)"

test-llm: ## Run LLM integration tests
	@echo "$(BLUE)Running LLM integration tests...$(NC)"
	@cd $(BACKEND_DIR) && \
	source venv/bin/activate && \
	python -m pytest tests/ -k "llm" -v --tb=short
	@echo "$(GREEN)✓ LLM tests complete$(NC)"

test-scenarios: ## Run LangWatch scenario tests
	@echo "$(BLUE)Running LangWatch scenario tests...$(NC)"
	@cd $(BACKEND_DIR) && \
	source venv/bin/activate && \
	python -m pytest tests/scenarios/ -v --tb=short
	@echo "$(GREEN)✓ Scenario tests complete$(NC)"

logs: ## Show all logs in real-time
	@echo "$(BLUE)Showing all logs (Ctrl+C to exit)...$(NC)"
	@if [ -f "$(LOGS_DIR)/backend.log" ] && [ -f "$(LOGS_DIR)/frontend.log" ]; then \
		tail -f $(LOGS_DIR)/backend.log $(LOGS_DIR)/frontend.log; \
	elif [ -f "$(LOGS_DIR)/backend.log" ]; then \
		tail -f $(LOGS_DIR)/backend.log; \
	else \
		echo "$(YELLOW)⚠ No log files found. Start services first.$(NC)"; \
	fi

logs-backend: ## Show backend logs
	@if [ -f "$(LOGS_DIR)/backend.log" ]; then \
		tail -f $(LOGS_DIR)/backend.log; \
	else \
		echo "$(YELLOW)⚠ Backend log not found$(NC)"; \
	fi

logs-frontend: ## Show frontend logs
	@if [ -f "$(LOGS_DIR)/frontend.log" ]; then \
		tail -f $(LOGS_DIR)/frontend.log; \
	else \
		echo "$(YELLOW)⚠ Frontend log not found$(NC)"; \
	fi

status: ## Show service status
	@echo "$(BLUE)Service Status:$(NC)"
	@if [ -f "$(LOGS_DIR)/backend.pid" ]; then \
		PID=$$(cat $(LOGS_DIR)/backend.pid); \
		if ps -p $$PID > /dev/null 2>&1; then \
			echo "$(GREEN)✓ Backend: Running (PID: $$PID)$(NC)"; \
		else \
			echo "$(RED)✗ Backend: Not running$(NC)"; \
		fi; \
	else \
		echo "$(RED)✗ Backend: Not running$(NC)"; \
	fi
	
	@if [ -f "$(LOGS_DIR)/frontend.pid" ]; then \
		PID=$$(cat $(LOGS_DIR)/frontend.pid); \
		if ps -p $$PID > /dev/null 2>&1; then \
			echo "$(GREEN)✓ Frontend: Running (PID: $$PID)$(NC)"; \
		else \
			echo "$(RED)✗ Frontend: Not running$(NC)"; \
		fi; \
	else \
		echo "$(RED)✗ Frontend: Not running$(NC)"; \
	fi

health: ## Check system health
	@echo "$(BLUE)System Health Check:$(NC)"
	@echo -n "Python: "
	@if command -v $(PYTHON) > /dev/null 2>&1; then \
		echo "$(GREEN)✓ $$($(PYTHON) --version)$(NC)"; \
	else \
		echo "$(RED)✗ Not found$(NC)"; \
	fi
	
	@echo -n "Virtual Environment: "
	@if [ -d "$(VENV_DIR)" ]; then \
		echo "$(GREEN)✓ Active$(NC)"; \
	else \
		echo "$(RED)✗ Not found$(NC)"; \
	fi
	
	@echo -n "Node.js: "
	@if command -v $(NODE) > /dev/null 2>&1; then \
		echo "$(GREEN)✓ $$($(NODE) --version)$(NC)"; \
	else \
		echo "$(YELLOW)⚠ Not found (frontend disabled)$(NC)"; \
	fi
	
	@echo -n "Environment Variables: "
	@if [ -f ".env" ]; then \
		if grep -q "GEMINI_API_KEY" .env; then \
			echo "$(GREEN)✓ Configured$(NC)"; \
		else \
			echo "$(YELLOW)⚠ Missing GEMINI_API_KEY$(NC)"; \
		fi; \
	else \
		echo "$(RED)✗ .env file missing$(NC)"; \
	fi

clean: ## Clean build artifacts
	@echo "$(BLUE)Cleaning build artifacts...$(NC)"
	@find . -type d -name "__pycache__" -exec rm -rf {} + 2>/dev/null || true
	@find . -type f -name "*.pyc" -delete 2>/dev/null || true
	@find . -type d -name ".pytest_cache" -exec rm -rf {} + 2>/dev/null || true
	@find . -type d -name "*.egg-info" -exec rm -rf {} + 2>/dev/null || true
	@if [ -d "$(FRONTEND_DIR)/dist" ]; then rm -rf $(FRONTEND_DIR)/dist; fi
	@if [ -d "$(FRONTEND_DIR)/node_modules/.cache" ]; then rm -rf $(FRONTEND_DIR)/node_modules/.cache; fi
	@echo "$(GREEN)✓ Clean complete$(NC)"

reset: ## Reset entire environment
	@echo "$(RED)⚠ This will delete the virtual environment and node_modules$(NC)"
	@read -p "Are you sure? (y/N): " confirm && [ "$$confirm" = "y" ]
	@$(MAKE) --no-print-directory _stop-all
	@rm -rf $(VENV_DIR)
	@if [ -d "$(FRONTEND_DIR)/node_modules" ]; then rm -rf $(FRONTEND_DIR)/node_modules; fi
	@rm -rf $(LOGS_DIR)
	@echo "$(GREEN)✓ Environment reset$(NC)"
	@echo "$(BLUE)Run 'make setup && make install' to rebuild$(NC)"

format: ## Format all code
	@echo "$(BLUE)Formatting code...$(NC)"
	@cd $(BACKEND_DIR) && \
	source venv/bin/activate && \
	python -m black src/ tests/ && \
	python -m ruff --fix src/ tests/
	
	@if [ -f "$(FRONTEND_DIR)/package.json" ]; then \
		echo "$(YELLOW)Formatting frontend...$(NC)"; \
		cd $(FRONTEND_DIR) && $(NPM) run lint -- --fix; \
	fi
	@echo "$(GREEN)✓ Formatting complete$(NC)"

lint: ## Run linting
	@echo "$(BLUE)Running linting...$(NC)"
	@cd $(BACKEND_DIR) && \
	source venv/bin/activate && \
	python -m ruff check src/ tests/ && \
	python -m mypy src/
	
	@if [ -f "$(FRONTEND_DIR)/package.json" ]; then \
		echo "$(YELLOW)Linting frontend...$(NC)"; \
		cd $(FRONTEND_DIR) && $(NPM) run lint; \
	fi
	@echo "$(GREEN)✓ Linting complete$(NC)"

_stop-all: ## Internal: Stop all services
	@echo "$(YELLOW)Stopping all services...$(NC)"
	@if [ -f "$(LOGS_DIR)/backend.pid" ]; then \
		kill $$(cat $(LOGS_DIR)/backend.pid) 2>/dev/null || true; \
		rm -f $(LOGS_DIR)/backend.pid; \
	fi
	@if [ -f "$(LOGS_DIR)/frontend.pid" ]; then \
		kill $$(cat $(LOGS_DIR)/frontend.pid) 2>/dev/null || true; \
		rm -f $(LOGS_DIR)/frontend.pid; \
	fi
	@echo "$(GREEN)✓ All services stopped$(NC)"

# Development shortcuts
run: dev ## Alias for dev
start: dev ## Alias for dev
stop: _stop-all ## Stop all services
restart: _stop-all dev ## Restart all services
```

### Step 7: Environment Configuration Files

#### .env Template:
```bash
# API Keys (REQUIRED)
GEMINI_API_KEY=your_gemini_api_key_here
LANGWATCH_API_KEY=your_langwatch_key_here
OPENAI_API_KEY=your_openai_key_here

# Backend Configuration
BACKEND_HOST=0.0.0.0
BACKEND_PORT=8000
DEBUG=true

# Frontend Configuration (if applicable)
FRONTEND_HOST=0.0.0.0
FRONTEND_PORT=5173
API_BASE_URL=http://localhost:8000

# Database (if needed)
DATABASE_URL=sqlite:///./app.db

# Logging
LOG_LEVEL=INFO
LOG_FORMAT=json

# Testing
TEST_ENV=true
LANGWATCH_ENV=test
```

#### .gitignore Template:
```bash
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# Virtual Environment
venv/
env/
ENV/

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Testing
.pytest_cache/
.coverage
htmlcov/
.tox/
.cache

# Node.js (if frontend exists)
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.pnpm-debug.log*

# Frontend build
frontend/dist/
frontend/build/

# Logs
logs/
*.log

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Project specific
progress/
context/conversations/
datasets/generated/
```

## Quick Start Commands:

```bash
# Complete environment setup
claude-code environment-setup.md --project-name=spanish-nes-analyzer

# This creates everything and runs:
make setup      # Create environments
make install    # Install dependencies  
make dev        # Start development servers
make logs       # Monitor in real-time
```

## Advanced Monitoring Commands:

```bash
# Real-time log monitoring with filtering
make logs | grep ERROR              # Show only errors
make logs-backend | grep "NES"      # Filter backend logs
make logs-frontend | grep "axios"   # Filter frontend logs

# Health monitoring
make health     # Check system status
make status     # Check running services

# Development workflow
make test && make lint && make dev  # Test, lint, then run
make restart                        # Stop and restart all
```

This environment setup ensures robust development with proper isolation, monitoring, and automation for your Spanish NES document analysis system!