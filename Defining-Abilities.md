The `Ability` class is where all user permissions are defined. An example Ability class looks like this.

```ruby
class Ability
  include CanCan::Ability

  def initialize(user)
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

You can pass an array for either of these parameters to match any one. In this case the user will have the ability to update or destroy both articles and comments.

```ruby
can [:update, :destroy], [Article, Comment]
```

Use `:manage` to represent any action and `:all` to represent any class. Here are some examples.

```ruby
can :manage, Article   # has permissions to do anything to articles
can :read, :all        # has permission to read any model
can :manage, :all      # has permission to do anything to any model
```

You can pass a hash of conditions as the third argument to further define what the user is able to access. Here the user will only have permission to read active projects which he owns.

```ruby
can :read, Project, :active => true, :user_id => user.id
```

See [[Defining Abilities with Hashes]] for more information.

Blocks can also be used if you need more control.

```ruby
can :update, Project do |project|
  project.groups.include?(user.group)
end
```

If the block returns true then the user has that ability for that project, otherwise he will be denied access. See [[Defining Abilities with Blocks]] for more information.