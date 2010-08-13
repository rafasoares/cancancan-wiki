CanCan makes two assumptions about your application.

* You have an `Ability` class which defines the permissions.
* You have a `current_user` method in the controller which returns the current user model.

You can override both of these by defining the `current_ability` method in your `ApplicationController`. The current method looks like this.

```ruby
def current_ability
  @current_ability ||= Ability.new(current_user)
end
```

The `Ability` class and `current_user` method can easily be changed to something else.

```ruby
# in ApplicationController
def current_ability
  @current_ability ||= AccountAbility.new(current_account)
end
```

That's it! See [[Accessing Request Data]] for a more complex example of what you can do here.