A hash of conditions can be passed for defining user permissions in the `Ability` class. Here the user will only have permission to read active projects which he owns.

```ruby
can :read, Project, :active => true, :user_id => user.id
```

It is important to only use database columns for these conditions so it can be used for [[Fetching Records]].

You can use nested hashes to define conditions on associations. Here the project can only be read if the category it belongs to is visible.

```ruby
can :read, Project, :category => { :visible => true }
```

An array or range can be passed to match multiple values. Here the user can only read projects of priority 1 through 3.

```ruby
can :read, Project, :priority => 1..3
```

Basically anything that you can pass to a hash of conditions in ActiveRecord will work here. If you need more flexibility, try [[Defining Abilities with Blocks]].

Also you could use standard SQL fragment. But be aware to specify block as well, otherwise it would evaluate to `true` on real objects.

```ruby
can :read, Project, ["published_at > ?", 2.days.ago] do |project|
  project.nil? || project.published_at > 2.days.ago
end
```

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