# Legacy Code Refactoring

Systematic approach to modernizing and improving legacy codebases while maintaining functionality.

## Target: $ARGUMENTS

## Legacy Refactoring Framework

### 1. **Legacy Code Assessment**

```bash
# Create refactoring session
REFACTOR_SESSION="refactor-$(date +%Y%m%d-%H%M%S)"
mkdir -p .refactor-sessions/$REFACTOR_SESSION
cd .refactor-sessions/$REFACTOR_SESSION

# Initialize refactoring log
echo "Legacy Refactoring Session: $REFACTOR_SESSION" > refactor.log
echo "Target: $ARGUMENTS" >> refactor.log
echo "Started: $(date)" >> refactor.log
echo "Git Commit: $(git rev-parse HEAD)" >> refactor.log

# Create safety checkpoint
git branch "refactor-checkpoint-$(date +%Y%m%d-%H%M%S)"
git stash push -m "Pre-refactor state"
```

### 2. **Code Quality Analysis**

```bash
echo "=== Code Quality Assessment ===" >> refactor.log

# Technical debt analysis
grep -r "TODO\|FIXME\|HACK\|XXX\|DEBT" --include="*.py" . > technical_debt.txt
echo "Technical debt items found: $(wc -l < technical_debt.txt)" >> refactor.log

# Code complexity analysis
python -m radon cc . --show-complexity > complexity_analysis.txt
python -m radon mi . > maintainability_index.txt

# Code duplication detection
python -c "
import ast
import hashlib
from collections import defaultdict
import os

class DuplicationDetector(ast.NodeVisitor):
    def __init__(self):
        self.function_hashes = defaultdict(list)
        self.class_hashes = defaultdict(list)
        
    def visit_FunctionDef(self, node):
        # Create hash of function structure
        func_str = ast.dump(node, annotate_fields=False)
        func_hash = hashlib.md5(func_str.encode()).hexdigest()
        self.function_hashes[func_hash].append({
            'name': node.name,
            'line': node.lineno,
            'file': getattr(self, 'current_file', 'unknown')
        })
        self.generic_visit(node)
        
    def visit_ClassDef(self, node):
        # Create hash of class structure  
        class_str = ast.dump(node, annotate_fields=False)
        class_hash = hashlib.md5(class_str.encode()).hexdigest()
        self.class_hashes[class_hash].append({
            'name': node.name,
            'line': node.lineno,
            'file': getattr(self, 'current_file', 'unknown')
        })
        self.generic_visit(node)

detector = DuplicationDetector()

for root, dirs, files in os.walk('.'):
    for file in files:
        if file.endswith('.py'):
            filepath = os.path.join(root, file)
            try:
                with open(filepath, 'r') as f:
                    tree = ast.parse(f.read())
                    detector.current_file = filepath
                    detector.visit(tree)
            except:
                continue

# Find duplications
print('DUPLICATE FUNCTIONS:')
for hash_val, functions in detector.function_hashes.items():
    if len(functions) > 1:
        print(f'Hash {hash_val[:8]}:')
        for func in functions:
            print(f'  {func[\"file\"]}:{func[\"line\"]} - {func[\"name\"]}')

print('\\nDUPLICATE CLASSES:')
for hash_val, classes in detector.class_hashes.items():
    if len(classes) > 1:
        print(f'Hash {hash_val[:8]}:')
        for cls in classes:
            print(f'  {cls[\"file\"]}:{cls[\"line\"]} - {cls[\"name\"]}')
" > duplication_analysis.txt 2>&1

# Code smell detection
python -c "
import ast
import os
from collections import defaultdict

class CodeSmellDetector(ast.NodeVisitor):
    def __init__(self):
        self.smells = defaultdict(list)
        self.current_file = ''
        
    def visit_FunctionDef(self, node):
        # Long function detection
        if hasattr(node, 'body') and len(node.body) > 20:
            self.smells['long_functions'].append(f'{self.current_file}:{node.lineno} - {node.name} ({len(node.body)} statements)')
        
        # Too many parameters
        if len(node.args.args) > 5:
            self.smells['too_many_parameters'].append(f'{self.current_file}:{node.lineno} - {node.name} ({len(node.args.args)} parameters)')
        
        # Deep nesting
        max_depth = self._calculate_nesting_depth(node)
        if max_depth > 4:
            self.smells['deep_nesting'].append(f'{self.current_file}:{node.lineno} - {node.name} (depth {max_depth})')
            
        self.generic_visit(node)
        
    def visit_ClassDef(self, node):
        # Large class detection
        method_count = sum(1 for n in node.body if isinstance(n, ast.FunctionDef))
        if method_count > 15:
            self.smells['large_classes'].append(f'{self.current_file}:{node.lineno} - {node.name} ({method_count} methods)')
            
        self.generic_visit(node)
        
    def visit_If(self, node):
        # Complex conditionals
        if self._count_boolean_operators(node.test) > 3:
            self.smells['complex_conditionals'].append(f'{self.current_file}:{node.lineno} - Complex if statement')
            
        self.generic_visit(node)
        
    def _calculate_nesting_depth(self, node, depth=0):
        max_depth = depth
        for child in ast.iter_child_nodes(node):
            if isinstance(child, (ast.If, ast.For, ast.While, ast.With, ast.Try)):
                child_depth = self._calculate_nesting_depth(child, depth + 1)
                max_depth = max(max_depth, child_depth)
        return max_depth
        
    def _count_boolean_operators(self, node):
        count = 0
        for child in ast.walk(node):
            if isinstance(child, (ast.BoolOp, ast.Compare)):
                count += 1
        return count

detector = CodeSmellDetector()

for root, dirs, files in os.walk('.'):
    for file in files:
        if file.endswith('.py'):
            filepath = os.path.join(root, file)
            try:
                with open(filepath, 'r') as f:
                    tree = ast.parse(f.read())
                    detector.current_file = filepath
                    detector.visit(tree)
            except:
                continue

print('CODE SMELLS DETECTED:')
for smell_type, smells in detector.smells.items():
    print(f'\\n{smell_type.upper().replace(\"_\", \" \")}:')
    for smell in smells[:10]:  # Show first 10 of each type
        print(f'  {smell}')
    if len(smells) > 10:
        print(f'  ... and {len(smells) - 10} more')
" > code_smells.txt 2>&1

# Unused code detection
python -m vulture . > unused_code.txt 2>&1 || echo "Vulture not available, skipping unused code detection"
```

