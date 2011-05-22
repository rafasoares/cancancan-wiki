You can bypass Cancan 2.0's authorization for Devise controllers similar to Cancan 1.6:

```ruby
class ApplicationController < ActionController::Base
  protect_from_forgery
  enable_authorization :unless => :devise_controller?
end
```