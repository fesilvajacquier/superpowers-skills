---
name: Rails Testing with Minitest
description: Rails-specific testing guidance using Minitest, fixtures, and unit-first approach
when_to_use: when writing tests for Rails projects
version: 1.0.0
languages: ruby
---

# Rails Testing with Minitest

## Overview

Rails testing with strong preference for unit tests, fixtures over factories, and disposable tests during development.

**Core principle:** Unit tests are mandatory. System tests only for critical flows. Fixtures over factories.

## Testing Hierarchy

### Unit Tests (Default)

**Always write unit tests for:**
- Models (validations, associations, methods)
- Helpers
- Mailers
- Jobs
- Services/POROs

**Example structure:**
```ruby
class UserTest < ActiveSupport::TestCase
  test "validates presence of email" do
    user = User.new(name: "Test")
    assert_not user.valid?
    assert_includes user.errors[:email], "can't be blank"
  end

  test "full_name combines first and last name" do
    user = users(:john) # Using fixture
    assert_equal "John Doe", user.full_name
  end
end
```

### System Tests (Rare - Specific Criteria Only)

**Only write system tests for:**
- Critical user flows requiring JavaScript interactions (e.g., live search, dynamic forms)
- Full authentication flows (signup, login, password reset)
- Payment processing with external services
- File uploads with preview functionality

**Why avoid system tests:**
- Slow execution
- Brittle (CSS/HTML changes break tests)
- Hard to debug
- Most behavior testable at unit level

### Fixtures Over Factories

**Always prefer fixtures:**

```yaml
# test/fixtures/users.yml
john:
  email: john@example.com
  name: John Doe
  created_at: <%= 1.day.ago %>

jane:
  email: jane@example.com
  name: Jane Smith
```

**In tests:**
```ruby
user = users(:john)
```

**Why fixtures:**
- Loaded once per test suite (fast)
- Defined in one place (DRY)
- Forces thinking about data structure
- No factory dependency

## Disposable Tests

**Mark tests as disposable when:**
- Exploring behavior of unfamiliar code
- Testing intermediate refactoring states
- Understanding how a gem/library works

**Syntax:**
```ruby
# TODO: disposable - remove after understanding ActiveStorage callbacks
test "exploring attachment lifecycle" do
  # temporary test code
end
```

**Commit message when removing:**
```bash
git commit -m "Remove disposable tests for [feature]"
```

## Running Tests

**Single test:**
```bash
rails test test/models/user_test.rb:42
```

**All unit tests:**
```bash
rails test
```

**System tests only:**
```bash
rails test:system
```

## References

- Follow commit format from `/commit` slash command
- Use TDD workflow from skills/testing/test-driven-development
- Avoid anti-patterns from skills/testing/testing-anti-patterns
