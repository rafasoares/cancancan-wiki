What if you want to determine the abilities of a `User` record that is not the `current_user`? Maybe we want to see if another user can update an article.

```ruby
some_user.ability.can? :update, @article
```

You can easily add an `ability` method in the `User` model.

```ruby
def ability
  @ability ||= Ability.new(self)
end
```

I also recommend adding delegation so `can?` can be called directly from the user.

```ruby
class User < ActiveRecord::Base
  delegate :can?, :cannot?, :to => :ability
  # ...
end

some_user.can? :update, @article
```

Finally, if you're using this approach, it's best to override the `current_ability` method in the `ApplicationController` so it uses the same method.

```ruby
def current_ability
  current_user.ability
end
```

The downside of this approach is that [[Accessing Request Data]] is not as easy, so it depends on the needs of your application.

A different approach is taken by [cantango](https://github.com/kristianmandrup/cantango) a gem that builds on top of cancan, and exposes methods such as `#user_can?` and `#admin_can?` for each devise user. These methods wrap the `#current_[user_type]` methods exposed by devise.