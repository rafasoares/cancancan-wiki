Sometimes you need to restrict which records are returned from the database based on what the user is able to access. This can be done with the `accessible_by` method on any Active Record model. Simply pass it the current ability to find only the records which the user is able to `:index`.

```ruby
# current_ability is a method made available by CanCan to your controllers extending ActionController::Base
@articles = Article.accessible_by(current_ability)
```

**Note:** As of 1.4 this is done automatically by `load_resource` for the index action, so it rarely needs to be done manually.

You can change the action by passing it as the second argument. Here we find only the records the user has permission to update.

```ruby
@articles = Article.accessible_by(current_ability, :update) 
```

If you want to use the current controller's action, make sure to call `to_sym` on it:

```ruby
@articles = Article.accessible_by(current_ability, params[:action].to_sym)
```

This is an Active Record scope so other scopes and pagination can be chained onto it.

As of CanCan 1.3, this will work with multiple `can` calls which allows you to define complex permission logic and have it translate properly to SQL. Special thanks to [[funny-falcon|https://github.com/funny-falcon]] for this feature.

```ruby
# in Ability
# assuming user.id == 1
can :manage, User, :manager_id => user.id
cannot :manage, User, :self_managed => true
can :manage, User, :id => user.id
# translates to "(id = 1) OR (not (self_managed = 't') AND (manager_id = 1))"
# as if it is read from bottom to top
# "user could manage himself, for other he could not manage self_managed users, otherwise he could manage his employees"
```

It will raise an exception if any requested model's ability definition is defined using just block. As of 1.4, you could define SQL fragment in addition to block (look for examples in [[Defining Abilities with Blocks]]).

If you are using something other than Active Record you can fetch the conditions hash directly from the current ability.

```ruby
current_ability.model_adapter(TargetClass, :target_action).conditions
```