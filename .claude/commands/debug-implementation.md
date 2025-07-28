# Systematic Debugging Implementation

A comprehensive debugging framework for diagnosing and fixing code issues systematically.

## Issue Description: $ARGUMENTS

## Debugging Protocol

### 1. **Issue Intake & Classification**

```bash
# Create debugging session
DEBUG_SESSION="debug-$(date +%Y%m%d-%H%M%S)"
mkdir -p .debug-sessions/$DEBUG_SESSION
cd .debug-sessions/$DEBUG_SESSION

# Initialize debug log
echo "Debug Session: $DEBUG_SESSION" > debug.log
echo "Issue: $ARGUMENTS" >> debug.log
echo "Started: $(date)" >> debug.log
echo "Git Commit: $(git rev-parse HEAD)" >> debug.log
```

**Issue Classification:**
- **Syntax Error:** Code won't parse/compile
- **Runtime Error:** Exception thrown during execution  
- **Logic Error:** Code runs but produces wrong results
- **Performance Issue:** Code is slow or resource-intensive
- **Integration Error:** External service/API failure
- **Configuration Error:** Environment/setup issue

### 2. **Environmental Context Gathering**

```bash
# System information
echo "=== System Context ===" >> debug.log
python --version >> debug.log
pip freeze > requirements_frozen.txt
git status --porcelain > git_status.txt
git log --oneline -5 > recent_commits.txt

# Environment variables (sanitized)
env | grep -v "SECRET\|KEY\|PASSWORD\|TOKEN" > environment.txt

# Process information
ps aux | grep python > processes.txt

# Resource usage
df -h > disk_usage.txt
free -h > memory_usage.txt 2>/dev/null || vm_stat > memory_usage.txt
```

### 3. **Error Reproduction**

```bash
# Capture the exact error
echo "=== Error Reproduction ===" >> debug.log

# Try to reproduce with verbose logging
python -u your_script.py 2>&1 | tee error_output.txt

# If it's a web application
if [[ -f "app.py" || -f "main.py" ]]; then
    echo "Testing web application endpoints..." >> debug.log
    # Add curl tests for API endpoints
fi

# Capture stack trace if available
python -c "
import traceback
import sys
try:
    # Your failing code here
    pass
except Exception as e:
    with open('stack_trace.txt', 'w') as f:
        traceback.print_exc(file=f)
    print(f'Error captured: {e}')
"
```

### 4. **Code Analysis Phase**

```bash
echo "=== Code Analysis ===" >> debug.log

# Static analysis
python -m pylint --errors-only . > lint_errors.txt 2>&1
python -m mypy . > type_errors.txt 2>&1

# Dependency analysis
pip check > dependency_conflicts.txt 2>&1

# Security scan
python -m bandit -r . -f txt > security_issues.txt 2>&1

# Find suspicious patterns
grep -r "TODO\|FIXME\|HACK\|XXX" --include="*.py" . > tech_debt.txt
```

### 5. **Interactive Debugging**

**For Python applications:**
```python
# Enhanced debugging script
import pdb
import logging
import traceback
from functools import wraps

# Set up debug logging
logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('debug_trace.log'),
        logging.StreamHandler()
    ]
)

def debug_decorator(func):
    """Decorator to add debugging to functions"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        logging.debug(f"Calling {func.__name__} with args={args}, kwargs={kwargs}")
        try:
            result = func(*args, **kwargs)
            logging.debug(f"{func.__name__} returned: {result}")
            return result
        except Exception as e:
            logging.error(f"Exception in {func.__name__}: {e}")
            logging.error(traceback.format_exc())
            pdb.post_mortem()
            raise
    return wrapper

# Add this decorator to problematic functions
```

### 6. **Data Flow Analysis**

