You can use CanCan with controllers that do not follow the traditional show/new/edit/destroy actions, however you cannot use the `load_and_authorize_resource` method. Instead you will need to `authorize!` each action manually.

For example, let's say we have a controller which does some miscellaneous administration tasks such as rolling log files. We can use the `authorize!` method here.

```ruby
class AdminController < ActionController::Base
  def roll_logs
    authorize! :roll, :logs
    # roll the logs here
  end
end
```

And then authorize that in the `Ability` class.

```ruby
can :roll, :logs if user.admin?
```

Notice you can pass a symbol as the second argument to both `authorize!` and `can`. It doesn't have to be a model class or instance. Generally the first argument is the "action" one is trying to perform and the second argument is the "subject" the action is being performed on. It can be anything.