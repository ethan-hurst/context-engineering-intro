# PRP Quality Review & Improvement

Comprehensive review and enhancement of existing PRPs to ensure they meet bulletproof standards.

## Target PRP: $ARGUMENTS

## Review Process

### 1. **PRP Structure Analysis**

```bash
# Extract PRP metadata
PRP_FILE="$ARGUMENTS"
echo "Reviewing PRP: $PRP_FILE" > .prp-review.log
echo "Review started: $(date)" >> .prp-review.log

# Check required sections
REQUIRED_SECTIONS=("Goal" "Why" "What" "All Needed Context" "Implementation Blueprint" "Validation Loop")
for section in "${REQUIRED_SECTIONS[@]}"; do
    if grep -q "## $section" "$PRP_FILE"; then
        echo "✓ Section '$section' found" >> .prp-review.log
    else
        echo "✗ Missing section: '$section'" >> .prp-review.log
    fi
done
```

### 2. **Content Completeness Review**

#### **Context Sufficiency Check:**
```yaml
Required Context Elements:
- [ ] External API documentation links with specific sections
- [ ] Code pattern examples from existing codebase  
- [ ] Error handling strategies with real examples
- [ ] Database schema or data structure requirements
- [ ] Authentication/authorization requirements
- [ ] Performance benchmarks and constraints
- [ ] Integration points with existing systems
- [ ] Known gotchas and edge cases
- [ ] Validation commands that are executable
- [ ] Success criteria that are measurable
```

#### **Implementation Blueprint Quality:**
- **Specificity Check:**
  ```bash
  # Look for vague language
  grep -i "somehow\|maybe\|probably\|should work\|similar to" "$PRP_FILE" > .vague-language.log
  
  # Check for missing file paths
  grep -c "src/\|components/\|pages/" "$PRP_FILE" > .file-paths-count.log
  
  # Verify code examples are complete
  grep -A5 -B5 "```" "$PRP_FILE" | grep -c "TODO\|FIXME\|..." > .incomplete-code.log
  ```

- **Actionability Assessment:**
  - Are all steps clearly defined?
  - Can each step be completed without additional research?
  - Are dependencies explicitly stated?

### 3. **Technical Accuracy Review**

#### **Code Pattern Validation:**
```bash
# Check if referenced files exist
grep -o "src/[^)]*\.py\|components/[^)]*\.tsx\|pages/[^)]*\.tsx" "$PRP_FILE" | while read file; do
    if [ -f "$file" ]; then
        echo "✓ Referenced file exists: $file" >> .prp-review.log
    else
        echo "✗ Missing referenced file: $file" >> .prp-review.log
    fi
done

# Validate external URLs
grep -o "https://[^)]*" "$PRP_FILE" | while read url; do
    if curl -s --head "$url" | head -n 1 | grep -q "200 OK"; then
        echo "✓ URL accessible: $url" >> .prp-review.log
    else
        echo "✗ URL not accessible: $url" >> .prp-review.log
    fi
done
```

#### **Technical Feasibility Assessment:**
- Are the proposed solutions technically sound?
- Are performance requirements realistic?
- Are dependencies available and compatible?
- Are security considerations adequate?

### 4. **Risk Analysis Enhancement**

```python
# Risk Assessment Framework
risk_categories = {
    "implementation": [
        "Complex dependencies",
        "Breaking changes required", 
        "Performance impact",
        "Security implications"
    ],
    "business": [
        "User experience impact",
        "Maintenance overhead",
        "Resource requirements",
        "Timeline constraints"
    ],
    "technical": [
        "Scalability concerns",
        "Integration complexity",
        "Data migration needs",
        "Rollback difficulty"
    ]
}

# For each category, assess:
# - Probability (1-5)
# - Impact (1-5) 
# - Mitigation strategy
# - Owner responsibility
```

### 5. **Validation Command Testing**

```bash
# Test each validation command in the PRP
echo "=== Testing Validation Commands ===" >> .prp-review.log

# Extract validation commands
grep -A10 "```bash" "$PRP_FILE" | grep -v "```" | while read cmd; do
    if [ -n "$cmd" ]; then
        echo "Testing command: $cmd" >> .prp-review.log
        # Test in dry-run mode where possible
        echo "$cmd --dry-run 2>&1 || echo 'Command test failed'" >> .validation-test.sh
    fi
done

# Make executable and run
chmod +x .validation-test.sh
bash .validation-test.sh >> .prp-review.log 2>&1
```

### 6. **Success Criteria Enhancement**

**SMART Criteria Validation:**
- **Specific:** Are outcomes precisely defined?
- **Measurable:** Can success be quantified?
- **Achievable:** Are goals realistic given constraints?
- **Relevant:** Do outcomes align with business objectives?  
- **Time-bound:** Are deadlines or milestones specified?

**Enhanced Success Criteria Template:**
```yaml
Performance:
  - API response time: < 200ms for 95th percentile
  - Page load time: < 2 seconds on 3G
  - Memory usage: < 100MB per user session
  
