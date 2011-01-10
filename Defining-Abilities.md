The `Ability` class is where all user permissions are defined. An example class looks like this.

```ruby
class Ability
  include CanCan::Ability

  def initialize(user)
    user ||= User.new # guest user (not logged in)
    if user.admin?
      can :manage, :all
    else
      can :read, :all
    end
  end
end
```

The current user model is passed into the initialize method so the permissions can be modified based on any user attributes. CanCan makes no assumption about how roles are handled in your application. See [[Role Based Authorization]] for an example.

The `can` method is used to define permissions and requires two arguments. The first one is the action you're setting the permission for, the second one is the class of object you're setting it on.

```ruby
can :update, Article
```

You can pass an array for either of these parameters to match any one. For example, here the user will have the ability to update or destroy both articles and comments.

```ruby
can [:update, :destroy], [Article, Comment]
```

### First Argument: Action

The first argument is the action which can be performed. You can use `:manage` to represent any action. Here the user will be able to do anything to an article.

```ruby
can :manage, Article
```

Other common actions are `:read`, `:create`, `:update` and `:destroy`. If you have custom controller actions you can use those directly here.

Also see [[Action Aliases]] and [[Custom Actions]].


### Second Argument: Subject

The second argument is the class which the action can be performed on. Use `:all` to represent any object. Here the user has access to read anything.

```ruby
can :read, :all
```

Also see [[Custom Subjects]].


### Third Argument: Conditions

A hash of conditions can be passed to further restrict which records this permission applies to. Here the user will only have permission to read active projects which he owns.

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

Basically anything that you can pass to a hash of conditions in ActiveRecord will work here.


### Block Conditions

A block can also be used to add conditions which cannot be defined through a hash.

```ruby
can :update, Project, ["priority < ?", 3] do |project|
  project.priority < 3
end
```

If the block returns true then the user has that ability, otherwise he will be denied access. The third argument here is SQL representing the same behavior, this is optional but providing it allows it to work with [[Fetching Records]].

**Note:** The block is only called if an instance is provided. See [[Checking Abilities]] for details.

### Overriding All Behavior

You can override all `can` behavior by passing no arguments, this is useful when permissions are defined outside of ruby such as when defining [[Abilities in Database]].

```ruby
can do |action, subject_class, subject|
  # ...
end
```

### Additional Docs

* [[Checking Abilities]]
* [[Testing Abilities]]
* [[Debugging Abilities]]
* [[Ability Precedence]]
