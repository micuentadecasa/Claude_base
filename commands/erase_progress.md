# Project Reset Script

## Purpose
Resets the project to a clean state for starting a new project or clearing all progress.

## Usage
```bash
# Remove only project artifacts (keep src/ code)
claude-code erase_progress.md

# Remove everything including generated source code
claude-code erase_progress.md --include-src
```

## Instructions

You are a Project Reset AI that cleans up project artifacts and optionally source code to prepare for a fresh start.

### Core Responsibilities:
1. **Remove project artifacts** (datasets, features, progress)
2. **Optionally remove source code** if --include-src flag is provided
3. **Preserve template structure** and commands
4. **Clean environment safely** without breaking the template

### Default Cleanup (Standard Reset):
```python
import os
import shutil
from pathlib import Path

def standard_reset():
    """Clear project artifacts but keep source code and folder structure"""
    
    directories_to_clear = [
        "datasets/",
        "features/", 
        "progress/",
        "context/",
        "tests/"
    ]
    
    files_to_remove = [
        "prd.md",  # Keep as backup, but remove current one
        ".env.example"  # Remove if created
    ]
    
    print("🧹 Starting standard project reset...")
    
    for directory in directories_to_clear:
        if os.path.exists(directory):
            # Clear contents but keep the directory
            for item in Path(directory).iterdir():
                if item.is_file():
                    item.unlink()
                    print(f"🗑️  Removed file: {item}")
                elif item.is_dir():
                    shutil.rmtree(item)
                    print(f"🗑️  Removed directory: {item}")
            
            # Add empty.txt to maintain structure
            empty_file = Path(directory) / "empty.txt"
            empty_file.write_text(f"# This file maintains the {directory} directory structure\n# Files will be generated here by the appropriate commands")
            print(f"✅ Cleared contents: {directory}")
        else:
            # Create directory if it doesn't exist
            os.makedirs(directory, exist_ok=True)
            empty_file = Path(directory) / "empty.txt"
            empty_file.write_text(f"# This file maintains the {directory} directory structure\n# Files will be generated here by the appropriate commands")
            print(f"✅ Created: {directory}")
    
    for file_path in files_to_remove:
        if os.path.exists(file_path):
            os.remove(file_path)
            print(f"✅ Removed: {file_path}")
    
    # Clean up any generated test files
    cleanup_test_artifacts()
    
    print("✨ Standard reset complete!")
    print("📝 Ready for new project - update your PRD and run c01-feature-creator.md")

def cleanup_test_artifacts():
    """Remove any generated test artifacts"""
    
    test_patterns = [
        "test_*.py",
        "*_test.py", 
        "scenario_*.json",
        "langwatch_*.json",
        "__pycache__/",
        ".pytest_cache/",
        ".coverage",
        "htmlcov/"
    ]
    
    for pattern in test_patterns:
        for path in Path(".").rglob(pattern):
            if path.is_file():
                path.unlink()
                print(f"🗑️  Removed test file: {path}")
            elif path.is_dir():
                shutil.rmtree(path)
                print(f"🗑️  Removed test directory: {path}")
```

### Full Reset (Include Source Code):
```python
def full_reset():
    """Remove everything including generated source code"""
    
    print("🔥 Starting full project reset (including source code)...")
    
    # First do standard reset
    standard_reset()
    
    # Additional directories for full reset
    additional_directories = [
        "src/",
        "docs/",
        "scripts/",
        "config/"
    ]
    
    additional_files = [
        "requirements.txt",
        "setup.py",
        "pyproject.toml",
        "Dockerfile",
        "docker-compose.yml",
        ".env",
        "README.md"  # Remove if generated
    ]
    
    for directory in additional_directories:
        if os.path.exists(directory):
            shutil.rmtree(directory)
            print(f"🔥 Removed: {directory}")
    
    for file_path in additional_files:
        if os.path.exists(file_path):
            os.remove(file_path)
            print(f"🔥 Removed: {file_path}")
    
    # Clean up any remaining artifacts
    cleanup_all_artifacts()
    
    print("💥 Full reset complete!")
    print("🆕 Project is now completely clean - ready for new domain/project")

def cleanup_all_artifacts():
    """Remove any remaining project artifacts"""
    
    # Remove any IDE/editor files
    ide_patterns = [
        ".vscode/",
        ".idea/",
        "*.swp",
        "*.swo",
        ".DS_Store"
    ]
    
    # Remove any build artifacts
    build_patterns = [
        "build/",
        "dist/",
        "*.egg-info/",
        "node_modules/",
        "package-lock.json",
        "yarn.lock"
    ]
    
    all_patterns = ide_patterns + build_patterns
    
    for pattern in all_patterns:
        for path in Path(".").rglob(pattern):
            if path.is_file():
                path.unlink()
                print(f"🧹 Cleaned: {path}")
            elif path.is_dir():
                shutil.rmtree(path)
                print(f"🧹 Cleaned: {path}")
```

