The `CanCan::AccessDenied` exception is raised when calling `authorize!` in the controller and the user is not able to perform the given action. A message can optionally be provided.

```ruby
authorize! :read, Article, :message => "Unable to read this article."
```

This exception can also be raised manually if you want more custom behavior.

```ruby
raise CanCan::AccessDenied.new("Not authorized!", :read, Article)
```

The message can also be customized through internationalization.

```yaml
# in config/locales/en.yml
en:
  unauthorized:
    manage:
      all: "Not authorized to %{action} %{subject}."
      user: "Not allowed to manage other user accounts."
    update:
      project: "Not allowed to update this project."
```

Notice `manage` and `all` can be used to generalize the subject and actions. Also `%{action}` and `%{subject}` can be used as variables in the message.

You can catch the exception and modify its behavior in the `ApplicationController`. For example here we set the error message to a flash and redirect to the home page.

```ruby
class ApplicationController < ActionController::Base
  rescue_from CanCan::AccessDenied do |exception|
    flash[:alert] = exception.message
    redirect_to root_url
  end
end
```

The action and subject can be retrieved through the exception to customize the behavior further.

```ruby
exception.action # => :read
exception.subject # => Article
```

The default error message can also be customized through the exception. This will be used if no message was provided.

```ruby
exception.default_message = "Default error message"
exception.message # => "Default error message"
```

If you prefer to return the 403 Forbidden HTTP code, create a `public/403.html` file and write a rescue_from statement like this example in `ApplicationController`:

```ruby
class ApplicationController < ActionController::Base
  rescue_from CanCan::AccessDenied do |exception|
    render :file => "#{RAILS_ROOT}/public/403.html", :status => 403
  end
end 
```

`403.html` must be pure HTML, CSS, and JavaScript--not a template. The fields of the exception are not available to it.

See [[Authorization in Web Services]] for rescuing exceptions for XML responses.