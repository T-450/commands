# TDD Cycle Workflow - GitHub Copilot Instructions

## Overview
Comprehensive Test-Driven Development (TDD) workflow with strict red-green-refactor discipline.

## When to Use
Ask GitHub Copilot to follow this workflow when:
- "Implement [feature] using TDD methodology"
- "Create [component] with test-first approach"  
- "Build [module] following TDD cycle"
- "Develop [functionality] using red-green-refactor"

## Workflow Phases

### Phase 1: Requirements & Test Specification

#### 1.1 Analyze Requirements
**Prompt Copilot:**
```
Analyze requirements for [feature]. Define:
1. Acceptance criteria
2. Edge cases  
3. Test scenarios
4. Test data needed
```

**Expected Output:**
- Clear acceptance criteria
- Edge case matrix
- Comprehensive test scenarios

#### 1.2 Design Test Architecture
**Prompt Copilot:**
```
Design test architecture for [feature]:
1. Test structure and organization
2. Fixtures and test data
3. Mock strategy
4. Test isolation approach
```

**Validation:**
- Tests must be isolated
- Fast execution (< 5 seconds for unit tests)
- Clear test organization

### Phase 2: RED - Write Failing Tests

#### 2.1 Write Failing Tests
**Prompt Copilot:**
```
Write FAILING tests for [feature] that:
1. Test happy path scenarios
2. Cover edge cases and boundaries
3. Include error handling scenarios
4. Use descriptive test names
5. Follow AAA pattern (Arrange-Act-Assert)

IMPORTANT: Tests must fail initially. Do NOT implement production code yet.
```

**Example Request:**
```
Write failing tests for a shopping cart that:
- Adds items correctly
- Calculates total price with discounts
- Handles empty cart scenarios
- Validates quantity limits
- Prevents negative quantities
```

**Test Naming Convention:**
```
should_[expected_behavior]_when_[condition]

Examples:
- should_add_item_to_cart_when_valid_product_provided
- should_calculate_discount_when_threshold_met
- should_throw_error_when_negative_quantity_given
```

#### 2.2 Verify Test Failures
Run tests and ensure they fail for the RIGHT reasons:
- Missing implementation (GOOD)
- Not test syntax errors (BAD)
- Not import errors (BAD)

**Checklist:**
- [ ] All tests written before implementation
- [ ] All tests fail with meaningful error messages
- [ ] Failures due to missing implementation
- [ ] No test passes accidentally

### Phase 3: GREEN - Make Tests Pass

#### 3.1 Minimal Implementation
**Prompt Copilot:**
```
Implement MINIMAL code to make these tests pass:
[paste failing tests]

Requirements:
- Write only what's needed to pass tests
- No extra features or optimizations
- Keep it simple and straightforward
- Focus on making tests green
```

**Example:**
```
Make these shopping cart tests pass with minimal implementation.
Do not add:
- Features not tested
- Performance optimizations
- Additional validation beyond tests
```

#### 3.2 Verify All Tests Pass
```bash
# Run tests
npm test          # JavaScript
pytest            # Python
mvn test          # Java
go test ./...     # Go
```

**Checklist:**
- [ ] All tests pass
- [ ] No extra code beyond requirements
- [ ] Coverage meets minimum 80%
- [ ] No tests modified to pass

### Phase 4: REFACTOR - Improve Code Quality

#### 4.1 Code Refactoring
**Prompt Copilot:**
```
Refactor this implementation while keeping tests green:
[paste code]

Focus on:
1. SOLID principles
2. Remove duplication (DRY)
3. Improve naming clarity
4. Extract methods for clarity
5. Optimize performance
6. Add documentation

CONSTRAINT: Run tests after each change. All tests must stay green.
```

**Refactoring Triggers:**
- Cyclomatic complexity > 10
- Method length > 20 lines
- Class length > 200 lines
- Duplicate code blocks > 3 lines

#### 4.2 Test Refactoring
**Prompt Copilot:**
```
Refactor these tests:
[paste tests]

Improvements:
1. Remove test duplication
2. Extract common fixtures
3. Improve test names
4. Better test organization
5. Enhanced readability

CONSTRAINT: Coverage must stay same or improve.
```