### 3. **Dependency Analysis**

```bash
echo "=== Dependency Analysis ===" >> refactor.log

# Outdated dependencies
pip list --outdated --format=json > outdated_dependencies.json 2>/dev/null

# Dependency security scan
python -m safety check --json > dependency_vulnerabilities.json 2>/dev/null

# Import analysis
python -c "
import ast
import os
from collections import defaultdict

class ImportAnalyzer(ast.NodeVisitor):
    def __init__(self):
        self.imports = defaultdict(list)
        self.unused_imports = set()
        self.current_file = ''
        
    def visit_Import(self, node):
        for alias in node.names:
            self.imports[alias.name].append(self.current_file)
            
    def visit_ImportFrom(self, node):
        module = node.module or ''
        for alias in node.names:
            full_name = f'{module}.{alias.name}' if module else alias.name
            self.imports[full_name].append(self.current_file)

analyzer = ImportAnalyzer()

for root, dirs, files in os.walk('.'):
    for file in files:
        if file.endswith('.py'):
            filepath = os.path.join(root, file)
            try:
                with open(filepath, 'r') as f:
                    tree = ast.parse(f.read())
                    analyzer.current_file = filepath
                    analyzer.visit(tree)
            except:
                continue

print('IMPORT ANALYSIS:')
print(f'Total unique imports: {len(analyzer.imports)}')
print('\\nMost frequently imported modules:')
import_counts = {module: len(files) for module, files in analyzer.imports.items()}
for module, count in sorted(import_counts.items(), key=lambda x: x[1], reverse=True)[:10]:
    print(f'  {module}: {count} files')

# Find potentially unused imports (basic heuristic)
print('\\nPotentially unused imports (need manual verification):')
for module, files in analyzer.imports.items():
    if len(files) == 1 and not any(stdlib in module for stdlib in ['os', 'sys', 'json', 'time', 'datetime']):
        print(f'  {module} in {files[0]}')
" > import_analysis.txt 2>&1

# Check for circular imports
python -c "
import ast
import os
from collections import defaultdict, deque

def find_circular_imports():
    # Build import graph
    imports = defaultdict(set)
    
    for root, dirs, files in os.walk('.'):
        for file in files:
            if file.endswith('.py'):
                filepath = os.path.join(root, file)
                module_name = filepath.replace('/', '.').replace('.py', '')
                
                try:
                    with open(filepath, 'r') as f:
                        tree = ast.parse(f.read())
                        
                    for node in ast.walk(tree):
                        if isinstance(node, ast.ImportFrom):
                            if node.module and not node.module.startswith('.'):
                                imports[module_name].add(node.module)
                        elif isinstance(node, ast.Import):
                            for alias in node.names:
                                imports[module_name].add(alias.name)
                except:
                    continue
    
    # Detect cycles using DFS
    def has_cycle(graph, start, visited=None, rec_stack=None):
        if visited is None:
            visited = set()
        if rec_stack is None:
            rec_stack = set()
            
        visited.add(start)
        rec_stack.add(start)
        
        for neighbor in graph.get(start, []):
            if neighbor not in visited:
                if has_cycle(graph, neighbor, visited, rec_stack):
                    return True
            elif neighbor in rec_stack:
                return True
                
        rec_stack.remove(start)
        return False
    
    cycles = []
    for module in imports:
        if has_cycle(imports, module):
            cycles.append(module)
    
    return cycles

circular_imports = find_circular_imports()
if circular_imports:
    print('CIRCULAR IMPORTS DETECTED:')
    for module in circular_imports[:10]:  # Show first 10
        print(f'  {module}')
else:
    print('No circular imports detected')
" > circular_imports.txt 2>&1
```

