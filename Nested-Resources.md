Let's say we have a nested resources set up in our routes.

```ruby
resources :projects do
  resources :tasks
end
```

We can then tell CanCan to load the project and then load the task through that.

```ruby
class TasksController < ApplicationController
  load_and_authorize_resource :project
  load_and_authorize_resource :task, :through => :project
end
```

This will fetch the project using `Project.find(params[:project_id])` on every controller action, save it in the `@project` instance variable, and authorize it using the `:read` action to ensure the user has the ability to access that project. If you don't want to do the authorization you can simply use `load_resource`. The task is then loaded through the `@project.tasks` association.

If the resource name (`:project` in this case) does not match the controller then it will be considered a parent resource. You can manually specify parent/child resources using the `:parent => false` option.


## Singleton Resource

What if each project only had one task through a `has_one` association? To set up singleton resources you can use the `:singleton` option.

```ruby
class TasksController < ApplicationController
  load_and_authorize_resource :project
  load_and_authorize_resource :task, :through => :project, :singleton => true
end
```

It will then use the `@project.task` and `@project.build_task` methods for fetching and building respectively.


## Polymorphic Associations

Let's say tasks can either be assigned to a Project or an Event through a polymorphic association. An array can be passed into the `:through` option and it will use the first one it finds.

<pre>
load_and_authorize_resource :project
load_and_authorize_resource :event
load_and_authorize_resource :task, :through => [:project, :event]
</pre>

Here it will check for the existence of the `@project` or `@event` variable and whichever one exists it will fetch the task through that.


## Accessing Parent in Ability

Sometimes the child permissions are closely tied to the parent resource. For example, if there is a `user_id` column on Project, one may want to only allow access to tasks if the user owns their project.

This will happen automatically due to the `@project` instance being authorized in the nesting. However it's still a good idea to restrict the tasks separately. You can do so by going through the project association.

```ruby
# in Ability
can :manage, Task, :project => { :user_id => user.id }
```

This means you will need to have a project tied to the tasks which you pass into here. For example, if you are checking if the user has permission to create a new task, do that by building it through the project.

```rhtml
<% if can? :create, @project.tasks.build %>
```

Basically, if you need to access the parent resource, be sure to pass it in with the child instance.