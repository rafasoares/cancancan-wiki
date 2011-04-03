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

You can pass `:manage` to represent any action and `:all` to represent any object.

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


## Hash of Conditions

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

Anything that you can pass to a hash of conditions in Active Record will work here. The only exception is working with model ids. You can't pass in the model objects directly, you must pass in the ids.

```ruby
can :manage, Project, :group => { :id => user.group_ids }
```

If you have a complex case which cannot be done through a hash of conditions, see [[Defining Abilities with Blocks]] or [[MetaWhere]].

## Combining Abilities

It is possible to define multiple abilities for the same resource. Here the user will be able to read products which are released OR available for preview.

```ruby
can :read, Project, :released => true
can :read, Project, :preview => true
```

The `cannot` method takes the same arguments as `can` and defines which actions the user is unable to perform. This is normally done after a more generic `can` call.

```ruby
can :manage, Project
cannot :destroy, Project
```

The order of these calls is important. See [[Ability Precedence]] for more details.

## Additional Docs

* [[Defining Abilities with Blocks]]
* [[Checking Abilities]]
* [[Testing Abilities]]
* [[Debugging Abilities]]
* [[Ability Precedence]]
