If you want to be certain authorization is not forgotten in some controller action, add `check_authorization` to your `ApplicationController`.

```ruby
class ApplicationController < ActionController::Base
  check_authorization
end
```

This will add an `after_filter` to ensure authorization takes place in every inherited controller action. If no authorization happens it will raise a `CanCan::AuthorizationNotPerformed` exception. You can skip this check by adding `skip_authorization_check` to that controller. Both of these methods take the same arguments as `before_filter` so you can exclude certain actions with `:only` and `:except`.

```ruby
class UsersController < ApplicationController
  skip_authorization_check :only => [:new, :create]
  # ...
end
```

## Rails Engines

This can cause issues with Rails Engines such as [[Devise|https://github.com/plataformatec/devise]] because authorization will not happen there. The best thing to do is [[override|https://github.com/plataformatec/devise/wiki/How-To:-Redirect-after-registration-(sign-up)]] the engine controller and add `skip_authorization_check` or perform any other authorization you see fit.

