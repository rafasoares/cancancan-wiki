What do you do when permissions you defined in the Ability class don't seem to be working properly? First try to duplicate this problem in the `rails console` or better yet, see [[Testing Abilities]].

## Debugging Member Actions

```ruby
# in rails console or test
user = User.first # fetch any user you want to test abilities on
project = Project.first # any model you want to test against
ability = Ability.new(user)
ability.can?(:create, project) # see if it returns the expected behavior for that action
```

Note: this assumes that the model instance is being loaded properly. If you are only using `authorize_resource` it will not have an instance to work with so it will use the class.

```ruby
ability.can?(:create, Project)
```

## Debugging `index` Action

```ruby
# in rails console or test
user = User.first # fetch any user you want to test abilities on
ability = Ability.new(user)
ability.can?(:index, Project) # see if user can access the class
Project.accessible_by(ability) # see if returns the records the user can access
```

## Logging `AccessDenied` Exception

If you think the `CanCan::AccessDenied` exception is being raised and you are not sure why, you can log this behavior to help debug what is triggering it.

```ruby
# in ApplicationController
rescue_from CanCan::AccessDenied do |exception|
  logger.debug "Access denied on #{exception.action} #{exception.subject.inspect}"
  # ...
end
```

## Issue Tracker

If you are still unable to resolve the issue, please post on the [[issue tracker|https://github.com/ryanb/cancan/issues]]