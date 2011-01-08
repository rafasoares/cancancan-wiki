This section has been moved to [[Defining Abilities]] under "Third Argument: Conditions".

## Checking with Class

It is important to note that even if you have a hash of conditions, calling `can?` on just the class will return true. For example.

```ruby
can :read, Project, :priority => 3
can? :read, Project # returns true
```

It is impossible to answer this `can?` question completely because not enough details are given. The class does not have a `priority` attribute to check on.

Think of it as asking "can the current user read **a** project?". The user can read a project, so this returns `true`. However it depends on which specific project you're talking about. If you are doing a class check, it is important you do another check once an instance becomes available so the hash of conditions can be used.

The reason I went with this approach is because of the controller `index` action. Since the `authorize_resource` before filter has no instance to check on, it will use the `Project` class. If the authorization failed at that point then it would be impossible to filter the results later using `accessible_by`.

```ruby
Project.accessible_by(current_ability)
```

That is why passing a class to `can?` will return `true`.