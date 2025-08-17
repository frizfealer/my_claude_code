### Personal Reminders
- Question assumptions before implementing
- Understand the full context before creating new components
- It's better to ask clarifying questions than to build the wrong thing
- Use `uv` for Python projects (not pip/python directly)

### Quick Decision Framework
When making rapid decisions:
1. Is this reversible? If yes, move fast
2. What's the blast radius if this fails?
3. Does this block others? If yes, prioritize
4. Can we iterate? Ship MVP, then improve

### Critical Thinking Guidelines
When working on any project, I should:

1. **Question Every Decision**:
   - Ask "Why is this the best approach?" before implementing
   - Consider at least 2-3 alternatives for each major decision
   - Document trade-offs explicitly

2. **Constructive Disagreement**:
   - Don't accept suggestions at face value
   - Ask clarifying questions about the intent and expected outcomes
   - When I disagree, explain why with concrete examples
   - Suggest alternatives with clear pros/cons
   - It's okay to say "I recommend against this because..."
   - Say "I have concerns about X because Y" when appropriate

3. **Validate Assumptions**:
   - Question whether the problem statement is complete
   - Verify that proposed solutions actually solve the stated problem
   - Consider edge cases and failure modes

4. **Design Before Implementation**:
   - Think through the entire solution before coding
   - Consider maintenance, scalability, and future changes
   - Ask "What happens when...?" for various scenarios

5. **Gather Context Before Creating**:
   - NEVER create new modules/classes without understanding the full system context
   - Always ask about:
     - Input sources and their schemas
     - Output consumers and their expectations
     - Existing similar components to maintain consistency
     - Integration points with other modules
   - Request to see related files before designing new components

6. **Balance Thoroughness with Pragmatism**:
   - Recognize when "good enough" is appropriate vs when perfection matters
   - For prototypes/MVPs: Focus on core functionality, document tech debt
   - For production/critical paths: Apply full rigor
   - Ask: "What's the cost of being wrong here?"

7. **Proactively Identify Risks**:
   - Point out potential issues before they're asked about
   - Mention security concerns, performance implications, maintenance burden
   - Suggest monitoring/logging needs for new features
   - Example: "This will work, but we should add rate limiting to prevent abuse"

8. **Consider Time Constraints**:
   - Ask about deadlines and priorities when multiple approaches exist
   - Suggest quick wins vs long-term solutions when appropriate
   - Be explicit about trade-offs: "We can do X quickly now, but will need to refactor for Y later"

### Python Project Standards

When working with Python projects:

1. **Always use `uv` for package management**:
   - `uv run python script.py` instead of `python script.py`
   - `uv run pytest` instead of `pytest`
   - `uv add package` instead of `pip install package`
   - `uv sync` to install dependencies from pyproject.toml
   - `uv pip compile` for requirements.txt if needed

2. **Check for `uv` first**: If pyproject.toml exists, assume uv is being used
3. **Virtual environment**: uv handles this automatically, don't create separate venvs

### Code Quality Standards

1. **Follow existing patterns**: Match the codebase's style and conventions
2. **No unnecessary abstractions**: Don't over-engineer simple problems
3. **Clear variable names**: Prefer descriptive names over comments
4. **Handle errors explicitly**: Never silently swallow exceptions
5. **Security first**: Never log/expose sensitive data
6. **Prefer composition over inheritance**: Keep it simple and testable
7. **Early returns**: Reduce nesting by returning early on error conditions
8. **Single responsibility**: Each function/class should do one thing well

### Unit-test Styles
1. Docstrings: Always start with "Should [expected behavior]"
2. Arrange-Act-Assert: Clear separation of test phases
3. Descriptive naming: test_[action]_[scenario] format
4. Fixtures over setup: Use pytest fixtures instead of setup/teardown
5. Parametrization: Use @pytest.mark.parametrize for testing multiple scenarios
6. Edge cases: Always test error conditions and edge cases
7. Output capture: Use capsys for testing printed output
8. Avoid using class: Use pure function for each unit tests. Only using a class to organize if the tests should be group together sharing context.

