By default, CanCan works best when the administration functionality is in the same controller as the public site. This way each resource only has one controller and set of actions it needs to handle permissions on.

However some sites work better when the administration section has its own separate set of controllers inside an `Admin` namespace such as `Admin::ArticlesController`. This means there are two controllers handling the same resource. Each controller will likely need different permission logic. The problem is that the `Ability` model knows nothing about the `Admin` namespace.

If you go with an `Admin` namespace I recommend creating a base controller which all admin controllers inherit from. This is often a good thing to do with any namespace because it can help refactor out common functionality that exists in the controllers in that namespace.

```ruby
# in controllers/admin/base_controller.rb
module Admin
  class BaseController < ApplicationController
    # common behavior goes here ...
  end
end
```

Then use this as the superclass for each of those controllers.

```ruby
# in controllers/admin/articles_controller.rb
module Admin
  class ArticlesController < BaseController
    # ...
  end
end
```

Now several possibilities open up, and the one to go with depends on how complex the authorization needs to be in this admin namespace. If all you need to do is verify `current_user.admin?` then you may want to skip CanCan altogether and use a simple before filter.

```ruby
# in controllers/admin/base_controller.rb
before_filter :verify_admin
private
def verify_admin
  redirect_to root_url unless current_user.admin?
end
```

There's really no need to use CanCan here because all the behavior is the same. However if you have various levels of admins which needs to access different things then that's a reason to use CanCan. In that case I recommend creating a separate `Ability` class.

```ruby
# in models/admin_ability.rb
class AdminAbility
  include CanCan::Ability
  def initialize(user)
    # define admin abilities here ....
  end
end
```

Then use this `AdminAbility` for admin controllers.

```ruby
# in admin/base_controller.rb
def current_ability
  @current_ability ||= ::AdminAbility.new(current_user)
end
```

Now all admin permission logic will be handled in its own `AdminAbility` class and everything else will be in the normal `Ability` class.