### 4. **Modernization Opportunities**

```bash
echo "=== Modernization Analysis ===" >> refactor.log

# Python version compatibility
python -c "
import ast
import os
import sys

class ModernizationDetector(ast.NodeVisitor):
    def __init__(self):
        self.opportunities = []
        self.current_file = ''
        
    def visit_For(self, node):
        # Detect range(len()) pattern
        if (isinstance(node.iter, ast.Call) and 
            isinstance(node.iter.func, ast.Name) and 
            node.iter.func.id == 'range' and 
            len(node.iter.args) == 1 and
            isinstance(node.iter.args[0], ast.Call) and
            isinstance(node.iter.args[0].func, ast.Name) and
            node.iter.args[0].func.id == 'len'):
            
            self.opportunities.append({
                'file': self.current_file,
                'line': node.lineno,
                'type': 'enumerate_pattern',
                'description': 'Use enumerate() instead of range(len())'
            })
            
        self.generic_visit(node)
        
    def visit_Call(self, node):
        # Detect old string formatting
        if (isinstance(node.func, ast.Attribute) and 
            node.func.attr == 'format'):
            self.opportunities.append({
                'file': self.current_file,
                'line': node.lineno,
                'type': 'string_formatting',
                'description': 'Consider using f-strings for string formatting'
            })
            
        # Detect dict.has_key() usage (Python 2 style)
        if (isinstance(node.func, ast.Attribute) and 
            node.func.attr == 'has_key'):
            self.opportunities.append({
                'file': self.current_file,
                'line': node.lineno,
                'type': 'dict_has_key',
                'description': 'Replace dict.has_key() with \"key in dict\"'
            })
            
        # Detect type() == comparisons
        if (isinstance(node.func, ast.Name) and 
            node.func.id == 'type' and
            len(node.args) == 1):
            # Check if this is in a comparison
            parent = getattr(node, 'parent', None)
            if isinstance(parent, ast.Compare):
                self.opportunities.append({
                    'file': self.current_file,
                    'line': node.lineno,
                    'type': 'type_check',
                    'description': 'Consider using isinstance() instead of type() comparison'
                })
                
        self.generic_visit(node)
        
    def visit_ListComp(self, node):
        # Check if list comprehension could be generator expression
        # This is a heuristic - need context to be sure
        if len(node.generators) == 1 and not node.generators[0].ifs:
            self.opportunities.append({
                'file': self.current_file,
                'line': node.lineno,
                'type': 'generator_expression',
                'description': 'Consider generator expression for memory efficiency'
            })
            
        self.generic_visit(node)

detector = ModernizationDetector()

for root, dirs, files in os.walk('.'):
    for file in files:
        if file.endswith('.py'):
            filepath = os.path.join(root, file)
            try:
                with open(filepath, 'r') as f:
                    tree = ast.parse(f.read())
                    detector.current_file = filepath
                    detector.visit(tree)
            except:
                continue

print('MODERNIZATION OPPORTUNITIES:')
by_type = {}
for opp in detector.opportunities:
    opp_type = opp['type']
    if opp_type not in by_type:
        by_type[opp_type] = []
    by_type[opp_type].append(opp)

for opp_type, opportunities in by_type.items():
    print(f'\\n{opp_type.upper().replace(\"_\", \" \")}:')
    for opp in opportunities[:5]:  # Show first 5 of each type
        print(f'  {opp[\"file\"]}:{opp[\"line\"]} - {opp[\"description\"]}')
    if len(opportunities) > 5:
        print(f'  ... and {len(opportunities) - 5} more')
" > modernization_opportunities.txt 2>&1

# Check for deprecated patterns
grep -r "imp\.load_source\|imp\.find_module" --include="*.py" . > deprecated_patterns.txt 2>/dev/null
grep -r "__cmp__\|__nonzero__" --include="*.py" . >> deprecated_patterns.txt 2>/dev/null
grep -r "file(" --include="*.py" . >> deprecated_patterns.txt 2>/dev/null
```

### 5. **Refactoring Strategy Planning**

