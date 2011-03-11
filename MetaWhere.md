As of CanCan 1.6, when you have [[MetaWhere|http://metautonomo.us/projects/metawhere/]] installed, it is possible to use those queries in abilities.

```ruby
can :read, Project, :priority.lt => 3
can :update, Article, :published_at.not_eq => nil
```

The `&` and `|` joining are not supported yet but hopefully will be soon. If you are interested in helping add this please see the issue tracker.