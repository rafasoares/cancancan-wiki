As of 1.4, the `load_and_authorize_resource` call will automatically detect if you are using [[Inherited Resources|http://github.com/josevalim/inherited_resources]] and load the resource through that. The `load` part in CanCan is still necessary since Inherited Resources does lazy loading. This will also ensure the behavior is identical to normal loading.

```ruby
class ProjectsController < InheritedResources::Base
  load_and_authorize_resource
end
```

if you are doing nesting you will need to mention it in both Inherited Resources and CanCan.

```ruby
class TasksController < InheritedResources::Base
  belongs_to :project
  load_and_authorize_resource :project
  load_and_authorize_resource :task, :through => :project
end
```
_Please note that even for a `has_many :tasks` association, the `load_and_authorize_resource` needs the singular name of the associated model..._

**Warning**: when overwriting the `collection` method in a controller the `load` part of a `load_and_authorize_resource` call will not work correctly. See https://github.com/ryanb/cancan/issues/274 for the discussions. 

## Mongoid
With mongoid it is necessary to reference `:project_id` instead of just `:project`
```ruby
class TasksController < InheritedResources::Base
  ...
  load_and_authorize_resource :task, :through => :project_id
end
```