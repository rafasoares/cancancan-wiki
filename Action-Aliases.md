You will usually be working with four actions when defining and checking permissions: `:read`, `:create`, `:update`, `:destroy`. These aren't the same as the 7 RESTful actions in Rails. CanCan automatically adds some default aliases for mapping those actions.

```ruby
alias_action :index, :show, :to => :read
alias_action :new, :to => :create
alias_action :edit, :to => :update
```

Notice the `edit` action is aliased to `update`. This means if the user is able to update a record he also has permission to edit it. You can define your own aliases in the `Ability` class.

```ruby
alias_action :update, :destroy, :to => :modify
can :modify, Comment
can? :update, Comment # => true
```

The `alias_action` method is an instance method and usually called in `initialize`. See [[Custom Actions]] for information on adding other actions.