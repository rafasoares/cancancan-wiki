You can use the `load_and_authorize_resource` method in your controller to load the resource into an instance variable and authorize it for each of the 7 RESTful actions.

```ruby
class CommentsController < ActionController::Base
  load_and_authorize_resource
end
```

This is the same as calling `load_resource` and `authorize_resource` because they are two separate steps and you can choose to use one or the other.

```ruby
class CommentsController < ActionController::Base
  load_resource
  authorize_resource
end
```

This will set up a before filter for every action in the controller. For the `new` or `create` action it will build it with `Comment.new(params[:comment])`. Otherwise it will do `Comment.find(params[:id])` if that `:id` param exists.

Next it will authorize the resource by passing the controller action and `@comment` instance into the `authorize!` check. If the instance doesn't exist (such as on the index action) the `Comment` class will be used.

The resource will only be loaded into an instance variable if it hasn't been already. This allows you to easily override how the loading happens in a separate `before_filter`.

```ruby
class BooksController < ApplicationController
  before_filter :find_book_by_permalink, :only => :show
  load_and_authorize_resource

  private

  def find_book_by_permalink
    @book = Book.find_by_permalink!(params[:id])
  end
end
```

Here the `@book` instance variable is already set so it will not be loaded for that action, only authorized. Alternatively you can use the `:find_by` option to do this.

```ruby
load_and_authorize_resource :find_by => :permalink
```

For additional information see the `load_resource` and `authorize_resource` methods in the [[RDoc|http://rdoc.info/projects/ryanb/cancan]].

Also see [[Nested Resources]] and [[Non RESTful Controllers]].