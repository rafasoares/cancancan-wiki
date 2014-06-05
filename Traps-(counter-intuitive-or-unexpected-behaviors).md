Here's a list of common counter-intuitive behaviors.

### Abilities are not defined and checked the same way

You have to use a **class** when defining abilities but an **instance** when checking abilities.

This means that using `can :read, Article.find(2)` is wrong and that `can? :create, Article` doesn't do what you expect it to do.

| N | Question                                | Ability                       | Is checked with                                         |
|---|-----------------------------------------|-------------------------------|---------------------------------------------------------|
| 1 | "User can update the article with id 2" | can(:update, Article, id: 2)  | can?(:update, Article.find(2))                          |
| 2 | "User can update any article"           | can(:update, Article)         | Article.reject{ &#124;a&#124; can?(:update, a) }.empty? |
| 3 | "User can update one of the articles"   | 1 or 2                        | can?(:update, Article)                                  |
| 4 | (invalid)                               | can(:update, Article.find(2)) | -                                                       |


### load_and_authorize_resource with :manage

Using `load_and_authorize_resource` with a rule like `can :manage, Article, id: 23` will allow rendering the `new` method of the ArticlesController, which is unexpected because this rule naively reads as _"the user can manage the existing article with id 23"_, which should have nothing to do with creating new articles.

But in reality the rule means _"the user can manage any article object with an id field set to 23"_, which includes creating a new Article with the id set to 23 like `Article.new(id: 23)`.

Thus `load_and_authorize_resource` will initialize a model in the `:new` action and set its id to 23, and happily render the page. Saving will not work tho.

The correct intended rule to avoid `new` being allowed would be:

``` ruby
can [:read, :update, :destroy], Article, id: 23
```