You can use the `load_and_authorize_resource` method in your controller to load the resource into an instance variable and authorize it for each of the 7 RESTful actions.

```ruby
class ProductsController < ActionController::Base
  load_and_authorize_resource
end
```

This is the same as calling `load_resource` and `authorize_resource` because they are two separate steps and you can choose to use one or the other.

```ruby
class ProductsController < ActionController::Base
  load_resource
  authorize_resource
end
```

**NOTE:** It is currently not possible to remove this load/authorize behavior after it has been set on a controller. Therefore, I recommend you set it separately for each controller. Don't apply it to the `ApplicationController`. If you want to be certain every controller has authorization, see [[Ensure Authorization]].

See [[Non RESTful Controllers]] for how to authorize other controllers.


## Choosing Actions

By default this will apply to **every action** in the controller even if it is not one of the 7 RESTful actions. The action name will be passed in when authorizing. For example, if we have a `discontinue` action on `ProductsController` it will have this behavior.

<pre>
class ProductsController < ActionController::Base
  load_and_authorize_resource
  def discontinue
    # Automatically does the following:
    # @product = Product.find(params[:id])
    # authorize! :discontinue, @product
  end
end
</pre>

You can specify which actions to effect using the `:except` and `:only` options, just like a `before_filter`.

```ruby
load_and_authorize_resource :only => [:index, :show]
```


## load_resource

### index action

As of 1.4 the index action will load the collection resource using `accessible_by`.

```ruby
def index
  # @products automatically set to Product.accessible_by(current_ability)
end
```

Since this is a scope you can build on it further.

```ruby
def index
  @products = @products.paginate(:page => params[:page])
end
```

The `@products` variable will not be set if `Product` does not respond to `accessible_by` (for example if you aren't using Active Record). It will also not be set if you are using blocks in any of the `can` definitions because that is incompatible with `accessible_by`.

### show, edit, update and destroy actions

These member actions simply fetch the record directly.

```ruby
def show
  # @product automatically set to Product.find(params[:id])
end
```

### new and create actions

As of 1.4 these builder actions will initialize the resource with the attributes in the hash conditions. For example, if we have this `can` definition.

```ruby
can :manage, Product, :discontinued => false
```

Then the product will be built with that attribute in the controller.

```ruby
@product = Product.new(:discontinued => false)
```

This way it will pass authorization when the user accesses the `new` action.

The attributes are then overridden by whatever is passed by the user in `params[:product]`.

### Custom class

If the model class is namespaced or different than the controller name you will need to specify the `:class` option.

<pre>
class ProductsController &lt; ApplicationController
  load_and_authorize_resource :class => "Store::Product"
end
</pre>


### Custom find

If you want to fetch a resource by something other than `id` it can be done so using the `find_by` option.

```ruby
load_resource :find_by => :permalink # will use find_by_permlink!(params[:id])
```

### Override loading

The resource will only be loaded into an instance variable if it hasn't been already. This allows you to easily override how the loading happens in a separate `before_filter`.

```ruby
class BooksController < ApplicationController
  before_filter :find_published_book, :only => :show
  load_and_authorize_resource

  private

  def find_published_book
    @book = Book.released.find(params[:id])
  end
end
```

It is important that any custom loading behavior happens **before** the call to `load_and_authorize_resource`. If you have `authorize_resource` in your `ApplicationController` then you need to use `prepend_before_filter` to do the loading in the controller subclasses so it happens before authorization.

## authorize_resource

Adding `authorize_resource` will make a before filter which calls `authorize!`, passing the resource instance variable if it exists. If the instance variable isn't set (such as in the index action) it will pass in the class name. For example, if we have a `ProductsController` it will do this before each action.

```ruby
authorize!(params[:action], @product || Product)
```

## More info

For additional information see the `load_resource` and `authorize_resource` methods in the [[RDoc|http://rdoc.info/projects/ryanb/cancan]].

Also see [[Nested Resources]] and [[Non RESTful Controllers]].