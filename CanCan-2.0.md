My ultimate goal in CanCan 2.0 is to make the behavior more intuitive. I've seen times in the issue tracker where it is used in a way it wasn't intended, or cases it authorized access when they didn't think it should. I'm taking all of this into account.

The biggest change is that basic controller action authorization will be handled by default. This means everything will be locked down with one simple `enable_authorization` call. The `load_and_authorize_resource` behavior is still there, but now only necessary if needing to check permission on resource instances.

This means abilities will be more focused on controller actions then resource classes, but this doesn't mean resources are being left in the dust. Another big feature is that you can add fine-grain permissions on specific resource attributes. See the Resource Attributes section below.


# README

**CanCan 2.0 is still in very early development.** Currently none of this documentation works, but I am writing the [readme first](http://tom.preston-werner.com/2010/08/23/readme-driven-development.html). See the [issue tracker](https://github.com/ryanb/cancan/issues) for progress on the implementation and feedback.

## Setup

First add authentication if you haven't already. This can be done through Devise, Authlogic, etc. The only thing CanCan requires by default is a `current_user` method in the controller.

To install CanCan, add it to your Gemfile and run the `bundle` command.

```ruby
gem "cancan", "2.0" # not yet available
```

Next generate an Ability class, this is where your permissions will be defined.

```bash
rails g cancan:ability
```

Then enable authorization in your ApplicationController or any controller you want authorization to happen in.

```ruby
class ApplicationController < ActionController::Base
  enable_authorization
end
```

This will add an authorization check locking down every controller action. If you try visiting a page `CanCan::Unauthorized` exception will be raised since you have not granted the user ability to access it. You can customize the behavior of this exception by passing a block.

```ruby
enable_authorization do |exception|
  redirect_to root_url, :alert => exception.message
end
```

Here it will redirect the user to the home page with an alert message when unauthorized. If you do this, make sure the user has permission to access the home page.


## Defining Abilities

You grant access to controller actions through the `Ability` class. The `current_user` is passed into the `initialize` method allowing you to define permissions based on user attributes.

```ruby
def initialize(user)
  if user
    can :access, :all
  else
    can :access, :home
  end
end
```

As you can see here, if a logged in user exists he can access the entire application, but guest users can only access the the HomeController.

The first argument to `can` is the name of the controller action. Using `:access` here will allow access to all actions on that controller. The second argument is the name of the controller. Using `:all` here will represent all controllers. Either one can be an array to represent multiple actions and controllers.

```ruby
can [:create, :update], [:posts, :comments]
```

Here the user will be able to create and update both posts and comments. You don't need to mention the `new` and `edit` actions because CanCan includes some default aliases. See the [[Aliases]] page for details.

You can check permissions in any controller or view using the `can?` method.

```rhtml
<% if can? :create, :comments %>
  <%= link_to "New Comment", new_comment_path %>
<% end %>
```

Here the link will only show up if one can create comments.


## Resources

What if you need to change authorization based on a model's attributes? You can do so by passing a hash of conditions as the last argument to `can`. For example, if you want to only allow one to access projects which he owns you can set the `:user_id` option.

```ruby
can :access, :projects, :user_id => user.id
```

A block can also be used for complex condition checks just like in CanCan 1, but here it is not necessary.

If you try visiting any of the project pages at this point you will see a `CanCan::InsufficientCheck` exception is raised. This is because the default authorization has no way to check permissions on the `@project` instance. You can check permissions on an object manually using the `authorize!` method.

```ruby
def edit
  @project = Project.find(params[:id])
  authorize! :edit, @project
end
```

However this can get tedious. Instead CanCan provides a `load_and_authorize_resource` method to load the `@project` instance in every controller action and authorize it.

```ruby
class ProjectsController < ApplicationController
  load_and_authorize_resource
  def edit
    # @project already loaded here and authorized
  end
end
```

The `index` (and other collection actions) will load the `@projects` instance which automatically limits the projects the user is allowed to access. This is a scope so you can make further calls to `where` to limit what is returned from the database.

You can check permissions on instances using the `can?` method as well.

```rhtml
<%= link_to "Edit Project", edit_project_path if can? :update, @project %>
```

Here it will only show the edit link if the `user_id` matches.


## Resource Attributes

It is possible to define permissions on specific resource attributes. For example, if you want to allow a user to only update the name and priority of a project, pass that as the third argument to `can`.

```ruby
can :update, :projects, [:name, :priority]
```

If you use this in combination with `load_and_authorize_resource` it will ensure that only those two attributes exist in `params[:project]` when updating the project.

You can combine this with a hash of conditions. For example, here the user can only update the price if the product isn't discontinued.

```ruby
can :update, :product, :price, :discontinued => false
```

You can check permissions on specific attributes to determine what to show in the form.

```rhtml
<%= f.text_field :name if can? :update, @project, :name %>
```

If you use this everywhere it will not be necessary to use `attr_accessible` in your models.

# What do you think?

Please submit any feedback to the [issue tracker](https://github.com/ryanb/cancan/issues). If you want want CanCan 2.0 to work a certain way, now is the time to request it.