**Checklist:**
- [ ] Tests still pass after refactoring
- [ ] Code complexity reduced
- [ ] Duplication eliminated
- [ ] Performance maintained or improved

### Phase 5: Integration Tests

#### 5.1 Write Integration Tests (Failing First)
**Prompt Copilot:**
```
Write FAILING integration tests for [feature]:
1. Test component interactions
2. Verify API contracts
3. Check data flow
4. Test external dependencies

Tests must fail initially.
```

#### 5.2 Implement Integration Logic
**Prompt Copilot:**
```
Implement integration code to make these tests pass:
[paste failing integration tests]

Focus on:
- Component interaction
- Data flow between components
- API contract compliance
```

### Phase 6: Continuous Improvement

#### 6.1 Add Edge Cases & Performance Tests
**Prompt Copilot:**
```
Add additional tests for [feature]:
1. Performance/stress tests
2. Boundary value tests
3. Error recovery tests
4. Race condition tests (if concurrent)
```

#### 6.2 Final Review
**Prompt Copilot:**
```
Review this TDD implementation:
[paste code and tests]

Check:
1. TDD process followed correctly
2. Code quality and maintainability
3. Test quality and coverage
4. Potential improvements
```

## Incremental Mode (One Test at a Time)

**When to use:** Learning TDD or very complex features

**Process:**
1. Write ONE failing test
2. Make ONLY that test pass
3. Refactor if needed
4. Repeat for next test

**Example Prompt:**
```
Let's implement this feature one test at a time:
1. First, write a failing test for adding a single item to cart
2. [After implementation] Now write a failing test for calculating total
3. [Continue...] Next, test removing items from cart
```

## Suite Mode (Batch Testing)

**When to use:** Well-understood features or experienced with TDD

**Process:**
1. Write ALL tests for feature (failing)
2. Implement code to pass ALL tests
3. Refactor entire module
4. Add integration tests

**Example Prompt:**
```
Write complete test suite for shopping cart including:
- Add/remove items
- Calculate totals
- Apply discounts
- Validate constraints
- Handle errors

Then implement all functionality.
```

## Language-Specific Examples

### Python (pytest)
```python
# Prompt: "Write failing pytest tests for a calculator class"

import pytest
from calculator import Calculator

class TestCalculator:
    def test_should_add_two_numbers_when_both_positive(self):
        calc = Calculator()
        result = calc.add(2, 3)
        assert result == 5
    
    def test_should_raise_error_when_dividing_by_zero(self):
        calc = Calculator()
        with pytest.raises(ValueError):
            calc.divide(10, 0)
```

### JavaScript (Jest)
```javascript
// Prompt: "Write failing Jest tests for a user service"

describe('UserService', () => {
  it('should create user when valid data provided', async () => {
    const service = new UserService();
    const user = await service.createUser({
      email: 'test@example.com',
      password: 'secure123'
    });
    expect(user.id).toBeDefined();
    expect(user.email).toBe('test@example.com');
  });

  it('should throw error when invalid email provided', async () => {
    const service = new UserService();
    await expect(service.createUser({
      email: 'invalid',
      password: 'secure123'
    })).rejects.toThrow('Invalid email');
  });
});
```

### Go
```go
// Prompt: "Write failing Go tests for a string validator"

func TestValidator_ValidateEmail(t *testing.T) {
    tests := []struct {
        name    string
        email   string
        wantErr bool
    }{
        {
            name:    "should accept valid email",
            email:   "user@example.com",
            wantErr: false,
        },
        {
            name:    "should reject invalid email",
            email:   "invalid",
            wantErr: true,
        },
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            v := NewValidator()
            err := v.ValidateEmail(tt.email)
            if (err != nil) != tt.wantErr {
                t.Errorf("ValidateEmail() error = %v, wantErr %v", err, tt.wantErr)
            }
        })
    }
}
```

## Coverage Thresholds

### Minimum Requirements
- Line coverage: 80%
- Branch coverage: 75%
- Critical path coverage: 100%

### How to Check Coverage
```bash
# JavaScript
npm test -- --coverage

# Python
pytest --cov=src --cov-report=html

# Go
go test -cover ./...

# Java
mvn test jacoco:report
```

## Common Pitfalls to Avoid