```bash
cat > refactoring_strategy.md << 'EOF'
# Legacy Code Refactoring Strategy

## Assessment Summary
- Technical Debt Items: [Count from analysis]
- Code Smells: [Count from analysis]  
- Modernization Opportunities: [Count from analysis]
- Security Issues: [Count if security audit was run]

## Refactoring Priorities

### Phase 1: Safety & Stability (Week 1-2)
**Goal:** Improve code safety without changing functionality

1. **Fix Critical Issues**
   - Security vulnerabilities
   - Memory leaks
   - Resource leaks
   - Circular imports

2. **Add Missing Tests**
   - Cover critical paths with no tests
   - Add integration tests for main workflows
   - Add regression tests for bug fixes

3. **Improve Error Handling**
   - Add proper exception handling
   - Replace bare except clauses
   - Add logging for debugging

### Phase 2: Code Quality (Week 3-4)
**Goal:** Improve code maintainability

1. **Reduce Complexity**
   - Break down large functions (>20 lines)
   - Reduce parameter counts (>5 parameters)
   - Simplify deep nesting (>4 levels)

2. **Eliminate Duplication**
   - Extract common code into functions
   - Create utility modules for shared functionality
   - Use inheritance or composition for similar classes

3. **Improve Naming**
   - Use descriptive variable names
   - Follow Python naming conventions
   - Update outdated terminology

### Phase 3: Modernization (Week 5-6)
**Goal:** Update to modern Python practices

1. **Update Language Features**
   - Replace old string formatting with f-strings
   - Use enumerate() instead of range(len())
   - Use isinstance() instead of type() comparisons
   - Add type hints

2. **Update Dependencies**
   - Upgrade to latest compatible versions
   - Remove unused dependencies
   - Replace deprecated libraries

3. **Improve Architecture**
   - Apply SOLID principles
   - Implement proper separation of concerns
   - Add dependency injection where appropriate

### Phase 4: Performance & Polish (Week 7-8)
**Goal:** Optimize performance and finalize improvements

1. **Performance Optimization**
   - Replace O(n²) algorithms with O(n log n) or O(n)
   - Add caching for expensive operations
   - Use generators for memory efficiency

2. **Documentation & Standards**
   - Add comprehensive docstrings
   - Update README and documentation
   - Establish coding standards for future development

## Risk Mitigation

### Testing Strategy
- **Characterization Tests:** Capture current behavior before changes
- **Regression Tests:** Ensure no functionality is broken
- **Performance Tests:** Ensure no performance degradation

### Gradual Migration
- **Feature Flags:** Use flags to enable/disable new implementations
- **A/B Testing:** Run old and new code in parallel
- **Rollback Plan:** Ability to quickly revert changes

### Quality Gates
- All tests must pass
- Code coverage must not decrease
- Performance must not regress by more than 5%
- Security scan must pass

## Success Metrics

### Code Quality Metrics
- Cyclomatic complexity: Target < 10 per function
- Maintainability index: Target > 70
- Test coverage: Target > 80%
- Documentation coverage: Target > 90%

### Performance Metrics
- Response time: No degradation
- Memory usage: Target 20% reduction
- Error rate: Target 50% reduction

### Developer Experience
- Build time: Target 30% reduction
- Time to add new features: Target 40% reduction
- Onboarding time for new developers: Target 50% reduction

EOF
```

### 6. **Automated Refactoring Tools**