Reliability:
  - Uptime: 99.9% availability
  - Error rate: < 0.1% of requests
  - Recovery time: < 5 minutes for failures
  
Security:
  - All inputs validated and sanitized
  - Authentication required for sensitive operations  
  - Audit logging for all data modifications
  
Usability:
  - Task completion rate: > 95%
  - User satisfaction: > 4.5/5
  - Accessibility: WCAG 2.1 AA compliance
```

### 7. **Anti-Pattern Detection**

```bash
# Common PRP anti-patterns
echo "=== Anti-Pattern Detection ===" >> .prp-review.log

# Mock implementation patterns
grep -n "setTimeout\|mock\|fake\|dummy\|stub\|TODO.*implement" "$PRP_FILE" >> .anti-patterns.log

# Vague requirements
grep -n "user-friendly\|intuitive\|simple\|clean\|nice" "$PRP_FILE" >> .vague-requirements.log

# Missing error handling
if ! grep -q "error\|exception\|failure" "$PRP_FILE"; then
    echo "Warning: No error handling mentioned" >> .prp-review.log
fi

# Incomplete validation
if ! grep -q "test\|validate\|verify" "$PRP_FILE"; then
    echo "Warning: Insufficient validation strategy" >> .prp-review.log
fi
```

### 8. **Improvement Recommendations**

Based on the review findings, generate specific improvement recommendations:

```bash
# Generate improvement report
cat > .prp-improvements.md << 'EOF'
# PRP Improvement Recommendations

## Critical Issues (Must Fix)
EOF

# Add critical issues found
[ -s .anti-patterns.log ] && echo "- Remove mock implementation patterns" >> .prp-improvements.md
[ -s .incomplete-code.log ] && echo "- Complete all code examples" >> .prp-improvements.md

cat >> .prp-improvements.md << 'EOF'

## Enhancement Opportunities

### Context Enrichment
- Add more specific code examples from existing codebase
- Include performance benchmarks and constraints
- Provide more detailed error handling patterns

### Implementation Clarity  
- Break down complex steps into smaller, actionable tasks
- Add validation checkpoints throughout implementation
- Include rollback procedures for each major step

### Quality Assurance
- Enhance test coverage requirements
- Add security review checkpoints
- Include accessibility validation steps

### Documentation
- Add troubleshooting guides for common issues
- Include links to relevant team documentation
- Provide context on design decisions

EOF
```

### 9. **PRP Scoring Framework**

```python
# Comprehensive PRP scoring
def score_prp(prp_content):
    scores = {
        "completeness": 0,    # /25 points
        "clarity": 0,         # /25 points  
        "actionability": 0,   # /25 points
        "quality": 0          # /25 points
    }
    
    # Completeness scoring
    required_sections = ["Goal", "Why", "What", "Context", "Blueprint", "Validation"]
    scores["completeness"] = (sections_found / len(required_sections)) * 25
    
    # Clarity scoring  
    vague_terms = count_vague_language(prp_content)
    scores["clarity"] = max(0, 25 - (vague_terms * 2))
    
    # Actionability scoring
    executable_commands = count_executable_commands(prp_content)
    file_references = count_valid_file_references(prp_content)
    scores["actionability"] = min(25, (executable_commands + file_references) * 2)
    
    # Quality scoring
    code_examples = count_complete_code_examples(prp_content)
    validation_steps = count_validation_steps(prp_content)
    scores["quality"] = min(25, (code_examples + validation_steps) * 3)
    
    total_score = sum(scores.values())
    return scores, total_score

# Target: Minimum 80/100 for production PRPs
```

### 10. **Enhanced PRP Template Generation**

If the PRP needs significant improvements, generate an enhanced version:

```bash
# Create improved PRP template
cat > "${PRP_FILE%.md}_enhanced.md" << EOF
# Enhanced PRP: $(basename "$PRP_FILE" .md)

## Metadata
- Original PRP: $PRP_FILE
- Review Date: $(date)
- Enhancement Level: [Critical/Major/Minor]
- Estimated Implementation Time: [X hours]

## [Original content with enhancements marked]

<!-- ENHANCEMENT: Added specific performance requirements -->
<!-- ENHANCEMENT: Included error handling patterns -->
<!-- ENHANCEMENT: Added security considerations -->

EOF

echo "Enhanced PRP template created: ${PRP_FILE%.md}_enhanced.md"
```

## Quality Gates for PRPs

**Minimum Standards:**
- All required sections present and complete
- No anti-patterns detected
- All referenced files and URLs valid
- Validation commands executable
- Success criteria measurable
- Risk assessment comprehensive
- Overall score ≥ 80/100

**Review Outcome:**
- **Pass:** PRP meets bulletproof standards
- **Conditional:** Minor improvements needed
- **Fail:** Major revision required

## Output Artifacts

The review process generates:
- `.prp-review.log` - Detailed review findings
- `.prp-improvements.md` - Specific improvement recommendations  
- `.prp-score.json` - Quantitative quality assessment
- `*_enhanced.md` - Improved PRP template (if needed)

Use these artifacts to systematically improve PRP quality and ensure bulletproof implementation success.