```bash
echo "=== Data Flow Analysis ===" >> debug.log

# Trace data flow through the application
cat > data_flow_tracer.py << 'EOF'
import sys
import inspect
from typing import Any, Dict

class DataFlowTracer:
    def __init__(self):
        self.trace_data = []
        
    def trace_calls(self, frame, event, arg):
        if event == 'call':
            func_name = frame.f_code.co_name
            filename = frame.f_code.co_filename
            line_no = frame.f_lineno
            local_vars = {k: v for k, v in frame.f_locals.items() 
                         if not k.startswith('__')}
            
            self.trace_data.append({
                'function': func_name,
                'file': filename,
                'line': line_no,
                'variables': local_vars
            })
            
        return self.trace_calls
    
    def start_tracing(self):
        sys.settrace(self.trace_calls)
    
    def stop_tracing(self):
        sys.settrace(None)
        return self.trace_data

# Usage:
# tracer = DataFlowTracer()
# tracer.start_tracing()
# your_problematic_code()
# trace_data = tracer.stop_tracing()
EOF

python data_flow_tracer.py > data_flow.txt 2>&1
```

### 7. **Performance Profiling**

```bash
echo "=== Performance Analysis ===" >> debug.log

# CPU profiling
python -m cProfile -o profile_stats.prof your_script.py

# Generate human-readable profile
python -c "
import pstats
p = pstats.Stats('profile_stats.prof')
p.sort_stats('cumulative')
p.print_stats(20)
" > performance_profile.txt

# Memory profiling
python -m memory_profiler your_script.py > memory_profile.txt 2>&1

# Line-by-line profiling
kernprof -l -v your_script.py > line_profile.txt 2>&1
```

### 8. **Database/External Service Debugging**

```bash
echo "=== External Dependencies ===" >> debug.log

# Database connection test
if command -v psql &> /dev/null; then
    psql -h localhost -U user -d database -c "SELECT 1;" > db_test.txt 2>&1
fi

# API endpoint testing
if [[ -n "$API_BASE_URL" ]]; then
    curl -v "$API_BASE_URL/health" > api_health.txt 2>&1
    curl -v -H "Content-Type: application/json" "$API_BASE_URL/test" > api_test.txt 2>&1
fi

# Network connectivity
ping -c 3 google.com > network_test.txt 2>&1
```

### 9. **Log Analysis**

```bash
echo "=== Log Analysis ===" >> debug.log

# Analyze application logs
if [[ -f "app.log" ]]; then
    # Extract errors
    grep -i "error\|exception\|traceback" app.log > extracted_errors.txt
    
    # Timeline of events
    grep "$(date '+%Y-%m-%d')" app.log | head -100 > recent_logs.txt
    
    # Pattern analysis
    awk '{print $4}' app.log | sort | uniq -c | sort -nr > log_patterns.txt
fi

# System logs (if accessible)
journalctl --user --since "1 hour ago" > system_logs.txt 2>/dev/null || echo "No system logs available"
```

### 10. **Hypothesis Formation & Testing**

```bash
cat > hypothesis_testing.md << 'EOF'
# Debug Hypothesis Testing

## Hypothesis 1: [Description]
**Evidence Supporting:**
- 

**Evidence Against:**
- 

**Test Plan:**
1. 
2. 
3. 

**Result:** [Pass/Fail]

## Hypothesis 2: [Description]
**Evidence Supporting:**
- 

**Evidence Against:**
- 

**Test Plan:**
1. 
2. 
3. 

**Result:** [Pass/Fail]

EOF
```

### 11. **Automated Fix Suggestions**

```python
# Automated fix suggestions based on common patterns
class DebugFixSuggester:
    def __init__(self, error_text, code_files):
        self.error_text = error_text
        self.code_files = code_files
        
    def suggest_fixes(self):
        suggestions = []
        
        # Common error patterns and fixes
        if "ModuleNotFoundError" in self.error_text:
            missing_module = self.extract_missing_module()
            suggestions.append(f"Install missing module: pip install {missing_module}")
            
        if "NameError" in self.error_text:
            suggestions.append("Check for typos in variable names")
            suggestions.append("Verify all imports are present")
            
        if "IndentationError" in self.error_text:
            suggestions.append("Run: python -m autopep8 --in-place --aggressive file.py")
            
        if "TypeError" in self.error_text:
            suggestions.append("Check function parameter types")
            suggestions.append("Verify data types match expected values")
            
        if "ConnectionError" in self.error_text:
            suggestions.append("Check network connectivity")
            suggestions.append("Verify service endpoints are available")
            suggestions.append("Check firewall settings")
            
        return suggestions
        
    def extract_missing_module(self):
        # Extract module name from error message
        import re
        match = re.search(r"No module named '([^']+)'", self.error_text)
        return match.group(1) if match else "unknown_module"

# Generate fix suggestions
with open('error_output.txt', 'r') as f:
    error_content = f.read()

suggester = DebugFixSuggester(error_content, ['*.py'])
fixes = suggester.suggest_fixes()

with open('fix_suggestions.txt', 'w') as f:
    for i, fix in enumerate(fixes, 1):
        f.write(f"{i}. {fix}\n")
```

