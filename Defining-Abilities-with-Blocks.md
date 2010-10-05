If [[Defining Abilities with Hashes]] is not flexible enough for your needs, it is possible to use a block. Here you can use any Ruby code to restrict what the user is able to access.

```ruby
can :update, Project do |project|
  project.groups.include?(user.group)
end
```

If the block returns `true` then the user has that `:update` ability for that project, otherwise ability checking will *pass through* up to the next matching definition.

As of 1.4, the block is not called when checking permission on a class which means you no longer need to check for `nil`. This makes it consistent with [[Defining Abilities with Hashes]].

```ruby
can :read, Project do |project|
  project.publicly_available?
end
can? :read, Project # returns true without triggering block
can? :read, @project # triggers block
```

Also new in 1.4 is that only the object is passed into the block, never the action or class. See [[Upgrading to 1.4]] for details.

**The downside to using a block is that it cannot be used when [[Fetching Records]].**
But as of 1.4 you could specify raw SQL condition in addition to a block.

```ruby
can :update, Project, ['projects.id in (select gp.project_id 
                                        from groups_projects gp
                                        where gp.group_id = ?)', user.group_id] do |project|
  project.groups.include?(user.group)
end
```

If additional arguments are passed to the `can?` method, those arguments will be passed into the block. This is useful for passing additional information about the request.

```ruby
# in controller or view
can? :read, article, request.remote_ip

# in ability
can :read, Article do |article, remote_ip|
  # ...
end
```

See [[Accessing Request Data]] for an alternative solution.

You can override all `can` behavior by passing no arguments, this is useful when permissions are defined outside of ruby such as when defining [[Abilities in Database]].

```ruby
can do |action, subject_class, subject|
  # ...
end
```