```python
# Automated refactoring utilities
import ast
import re
from typing import List, Dict, Tuple

class AutoRefactorer:
    def __init__(self):
        self.changes = []
        
    def refactor_string_formatting(self, code: str) -> str:
        """Convert .format() to f-strings where possible"""
        
        # Simple pattern matching for basic cases
        # This is a simplified version - real implementation would need AST
        patterns = [
            (r'\"([^\"]*)\"\s*\.\s*format\(([^)]+)\)', r'f"\1"'),
            (r"'([^']*)'\s*\.\s*format\(([^)]+)\)", r"f'\1'"),
        ]
        
        result = code
        for pattern, replacement in patterns:
            # This needs more sophisticated handling for variable substitution
            matches = re.findall(pattern, result)
            for match in matches:
                # Would need to parse format args and substitute properly
                pass
                
        return result
    
    def refactor_range_len_to_enumerate(self, code: str) -> str:
        """Replace range(len()) patterns with enumerate()"""
        
        # Pattern: for i in range(len(sequence)):
        pattern = r'for\s+(\w+)\s+in\s+range\s*\(\s*len\s*\(\s*(\w+)\s*\)\s*\):'
        replacement = r'for \1, item in enumerate(\2):'
        
        return re.sub(pattern, replacement, code)
    
    def extract_long_functions(self, code: str, max_lines: int = 20) -> List[Dict]:
        """Identify functions that should be broken down"""
        
        tree = ast.parse(code)
        long_functions = []
        
        for node in ast.walk(tree):
            if isinstance(node, ast.FunctionDef):
                if hasattr(node, 'body') and len(node.body) > max_lines:
                    long_functions.append({
                        'name': node.name,
                        'line': node.lineno,
                        'length': len(node.body),
                        'suggestion': f'Consider breaking down {node.name} into smaller functions'
                    })
                    
        return long_functions
    
    def suggest_variable_renames(self, code: str) -> List[Dict]:
        """Suggest better variable names"""
        
        tree = ast.parse(code)
        suggestions = []
        
        for node in ast.walk(tree):
            if isinstance(node, ast.Name):
                name = node.id
                # Check for poor naming patterns
                if len(name) <= 2 and name not in ['i', 'j', 'k', 'x', 'y', 'z']:
                    suggestions.append({
                        'line': node.lineno,
                        'current': name,
                        'suggestion': f'Consider more descriptive name for "{name}"'
                    })
                elif name.lower() in ['temp', 'tmp', 'data', 'info', 'obj']:
                    suggestions.append({
                        'line': node.lineno,
                        'current': name,
                        'suggestion': f'"{name}" is too generic, use more specific name'
                    })
                    
        return suggestions

# Create refactoring script
with open('auto_refactor.py', 'w') as f:
    f.write('''
import ast
import os
import re
from typing import List, Dict

def apply_simple_refactorings(filepath: str) -> Dict[str, int]:
    """Apply simple, safe refactorings to a Python file"""
    
    changes_made = {
        'string_formatting': 0,
        'range_len_enumerate': 0,
        'type_checks': 0,
        'has_key_replacements': 0
    }
    
    with open(filepath, 'r') as f:
        content = f.read()
    
    original_content = content
    
    # Replace .has_key() with 'in' operator
    has_key_pattern = r'(\w+)\.has_key\(([^)]+)\)'
    has_key_replacement = r'\\2 in \\1'
    new_content = re.sub(has_key_pattern, has_key_replacement, content)
    if new_content != content:
        changes_made['has_key_replacements'] = len(re.findall(has_key_pattern, content))
        content = new_content
    
    # Replace type() == with isinstance() (simple cases)
    type_pattern = r'type\((\w+)\)\s*==\s*(\w+)'
    type_replacement = r'isinstance(\\1, \\2)'
    new_content = re.sub(type_pattern, type_replacement, content)
    if new_content != content:
        changes_made['type_checks'] = len(re.findall(type_pattern, content))
        content = new_content
    
    # Only write back if changes were made
    if content != original_content:
        # Create backup
        backup_path = filepath + '.refactor_backup'
        with open(backup_path, 'w') as f:
            f.write(original_content)
        
        # Write refactored content
        with open(filepath, 'w') as f:
            f.write(content)
            
        print(f"Refactored {filepath}")
        for change_type, count in changes_made.items():
            if count > 0:
                print(f"  {change_type}: {count} changes")
    
    return changes_made

def refactor_directory(directory: str = '.'):
    """Apply refactorings to all Python files in directory"""
    
    total_changes = {
        'files_modified': 0,
        'string_formatting': 0,
        'range_len_enumerate': 0,
        'type_checks': 0,
        'has_key_replacements': 0
    }
    
    for root, dirs, files in os.walk(directory):
        for file in files:
            if file.endswith('.py') and not file.endswith('.refactor_backup'):
                filepath = os.path.join(root, file)
                try:
                    changes = apply_simple_refactorings(filepath)
                    if any(count > 0 for count in changes.values()):
                        total_changes['files_modified'] += 1
                        for change_type, count in changes.items():
                            total_changes[change_type] += count
                except Exception as e:
                    print(f"Error refactoring {filepath}: {e}")
    
    print(f"\\nRefactoring Summary:")
    print(f"Files modified: {total_changes['files_modified']}")
    for change_type, count in total_changes.items():
        if change_type != 'files_modified' and count > 0:
            print(f"{change_type}: {count} total changes")

if __name__ == "__main__":
    refactor_directory()
''')

echo "Auto-refactoring script created" >> refactor.log
```

### 7. **Test-Driven Refactoring**

