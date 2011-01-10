The ability rules are interpreted from bottom to top. For example, let's say we want the user to be able to do everything to projects except destroy them. This is the correct way.

```ruby
can :manage, Project
cannot :destroy, Project
```

It is important the `cannot` line is near the bottom so it will override the `:manage` behavior. Otherwise CanCan will stop on the `:manage` line since it gives the user access and the `cannot` line would never be reached. Therefore, it is best to place the more generic rules near the top.

This is also important when dealing with roles which have inherited behavior. For example, let's say we have two roles, moderator and admin. We want the admin to inherit the moderator's behavior.

```ruby
if user.role? :moderator
  can :manage, Project
  cannot :destroy, Project
  can :manage, Comment
end
if user.role? :admin
  can :destroy, Project
end
```

Here it is important the admin role be after the moderator so it can override the `cannot` behavior to give the admin more permissions. See [[Role Based Authorization]].

If you are not getting the behavior you expect, please [[post an issue|https://github.com/ryanb/cancan/issues]].

## Additional Docs

* [[Defining Abilities]]
* [[Checking Abilities]]
* [[Debugging Abilities]]
* [[Testing Abilities]]