Sometimes you need to restrict which records are returned from the database based on what the user is able to access. This can be done with the `accessible_by` method on any Active Record model. Simply pass it the current ability to find only the records which the user is able to `:read`.

```ruby
@articles = Article.accessible_by(current_ability)
```

You can change the action by passing it as the second argument. Here we find only the records the user has permission to update.

```ruby
@articles = Article.accessible_by(current_ability, :update) 
```

This is an Active Record scope so other scopes and pagination can be chained onto it.

As of CanCan 1.3, this will work with multiple `can` calls which allows you to define complex permission logic and have it translate properly to SQL. Special thanks to "funny-falcon":http://github.com/funny-falcon for this feature.

```ruby
# in Ability
can :manage, User, :id => 1
can :manage, User, :manager_id => 1
cannot :manage, User, :self_managed => true
# translates to "not (self_managed = 't') AND ((manager_id = 1) OR (id = 1))"
```

It will raise an exception if any requested model's ability definition is defined using just block. To use it with block conditions, you should define SQL fragment as well (look for examples in [[Defining Abilities with Hashes]] and [[Defining Abilities with Blocks]])(*development version at the moment*).

If you are using something other than Active Record you can fetch the conditions hash directly from the current ability.

```ruby
current_ability.query(:read, Article).conditions
```