```bash
echo "=== Test-Driven Refactoring Setup ===" >> refactor.log

# Create characterization tests for legacy code
cat > create_characterization_tests.py << 'EOF'
import ast
import os
import inspect
import importlib.util
from typing import List, Dict, Any

class CharacterizationTestGenerator:
    def __init__(self, module_path: str):
        self.module_path = module_path
        self.functions = []
        self.classes = []
        
    def analyze_module(self):
        """Analyze module to find testable functions"""
        
        # Parse AST to find functions and classes
        with open(self.module_path, 'r') as f:
            tree = ast.parse(f.read())
            
        for node in ast.walk(tree):
            if isinstance(node, ast.FunctionDef):
                if not node.name.startswith('_'):  # Skip private functions
                    self.functions.append({
                        'name': node.name,
                        'line': node.lineno,
                        'args': [arg.arg for arg in node.args.args]
                    })
                    
            elif isinstance(node, ast.ClassDef):
                methods = []
                for item in node.body:
                    if isinstance(item, ast.FunctionDef) and not item.name.startswith('_'):
                        methods.append({
                            'name': item.name,
                            'args': [arg.arg for arg in item.args.args]
                        })
                        
                self.classes.append({
                    'name': node.name,
                    'line': node.lineno,
                    'methods': methods
                })
    
    def generate_test_file(self, output_path: str):
        """Generate characterization test file"""
        
        test_content = '''"""
Characterization tests for legacy code
Generated automatically - captures current behavior before refactoring
"""

import unittest
import sys
import os

# Add the module path to sys.path
sys.path.insert(0, os.path.dirname(os.path.abspath(__file__)))

# Import the module under test
from {module_name} import *

class TestCharacterization(unittest.TestCase):
    """Test current behavior of legacy code"""
    
    def setUp(self):
        """Set up test fixtures"""
        pass
    
    def tearDown(self):
        """Clean up after tests"""
        pass

'''.format(module_name=os.path.splitext(os.path.basename(self.module_path))[0])

        # Generate function tests
        for func in self.functions:
            test_content += self._generate_function_test(func)
            
        # Generate class tests
        for cls in self.classes:
            test_content += self._generate_class_test(cls)
            
        test_content += '''

if __name__ == '__main__':
    unittest.main()
'''

        with open(output_path, 'w') as f:
            f.write(test_content)
            
    def _generate_function_test(self, func: Dict) -> str:
        """Generate test for a function"""
        
        return f'''
    def test_{func['name']}_characterization(self):
        """Test current behavior of {func['name']}"""
        # TODO: Add test cases with known inputs and expected outputs
        # Example:
        # result = {func['name']}(test_input)
        # self.assertEqual(result, expected_output)
        pass
'''

    def _generate_class_test(self, cls: Dict) -> str:
        """Generate test for a class"""
        
        test_code = f'''
    def test_{cls['name']}_characterization(self):
        """Test current behavior of {cls['name']}"""
        # TODO: Add test cases for class instantiation and methods
        # instance = {cls['name']}()
        '''
        
        for method in cls['methods']:
            test_code += f'''
        # Test {method['name']} method
        # result = instance.{method['name']}(test_args)
        # self.assertEqual(result, expected_result)
        '''
            
        test_code += '        pass\n'
        return test_code

def generate_characterization_tests(directory: str = '.'):
    """Generate characterization tests for all Python files in directory"""
    
    for root, dirs, files in os.walk(directory):
        for file in files:
            if file.endswith('.py') and not file.startswith('test_'):
                filepath = os.path.join(root, file)
                
                try:
                    generator = CharacterizationTestGenerator(filepath)
                    generator.analyze_module()
                    
                    # Create test file path
                    test_filename = f"test_characterization_{os.path.splitext(file)[0]}.py"
                    test_filepath = os.path.join(root, test_filename)
                    
                    generator.generate_test_file(test_filepath)
                    print(f"Generated characterization tests: {test_filepath}")
                    
                except Exception as e:
                    print(f"Error generating tests for {filepath}: {e}")

if __name__ == "__main__":
    generate_characterization_tests()
EOF

python create_characterization_tests.py > characterization_tests_output.txt 2>&1
```

### 8. **Safe Refactoring Execution**

```bash
echo "=== Safe Refactoring Execution ===" >> refactor.log

# Create refactoring execution plan
cat > execute_refactoring.sh << 'EOF'
#!/bin/bash

# Safe refactoring execution script
set -e  # Exit on any error

PHASE="$1"
if [ -z "$PHASE" ]; then
    echo "Usage: $0 <phase_number>"
    echo "Phases: 1=Safety, 2=Quality, 3=Modernization, 4=Performance"
    exit 1
fi

echo "Starting refactoring phase $PHASE..."

# Create checkpoint before each phase
git add .
git commit -m "Checkpoint before refactoring phase $PHASE" || true
git branch "refactor-phase-$PHASE-checkpoint"

case $PHASE in
    1)  # Safety & Stability Phase
        echo "Phase 1: Safety & Stability"
        
        # Run tests before changes
        echo "Running baseline tests..."
        python -m pytest tests/ -v > phase1_baseline_tests.txt 2>&1 || echo "Some tests failed in baseline"
        
        # Apply automated safety fixes
        echo "Applying safety fixes..."
        python auto_refactor.py
        
        # Run tests after changes
        echo "Running tests after safety fixes..."
        python -m pytest tests/ -v > phase1_after_tests.txt 2>&1
        
        # Compare test results
        if ! diff phase1_baseline_tests.txt phase1_after_tests.txt > /dev/null; then
            echo "WARNING: Test behavior changed after safety fixes!"
            echo "Review changes carefully before proceeding."
        fi
        ;;
        
    2)  # Code Quality Phase  
        echo "Phase 2: Code Quality"
        
        # Extract long functions
        echo "Identifying functions to refactor..."
        python -c "
import ast
import os

def find_long_functions(max_lines=20):
    long_functions = []
    for root, dirs, files in os.walk('.'):
        for file in files:
            if file.endswith('.py'):
                filepath = os.path.join(root, file)
                try:
                    with open(filepath, 'r') as f:
                        tree = ast.parse(f.read())
                    
                    for node in ast.walk(tree):
                        if isinstance(node, ast.FunctionDef):
                            if len(node.body) > max_lines:
                                long_functions.append(f'{filepath}:{node.lineno} - {node.name} ({len(node.body)} lines)')
                except:
                    continue
    return long_functions

functions = find_long_functions()
print('LONG FUNCTIONS TO REFACTOR:')
for func in functions[:10]:
    print(f'  {func}')
" > long_functions_report.txt
        ;;
        
    3)  # Modernization Phase
        echo "Phase 3: Modernization"
        
        # Apply modernization refactorings
        echo "Applying modernization fixes..."
        # This would apply the modernization opportunities found earlier
        
        # Update dependencies
        echo "Checking for dependency updates..."
        pip list --outdated > outdated_deps_before.txt
        ;;
        
    4)  # Performance Phase
        echo "Phase 4: Performance & Polish"
        
        # Run performance baseline
        echo "Establishing performance baseline..."
        # This would run performance tests if available
        ;;
        
    *)
        echo "Invalid phase number: $PHASE"
        exit 1
        ;;
esac

echo "Phase $PHASE completed successfully"
echo "Review changes and run: git add . && git commit -m 'Refactoring phase $PHASE complete'"

EOF

chmod +x execute_refactoring.sh
```