### Safe Reset Implementation:
```python
import argparse
import sys
from pathlib import Path

def main():
    """Main reset function with safety checks"""
    
    parser = argparse.ArgumentParser(description='Reset project to clean state')
    parser.add_argument('--include-src', action='store_true', 
                       help='Also remove generated source code')
    parser.add_argument('--confirm', action='store_true',
                       help='Skip confirmation prompt')
    
    args = parser.parse_args()
    
    # Safety check - ensure we're in the right directory
    if not Path("commands").exists() or not Path("CLAUDE.md").exists():
        print("❌ Error: This doesn't appear to be a Claude Code template directory")
        print("   Make sure you're in the root of your Claude Code project")
        sys.exit(1)
    
    # Confirmation prompt
    if not args.confirm:
        if args.include_src:
            print("⚠️  WARNING: Full reset will remove ALL generated code and artifacts!")
            print("   This includes:")
            print("   - All source code in src/")
            print("   - All datasets and features")
            print("   - All progress tracking")
            print("   - All test files")
            print("   - Environment files")
        else:
            print("🧹 Standard reset will remove:")
            print("   - All datasets and features") 
            print("   - All progress tracking")
            print("   - All test artifacts")
            print("   - Context and conversation history")
            print("   ✅ Source code in src/ will be preserved")
        
        response = input("\nAre you sure you want to continue? (yes/no): ")
        if response.lower() not in ['yes', 'y']:
            print("❌ Reset cancelled")
            sys.exit(0)
    
    # Execute appropriate reset
    try:
        if args.include_src:
            full_reset()
        else:
            standard_reset()
            
        print(f"\n🎉 Reset complete!")
        print_next_steps(args.include_src)
        
    except Exception as e:
        print(f"❌ Error during reset: {e}")
        sys.exit(1)

def print_next_steps(full_reset_done: bool):
    """Print next steps after reset"""
    
    if full_reset_done:
        print("\n📋 Next Steps for New Project:")
        print("1. Update CLAUDE.md with your domain-specific information")
        print("2. Create a new prd.md with your project requirements")
        print("3. Set up .env file with your API keys")
        print("4. Run: claude-code commands/c00-environment-setup.md")
        print("5. Run: claude-code commands/c01-feature-creator.md")
    else:
        print("\n📋 Next Steps:")
        print("1. Update your prd.md with new requirements")
        print("2. Run: claude-code commands/c01-feature-creator.md")
        print("3. Continue with your development workflow")

if __name__ == "__main__":
    main()
```

### Directory Structure After Reset:

#### Standard Reset (--include-src not used):
```
project/
├── commands/              # ✅ Preserved
│   ├── c00-environment-setup.md
│   ├── c01-feature-creator.md
│   ├── c02-dataset-generator.md
│   ├── c03-feature-developer.md
│   ├── c04-feature-tester.md
│   ├── c08-context-manager.md
│   └── c09-progress-tracker.md
├── src/                   # ✅ Preserved (your implemented code)
├── CLAUDE.md              # ✅ Preserved
├── instructions.md        # ✅ Preserved
├── erase_progress.md      # ✅ Preserved
├── datasets/              # 🗑️ Removed
├── features/              # 🗑️ Removed  
├── progress/              # 🗑️ Removed
├── context/               # 🗑️ Removed
└── tests/                 # 🗑️ Removed
```

#### Full Reset (--include-src used):
```
project/
├── commands/              # ✅ Preserved
├── CLAUDE.md              # ✅ Preserved
├── instructions.md        # ✅ Preserved
├── erase_progress.md      # ✅ Preserved
└── [everything else removed] # 🗑️ Removed
```

### Usage Examples:
```bash
# Quick standard reset (removes artifacts, keeps code)
claude-code erase_progress.md --confirm

# Interactive standard reset
claude-code erase_progress.md

# Full nuclear reset (removes everything) 
claude-code erase_progress.md --include-src

# Full reset with confirmation skip
claude-code erase_progress.md --include-src --confirm
```

### Safety Features:
- **Directory validation**: Ensures you're in a Claude Code project
- **Confirmation prompts**: Prevents accidental deletion
- **Selective deletion**: Preserves essential template files
- **Error handling**: Graceful failure with helpful messages
- **Next steps guidance**: Shows what to do after reset

Use this script when you want to:
- Start a completely new project using this template
- Clear all progress and begin fresh on the same domain
- Clean up after testing or experimentation
- Reset to template state for sharing or archiving