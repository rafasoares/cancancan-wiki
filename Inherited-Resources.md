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