### 9. **Quality Assurance & Validation**

```bash
echo "=== Quality Assurance Setup ===" >> refactor.log

# Create comprehensive validation script
cat > validate_refactoring.py << 'EOF'
import subprocess
import json
import os
from typing import Dict, List, Any

class RefactoringValidator:
    def __init__(self):
        self.validation_results = {}
        
    def run_test_suite(self) -> Dict[str, Any]:
        """Run complete test suite and return results"""
        
        try:
            # Run pytest with coverage
            result = subprocess.run(
                ['python', '-m', 'pytest', 'tests/', '--cov=.', '--cov-report=json'],
                capture_output=True, text=True
            )
            
            # Parse coverage report
            try:
                with open('coverage.json', 'r') as f:
                    coverage_data = json.load(f)
                    coverage_percent = coverage_data['totals']['percent_covered']
            except:
                coverage_percent = 0
                
            return {
                'tests_passed': result.returncode == 0,
                'test_output': result.stdout,
                'test_errors': result.stderr,
                'coverage_percent': coverage_percent
            }
            
        except Exception as e:
            return {
                'tests_passed': False,
                'error': str(e),
                'coverage_percent': 0
            }
    
    def check_code_quality(self) -> Dict[str, Any]:
        """Check code quality metrics"""
        
        quality_results = {}
        
        # Complexity check
        try:
            result = subprocess.run(
                ['python', '-m', 'radon', 'cc', '.', '--json'],
                capture_output=True, text=True
            )
            if result.returncode == 0:
                complexity_data = json.loads(result.stdout)
                # Calculate average complexity
                total_complexity = 0
                function_count = 0
                for file_data in complexity_data.values():
                    for item in file_data:
                        if 'complexity' in item:
                            total_complexity += item['complexity']
                            function_count += 1
                
                avg_complexity = total_complexity / function_count if function_count > 0 else 0
                quality_results['avg_complexity'] = avg_complexity
                quality_results['high_complexity_functions'] = sum(
                    1 for file_data in complexity_data.values()
                    for item in file_data
                    if item.get('complexity', 0) > 10
                )
        except:
            quality_results['avg_complexity'] = 'unknown'
            
        # Maintainability index
        try:
            result = subprocess.run(
                ['python', '-m', 'radon', 'mi', '.', '--json'],
                capture_output=True, text=True
            )
            if result.returncode == 0:
                mi_data = json.loads(result.stdout)
                mi_scores = [item['mi'] for file_data in mi_data.values() for item in file_data]
                quality_results['avg_maintainability'] = sum(mi_scores) / len(mi_scores) if mi_scores else 0
        except:
            quality_results['avg_maintainability'] = 'unknown'
            
        return quality_results
    
    def check_security(self) -> Dict[str, Any]:
        """Run security checks"""
        
        security_results = {}
        
        try:
            # Run bandit security scanner
            result = subprocess.run(
                ['python', '-m', 'bandit', '-r', '.', '-f', 'json'],
                capture_output=True, text=True
            )
            
            if result.stdout:
                bandit_data = json.loads(result.stdout)
                security_results['security_issues'] = len(bandit_data.get('results', []))
                security_results['high_severity_issues'] = len([
                    issue for issue in bandit_data.get('results', [])
                    if issue.get('issue_severity') == 'HIGH'
                ])
            else:
                security_results['security_issues'] = 0
                security_results['high_severity_issues'] = 0
                
        except:
            security_results['security_issues'] = 'unknown'
            
        return security_results
    
    def validate_refactoring(self) -> Dict[str, Any]:
        """Run complete validation suite"""
        
        print("Validating refactoring changes...")
        
        # Run all validation checks
        self.validation_results['tests'] = self.run_test_suite()
        self.validation_results['quality'] = self.check_code_quality()
        self.validation_results['security'] = self.check_security()
        
        # Calculate overall score
        score = 0
        max_score = 100
        
        # Test score (40 points)
        if self.validation_results['tests']['tests_passed']:
            score += 40
        coverage = self.validation_results['tests']['coverage_percent']
        if coverage >= 80:
            score += 20
        elif coverage >= 60:
            score += 10
            
        # Quality score (30 points)  
        avg_complexity = self.validation_results['quality'].get('avg_complexity', 0)
        if isinstance(avg_complexity, (int, float)) and avg_complexity < 5:
            score += 15
        elif isinstance(avg_complexity, (int, float)) and avg_complexity < 10:
            score += 10
            
        avg_maintainability = self.validation_results['quality'].get('avg_maintainability', 0)
        if isinstance(avg_maintainability, (int, float)) and avg_maintainability > 70:
            score += 15
        elif isinstance(avg_maintainability, (int, float)) and avg_maintainability > 50:
            score += 10
            
        # Security score (10 points)
        security_issues = self.validation_results['security'].get('security_issues', 0)
        if security_issues == 0:
            score += 10
        elif security_issues < 5:
            score += 5
            
        self.validation_results['overall_score'] = score
        self.validation_results['max_score'] = max_score
        
        return self.validation_results
    
    def generate_report(self):
        """Generate validation report"""
        
        report = f"""
# Refactoring Validation Report

## Overall Score: {self.validation_results['overall_score']}/{self.validation_results['max_score']}

## Test Results
- Tests Passed: {'✓' if self.validation_results['tests']['tests_passed'] else '✗'}
- Code Coverage: {self.validation_results['tests']['coverage_percent']:.1f}%

## Code Quality
- Average Complexity: {self.validation_results['quality'].get('avg_complexity', 'unknown')}
- High Complexity Functions: {self.validation_results['quality'].get('high_complexity_functions', 'unknown')}
- Average Maintainability: {self.validation_results['quality'].get('avg_maintainability', 'unknown')}

## Security
- Security Issues: {self.validation_results['security'].get('security_issues', 'unknown')}
- High Severity Issues: {self.validation_results['security'].get('high_severity_issues', 'unknown')}

## Recommendations
"""
        
        score = self.validation_results['overall_score']
        if score >= 90:
            report += "- Excellent refactoring! Code quality significantly improved.\n"
        elif score >= 70:
            report += "- Good refactoring progress. Consider addressing remaining quality issues.\n"
        elif score >= 50:
            report += "- Moderate improvement. More refactoring work needed.\n"
        else:
            report += "- Significant issues remain. Consider additional refactoring phases.\n"
            
        if not self.validation_results['tests']['tests_passed']:
            report += "- CRITICAL: Fix failing tests before proceeding.\n"
            
        coverage = self.validation_results['tests']['coverage_percent']
        if coverage < 70:
            report += f"- Add more tests to improve coverage (current: {coverage:.1f}%).\n"
            
        return report

if __name__ == "__main__":
    validator = RefactoringValidator()
    results = validator.validate_refactoring()
    
    # Save results
    with open('refactoring_validation_results.json', 'w') as f:
        json.dump(results, f, indent=2)
    
    # Generate report
    report = validator.generate_report()
    with open('refactoring_validation_report.md', 'w') as f:
        f.write(report)
    
    print(report)
    print(f"\\nDetailed results saved to: refactoring_validation_results.json")
EOF

echo "Validation framework created" >> refactor.log
```

