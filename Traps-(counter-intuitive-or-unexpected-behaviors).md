Here's a list of common counter-intuitive behaviors.

### Abilities are not defined and checked the same way

You have to use a **class** when defining abilities but an **instance** when checking abilities.

This means that using `can :read, Article.find(2)` or `can? :create, Article` is wrong.

``` ruby
# defining abilities
can :read, @article        # bad
can :read, Article.find(2) # bad
can :manage, Item          # good, user can manage any items
can :create, Article       # good, user can create any articles
can :read, Article, id: 2  # good, user can read the article with id 2

# checking abilities
can? :destroy, User                # bad
can? :create, Article              # bad, this does NOT mean "can create an article"
can? :create, Article.new          # good, the user can create an article
can? :create, @user.articles.build # good, the user can create an article for this user
can? :read, @article               # good, the user can read this specific article
can? :read, Article.find(2)        # good, the user can read the article with id 2
```

### load_and_authorize_resource with :manage

`load_and_authorize_resource` with a rule like `can :manage, Article, id: 23` will allow rendering the `new` method of the ArticlesController. The rule really means _"you can manage any Article resource but it has to have an ID of 23"_, which includes creating a new Article with the id set to 23.

Thus `load_and_authorize_resource` will initialize a model in the `:new` action and set its id to 23, and happily render the page. Saving will not work tho.

The correct intended rule to avoid `new` being allowed would be:

``` ruby
can [:read, :update, :destroy], Article, id: 23
```