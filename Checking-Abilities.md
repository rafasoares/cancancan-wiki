Use the `can?` method in the controller or view to check the user's permission for a given action and object.

```ruby
can? :destroy, @project
```

You can also pass the class instead of an instance (if you don't have one handy).

```ruby
<% if can? :create, Project %>
  <%= link_to "New Project", new_project_path %>
<% end %>
```

**Note:** When passing a class, think of it as asking "Can the user create **a** project?". It will return @true@ even if a hash of conditions exist. See [[Defining Abilities with Hashes]] for details.

The `cannot?` method is for convenience and performs the opposite check of `can?`

```ruby
cannot? :destroy, @project
```

See  [[Custom Actions]] for checking abilities on other actions.