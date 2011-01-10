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

The current user model is passed into the initialize method, so the permissions can be modified based on any user attributes. CanCan makes no assumption about how roles are handled in your application. See [[Role Based Authorization]] for an example.

## The `can` Method

The `can` method is used to define permissions and requires two arguments. The first one is the action you're setting the permission for, the second one is the class of object you're setting it on.

```ruby
can :update, Article
```

You can pass `:manage` to represent any action and `:all` to represent any subject.

```ruby
can :manage, Article  # user can perform any action on the article
can :read, :all       # user can read any object
can :manage, :all     # user can perform any action on any object
```

Common actions are `:read`, `:create`, `:update` and `:destroy` but it can be anything. See [[Action Aliases]] and [[Custom Actions]] for more information on actions.

You can pass an array for either of these parameters to match any one. For example, here the user will have the ability to update or destroy both articles and comments.

```ruby
can [:update, :destroy], [Article, Comment]
```

A `cannot` method can also be used and takes the same arguments. This is normally done after a more generic `can` call.

```ruby
can :manage, Project
cannot :destroy, Project
```

The order of these calls is important. See [[Ability Precedence]] for more details.


### Hash of Conditions

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

**Note:** The passed in object to the block will always be an instance. If one is checking on a class it will not trigger the block. See [[Checking Abilities]] for details.


### Overriding All Behavior

You can override all `can` behavior by passing no arguments, this is useful when permissions are defined outside of ruby such as when defining [[Abilities in Database]].

```ruby
can do |action, subject_class, subject|
  # ...
end
```

Here the block will be triggered for every `can?` check, even when only a class is used in the check.


## Additional Docs

* [[Checking Abilities]]
* [[Testing Abilities]]
* [[Debugging Abilities]]
* [[Ability Precedence]]