### 12. **Solution Implementation & Verification**

```bash
echo "=== Solution Implementation ===" >> debug.log

# Create a fix branch
git checkout -b "fix-$DEBUG_SESSION"

# Apply fixes systematically
echo "Applying fix: [description]" >> debug.log

# Test each fix
python -m pytest tests/ -v > test_results_after_fix.txt 2>&1

# Verify the fix works
echo "Verification test:" >> debug.log
# Run the original failing scenario

# Performance regression check
python -m cProfile -o profile_after_fix.prof your_script.py
```

### 13. **Documentation & Knowledge Base Update**

```bash
cat > debugging_summary.md << EOF
# Debug Session Summary

**Issue:** $ARGUMENTS
**Session ID:** $DEBUG_SESSION
**Date:** $(date)

## Root Cause
[Detailed explanation of what caused the issue]

## Solution Applied
[Step-by-step description of the fix]

## Prevention Measures
[How to prevent this issue in the future]

## Related Issues
[Links to similar problems or documentation]

## Lessons Learned
[Key insights gained from this debugging session]

EOF

# Update team knowledge base
if [[ -d "docs/debugging" ]]; then
    cp debugging_summary.md "docs/debugging/issue-$DEBUG_SESSION.md"
fi
```

### 14. **Cleanup & Session Archive**

```bash
echo "=== Session Cleanup ===" >> debug.log

# Create comprehensive archive
tar -czf "debug-session-$DEBUG_SESSION.tar.gz" .debug-sessions/$DEBUG_SESSION

# Clean up temporary files
rm -f *.prof *.pyc __pycache__/

# Commit fixes if successful
if [[ "$FIX_SUCCESSFUL" == "true" ]]; then
    git add .
    git commit -m "Fix: $ARGUMENTS (Debug session: $DEBUG_SESSION)"
    git checkout main
    git merge "fix-$DEBUG_SESSION"
    git branch -d "fix-$DEBUG_SESSION"
fi

echo "Debug session completed: $(date)" >> debug.log
```

## Debug Tools & Utilities

**Essential Tools:**
```bash
# Install debug tools
pip install ipdb memory_profiler line_profiler py-spy

# For web applications
pip install flask-debugtoolbar django-debug-toolbar

# For async code
pip install aiomonitor

# Static analysis
pip install pylint mypy bandit vulture
```

**Debug Configuration:**
```python
# debug_config.py
import logging
import os

DEBUG_LEVEL = os.getenv('DEBUG_LEVEL', 'INFO')
DEBUG_FILE = os.getenv('DEBUG_FILE', 'debug.log')

logging.basicConfig(
    level=getattr(logging, DEBUG_LEVEL),
    format='%(asctime)s - %(name)s - %(levelname)s - %(funcName)s:%(lineno)d - %(message)s',
    handlers=[
        logging.FileHandler(DEBUG_FILE),
        logging.StreamHandler()
    ]
)

def setup_debugging():
    """Enhanced debugging setup"""
    if os.getenv('PYTHON_DEBUG'):
        import pdb
        import signal
        signal.signal(signal.SIGUSR1, lambda sig, frame: pdb.set_trace())
        print("Debug mode enabled. Send SIGUSR1 to enter debugger.")
```

## Quality Gates

**Debug Session Success Criteria:**
- Root cause identified and documented
- Fix implemented and tested
- No performance regression introduced
- Prevention measures documented
- Knowledge base updated

**Failure Escalation:**
- If issue remains unresolved after 4 hours, escalate to senior developer
- If system-wide impact, immediately escalate to architecture team
- Document all attempted solutions for future reference

This systematic approach ensures thorough debugging while building organizational knowledge to prevent similar issues.