### ❌ Don't Do This
```javascript
// Writing implementation before tests
function add(a, b) {
  return a + b;
}

// Then writing passing tests
test('adds numbers', () => {
  expect(add(2, 3)).toBe(5); // Already passes!
});
```

### ✅ Do This Instead
```javascript
// Write failing test first
test('should add two numbers', () => {
  const calc = new Calculator();
  expect(calc.add(2, 3)).toBe(5); // Will fail - Calculator doesn't exist
});

// Then implement minimal code
class Calculator {
  add(a, b) {
    return a + b;
  }
}
```

## Validation Checklist

### After RED Phase
- [ ] Tests fail with clear error messages
- [ ] Failures are due to missing implementation
- [ ] All edge cases covered
- [ ] Test names are descriptive

### After GREEN Phase
- [ ] All tests pass
- [ ] Only necessary code written
- [ ] No over-engineering
- [ ] Coverage thresholds met

### After REFACTOR Phase
- [ ] Tests still green
- [ ] Code is cleaner
- [ ] No duplication
- [ ] Better structure

## Integration with Other Workflows

- **Feature Development**: Use this for new features
- **Bug Fixes**: Write failing test for bug, then fix
- **Refactoring**: Ensure tests exist before refactoring
- **Code Review**: Verify TDD process was followed

## Metrics to Track

- Time in RED phase
- Time in GREEN phase
- Time in REFACTOR phase
- Number of cycles
- Test coverage growth
- Code complexity trends

## Success Criteria

- ✅ 100% of code written test-first
- ✅ All tests continuously passing
- ✅ Coverage exceeds thresholds
- ✅ Code complexity within limits
- ✅ Zero defects in tested code
- ✅ Fast test execution

## Example: Complete TDD Session

**Initial Request:**
```
Implement a simple shopping cart using TDD with these features:
1. Add items
2. Remove items
3. Calculate total
4. Apply discounts
```

**Step 1 - RED:** "Write failing tests for shopping cart"
```javascript
describe('ShoppingCart', () => {
  test('should start with zero items', () => {
    const cart = new ShoppingCart();
    expect(cart.itemCount()).toBe(0);
  });

  test('should add item successfully', () => {
    const cart = new ShoppingCart();
    cart.addItem({ id: 1, price: 10 });
    expect(cart.itemCount()).toBe(1);
  });

  test('should calculate total correctly', () => {
    const cart = new ShoppingCart();
    cart.addItem({ id: 1, price: 10 });
    cart.addItem({ id: 2, price: 20 });
    expect(cart.getTotal()).toBe(30);
  });
});

// All tests fail - ShoppingCart doesn't exist
```

**Step 2 - GREEN:** "Implement minimal ShoppingCart to pass tests"
```javascript
class ShoppingCart {
  constructor() {
    this.items = [];
  }

  itemCount() {
    return this.items.length;
  }

  addItem(item) {
    this.items.push(item);
  }

  getTotal() {
    return this.items.reduce((sum, item) => sum + item.price, 0);
  }
}

// All tests now pass!
```

**Step 3 - REFACTOR:** "Refactor ShoppingCart for better design"
```javascript
class ShoppingCart {
  #items = [];

  get itemCount() {
    return this.#items.length;
  }

  addItem(item) {
    this._validateItem(item);
    this.#items.push(item);
  }

  calculateTotal() {
    return this.#items.reduce((sum, item) => sum + item.price, 0);
  }

  _validateItem(item) {
    if (!item.id || typeof item.price !== 'number') {
      throw new Error('Invalid item');
    }
  }
}

// Tests still green, but code is cleaner
```

## Related Patterns

- `tdd-red.md` - Detailed guidance on RED phase
- `tdd-green.md` - Detailed guidance on GREEN phase  
- `tdd-refactor.md` - Detailed guidance on REFACTOR phase
- `test-harness.md` - Test infrastructure setup
- `feature-development.md` - Full feature implementation workflow

## Quick Reference

**To start TDD:**
```
"Let's implement [feature] using TDD. First, write failing tests."
```

**To continue to GREEN:**
```
"Now implement minimal code to pass these tests."
```

**To refactor:**
```
"Refactor this code while keeping tests green."
```

**To add more tests:**
```
"Add tests for these edge cases: [list cases]"
```
