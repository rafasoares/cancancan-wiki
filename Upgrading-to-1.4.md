CanCan 1.4 is one of the largest updates yet. Here are the highlights, but check out the [[CHANGELOG|http://github.com/ryanb/cancan/blob/master/CHANGELOG.rdoc]] for the full list.

## Loading `index` action

A collection of resources is automatically loaded in the `index` action using `accessible_by`.

```ruby
class ProductsController < ApplicationController
  load_resource
  def index
    # @products automatically set to Product.accessible_by(current_ability)
  end
end
```

Since this is a scope you can either ignore it or build on it further.

```ruby
def index
  @products = @products.paginate(:page => params[:page])
end
```

The `@products` variable will not be set if `Product` does not respond to `accessible_by` (for example if you aren't using Active Record). It will also not be set if you are using blocks in any of the can definitions because that is incompatible with `accessible_by`.


## Blocks in `can` definitions

There are many backward-incompatible changes regarding blocks in `can` definitions.

The block is no longer triggered if a class is passed in to the `can?` check. This makes the behavior consistent with the conditions hash and means you no longer need to check for nil.

```ruby
can :read, Project do |project|
  project.in_group? :foo
end
can? :read, Project # returns true without triggering block
can? :read, @project # triggers block
```

The action and class are no longer passed to the block when using `:manage` or `:all` arguments. Only the object is passed in. Here are a couple examples:

```ruby
can :manage, :Project do |project|
  # ...
end

can :manage, :all do |object|
  # ...
end
```

If you need the old behavior (like when defining [[Abilities in Database]]), you can call `can` without any arguments and everything will be passed to the block. This block will also be triggered with every check no matter if only a class is passed in to the `can?` check.

```ruby
can do |action, subject_class, subject|
  # ...
end
```


## Internationalization

The AccessDenied messages can be customized through internationalization. For example:

```yaml
# in locales/en.yml
en:
  unauthorized:
    manage:
      all: "Not authorized to %{action} %{subject}."
      user: "Not allowed to manage other user accounts."
    update:
      project: "Not allowed to update this project."
```

Notice `manage` and `all` can be used to generalize the subject and actions. Also `%{action}` and `%{subject}` can be used as variables in the message.


## Ensure Authorization is Performed

If you want air-tight authorization to be certain you don't forget it in some controller action, add `check_authorization` to your `ApplicationController` or any other controller.

```ruby
class ApplicationController < ActionController::Base
  check_authorization
end
```

This will add an `after_filter` to ensure authorization takes place in every inherited controller action. If not it will raise an exception. You can skip this check by adding `skip_authorization` to that controller. Both of these methods take the same arguments as `before_filter` so you can exclude certain actions.


## Nested Resources

TODO


## Inherited Resources

It will automatically detect if you are using Inherited Resources and load the resource through that. The `load_resource` call is still necessary however to support additional loading such as in the `index` action.

```ruby
class ProjectsController < InheritedResources::Base
  load_and_authorize_resource
end
```

## Running Specs

If you want to contribute to this project, a `Gemfile` has been added to make it easy to start developing and running the specs. Just run `bundle` and `rake` to run the specs. This currently does not work in Ruby 1.9.