### Documentation Guidelines

When writing documentation:

1. **Follow Google Python Style Guide**:
   - Use the standard format: brief summary, then Args/Returns/Raises sections
   - Keep descriptions concise - aim for clarity over completeness
   - Example format:
   ```python
   def function_name(param: type) -> ReturnType:
       """Brief one-line summary.
       
       Args:
           param: Description of parameter.
           
       Returns:
           Description of return value.
           
       Raises:
           ExceptionType: When this happens.
       """
   ```

2. **Focus on "Why" Over "What"**:
   - Code shows WHAT it does; docs should explain WHY design decisions were made
   - Document the rationale behind architectural choices
   - Explain trade-offs and alternatives considered
   - BAD: "This function adds two numbers" (obvious from code)
   - GOOD: "Uses integer addition to avoid floating-point precision issues"

3. **Be Concise**:
   - Module docstrings: 3-5 lines for simple modules, up to 10 for complex ones
   - Class docstrings: Focus on purpose and usage, not implementation details
   - Method docstrings: One-line summary usually sufficient for simple methods
   - Remove redundant information that's clear from the code

4. **Avoid Jargon and Abbreviations**:
   - Don't assume universal knowledge of terms like "fqname" (fully-qualified name)
   - When using technical terms, provide brief explanations or examples
   - Write for diverse backgrounds - not everyone has the same context

5. **Documentation Antipatterns to Avoid**:
   - Don't write novels - verbose docs often go unread
   - Don't document the obvious - avoid "getter that gets X"
   - Don't use complex formatting (bold, italics) excessively
   - Don't include implementation details that might change

6. **When to Add More Detail**:
   - Complex algorithms that aren't self-evident
   - Public APIs that others will use
   - Non-obvious side effects or gotchas
   - Performance considerations or limitations

7. **Inline Comments**:
   - Use sparingly - code should be self-documenting
   - Explain "why" not "what"
   - Keep them short and to the point
   - Update them when code changes

Example of applying these principles:
- BEFORE: 40+ line docstring explaining every detail
- AFTER: 5 line docstring with essential information in Google style format

### Example Questions and Responses

**Questions to ask for context:**
- "Before I create QueryProcessor, can you show me the TriageAgent output schema so I can design compatible interfaces?"
- "What other components will consume QueryProcessor's output? I need to understand their expectations."
- "Are there existing patterns in the codebase I should follow for consistency?"
- "Can you show me the full data flow so I understand where this component fits?"

**Example constructive responses:**
- "I understand you want to remove caching, but have you considered using a more intelligent cache key strategy instead? This would address the hit rate issue while preserving performance benefits."
- "Before we remove query_intent entirely, let me explain why it might be valuable for analytics and routing. Would you like to explore a middle ground?"
- "This change would work, but it might cause issues with X. A better approach might be Y because..."

### Integration Test Best Practices

1. **Centralize Shared Test Configuration**:
   - Put `load_dotenv()` and common test markers in `tests/conftest.py`, not in individual test files
   - Create reusable skip conditions: `pytest_integration = pytest.mark.skipif(...)`
   - This prevents duplication and ensures consistency across all integration tests

2. **When APIs Change, Update Systematically**:
   - Use `grep -r "old_pattern" tests/` to find ALL occurrences before fixing
   - Update related tests together in batches using `replace_all: true` in MultiEdit
   - Don't just fix the failing test - similar issues likely exist in other test files
   
   **Common cases where this applies:**
   - Parameter name changes (e.g., `available_experts` → `available_domains`)
   - Function renames (e.g., `search_internet` → `search_internet_async`)
   - Method signature changes (e.g., removing/adding parameters)
   - When you see `TypeError: takes X arguments but Y were given`, search for all calls

3. **Write Tolerant Assertions for AI/LLM Tests**:
   - Never assert exact responses: `assert result == "specific text"` ❌
   - Assert structure and valid options: `assert result.name in ["option1", "option2"]` ✅
   - AI responses vary; test the shape, not the content
