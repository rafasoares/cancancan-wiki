Some complex ability conditions are not possible through a hash as shown in the [[Defining Abilities]] page. For these you can use a block.

```ruby
can :update, Project do |project|
  project.priority < 3
end
```

If the block returns true then the user has that ability, otherwise he will be denied access. However this condition is only executable through Ruby, if you try to fetch records using `accessible_by` it will raise an exception. To fetch records from the database based on this condition you need to supply an SQL string representing the condition.

```ruby
can :update, Project, ["priority < ?", 3] do |project|
  project.priority < 3
end
```

You will then be able to use this condition when [[Fetching Records]].

**Note:** The passed in object to the block will always be an instance. If one is checking on a class it will not trigger the block. See [[Checking Abilities]] for details.


### Block Conditions with Scopes

As of CanCan 1.6 it's possible to pass a scope instead of an SQL string when using a block in an ability.

```ruby
can :read, Article, Article.published do |article|
  article.published_at <= Time.now
end
```

This is really useful if you have complex conditions which require `joins`. A couple caveats:

* You cannot use this with multiple `can` definitions that match the same action and model since it is not possible to combine them. An exception will be raised when that is the case.
* If you use this with `cannot`, the scope needs to be the inverse since it's passed directly through. For example, if you don't want someone to read discontinued products the scope will need to fetch non discontinued ones:

```ruby
cannot :read, Product, Product.where(:discontinued => false) do |product|
  product.discontinued?
end
```

It is only recommended to use scopes if a situation is too complex for a hash condition.


### Overriding All Behavior

You can override all `can` behavior by passing no arguments, this is useful when permissions are defined outside of ruby such as when defining [[Abilities in Database]].

```ruby
can do |action, subject_class, subject|
  # ...
end
```

Here the block will be triggered for every `can?` check, even when only a class is used in the check.


## Additional Docs

* [[Defining Abilities]]
* [[Checking Abilities]]
* [[Testing Abilities]]
* [[Debugging Abilities]]
* [[Ability Precedence]]
