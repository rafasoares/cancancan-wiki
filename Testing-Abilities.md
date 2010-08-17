It can be difficult to thoroughly test user permissions at the functional/integration level because there are often many branching possibilities. Since CanCan handles all permission logic in a single `Ability` class this makes it easy to have a solid set of unit test for complete coverage.

The `can?` method can be called directly on any `Ability` (like you would in the controller or view) so it is easy to test permission logic.

```ruby
test "user can only destroy projects which he owns" do
  user = User.create!
  ability = Ability.new(user)
  assert ability.can?(:destroy, Project.new(:user => user))
  assert ability.cannot?(:destroy, Project.new)
end
```


## RSpec

If you are testing the `Ability` class through RSpec there is a `be_able_to` matcher available. This checks if the `can?` method returns `true`.

```ruby
require "cancan/matchers"
# ...
ability.should be_able_to(:destroy, Project.new(:user => user))
ability.should_not be_able_to(:destroy, Project.new)
```


## Cucumber

By default, Cucumber will ignore the `rescue_from` call in the `ApplicationController` and report the `CanCan::AccessDenied` exception when running the features. If you want full integration testing you can change this behavior so the exception is caught by Rails. You can do so by setting this in the `env.rb` file.

```ruby
# in features/support/env.rb
ActionController::Base.allow_rescue = true
```

Alternatively, if you don't want to allow rescue on everything, you can tag individual scenarios with `@allow-rescue` tag.

```ruby
@allow-rescue
Scenario: Update Article
```

Here the `rescue_from` block will take effect only in this scenario.