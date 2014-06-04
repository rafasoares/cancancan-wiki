Hello,

Here's a list of common counter-intuitive behaviors.

- `load_and_authorize_resource` with a rule like `can :manage, Model, id: 23` will allow rendering the `new` method of the ModelsController. The rule really means _"you can manage any Model resource but it has to have an ID of 23"_, which includes creating a new Model with the id set to 23. Thus `load_and_authorize_resource` will initialize a model in the `:new` action and set its id to 23, and happilly render the page. Saving will not work tho.
- You can check abilites like `can? :read, Article.find(2)` but you **cannot** define abilities with `can :read, Article.find(2)`. You have to define abilities with the class, like `can :read, Article, id: 2` instead.