### 10. **Refactoring Documentation & Handoff**

```bash
cat > refactoring_documentation.md << EOF
# Legacy Code Refactoring Documentation

**Session:** $REFACTOR_SESSION
**Date:** $(date)
**Target:** $ARGUMENTS

## Refactoring Summary

### Code Health Improvements
- Technical debt items addressed: [Count]
- Code smells eliminated: [Count] 
- Security issues fixed: [Count]
- Performance optimizations: [Count]

### Quality Metrics Before/After
| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| Avg Complexity | [Before] | [After] | [Percentage] |
| Test Coverage | [Before]% | [After]% | [Difference] |
| Maintainability Index | [Before] | [After] | [Percentage] |
| Security Issues | [Before] | [After] | [Count Fixed] |

### Files Modified
[List of files that were changed during refactoring]

### Breaking Changes
[Any changes that might affect existing integrations]

### Migration Guide
[Instructions for any manual changes needed]

## Knowledge Transfer

### New Patterns Introduced
[Document any new architectural patterns or practices]

### Deprecated Patterns
[Document patterns that should no longer be used]

### Best Practices Established
[Document coding standards and practices for future development]

## Maintenance Recommendations

### Immediate Actions
- Monitor application performance for regressions
- Update deployment scripts if needed
- Train team on new patterns and practices

### Ongoing Maintenance
- Regular code quality reviews
- Automated quality gates in CI/CD
- Periodic technical debt assessment

### Future Refactoring Opportunities
[Areas identified for future improvement]

## Rollback Plan
[Instructions for reverting changes if issues arise]

EOF

echo "Refactoring session completed successfully"
echo "Documentation available at: refactoring_documentation.md"
echo "Validation results: refactoring_validation_report.md"
```

## Quality Gates

**Refactoring Success Criteria:**
- All existing tests continue to pass
- Code coverage maintained or improved
- No performance regressions > 5%
- Security scan passes
- Code quality metrics improved
- Documentation updated

**Safety Measures:**
- Comprehensive backup before changes
- Characterization tests for critical functionality
- Gradual refactoring in phases
- Continuous validation after each phase
- Rollback plan ready

This comprehensive legacy refactoring framework ensures systematic modernization while maintaining system stability and functionality.