As of 1.4, if you want to be certain authorization is not forgotten in some controller action, add `check_authorization` to your `ApplicationController`.

```ruby
class ApplicationController < ActionController::Base
  check_authorization
end
```

This will add an `after_filter` to ensure authorization takes place in every inherited controller action. If not it will raise an exception. You can skip this check by adding `skip_authorization_check` to that controller. Both of these methods take the same arguments as `before_filter` so you can exclude certain actions with `:only` and `:except`.