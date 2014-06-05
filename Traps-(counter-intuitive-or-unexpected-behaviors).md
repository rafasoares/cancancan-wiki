Here's a list of common counter-intuitive behaviors.

## Abilities are not defined the same way they are checked

You can check abilites like `can? :read, Article.find(2)` but you **cannot** define abilities with `can :read, Article.find(2)`. You have to define abilities on the class, like `can :read, Article, id: 2` instead. You **cannot** check abilities with `can? :create, Article`, you have to use `can? :create, Article.new` instead.

``` ruby
# defining abilities
can :read, @article           # bad
can :read, Article.find(2)    # bad
can :read, Article, id: 2     # good

# checking abilities
can? :create, Article         # bad
can? :create, Article.new     # good
can? :read, Article.find(2)   # good
```

## load_and_authorize_resource

`load_and_authorize_resource` with a rule like `can :manage, Article, id: 23` will allow rendering the `new` method of the ArticlesController. The rule really means _"you can manage any Article resource but it has to have an ID of 23"_, which includes creating a new Article with the id set to 23. Thus `load_and_authorize_resource` will initialize a model in the `:new` action and set its id to 23, and happily render the page. Saving will not work tho.
