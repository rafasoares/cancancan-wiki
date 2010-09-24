If [[Defining Abilities with Hashes]] is not flexible enough for your needs, it is possible to use a block. Here you can use any Ruby code to restrict what the user is able to access.

```ruby
can :update, Project do |project|
  project && project.groups.include?(user.group)
end
```

If the block returns `true` then the user has that `:update` ability for that project, otherwise he will be denied access. It's possible for the passed in model to be `nil` if one isn't specified, so be sure to take that into consideration.

**The downside to using a block is that it cannot be used when [[Fetching Records]].**
But you could specify raw SQL condition in addition to block:

```ruby
can :update, Project, ['projects.id in (select gp.project_id 
                                        from groups_projects gp
                                        where gp.group_id = ?)', user.group_id] do |project|
  project && project.groups.include?(user.group)
end
```

If `:all` is passed then the class will be passed into the block along with the object (just in case the object is nil). Here the user has permission to read all objects except orders.

```ruby
can :read, :all do |object_class, object|
  object_class != Order
end
```

If `:manage` is used then the action is passed into the block as well. Here the user can do everything but destroy comments.

```ruby
can :manage, Comment do |action, comment|
  action != :destroy
end
```

If you use both `:manage` and `:all` with a block you will have complete flexibility in how permissions are handled. See [[Abilities in Database]] for an example.

If additional arguments are passed to the `can?` method, those arguments will be passed into the block as well. This is useful for passing additional information about the request.

```ruby
# in controller or view
can? :read, article, request.remote_ip

# in ability
can :read, Article do |article, remote_ip|
  # ...
end
```

See [[Accessing Request Data]] for an alternative solution.