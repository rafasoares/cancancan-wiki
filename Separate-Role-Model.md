If you are defining a user's roles in Ruby then I don't recommend using a separate role model and table. See [[Role Based Authorization]] for my recommend way.

But in case a separate model is the best solution for you, here I show how to set up a many-to-many association between User and Role. The join model is called Assignment.

```ruby
class User < ActiveRecord::Base
  has_many :assignments
  has_many :roles, :through => :assignments
end

class Assignment < ActiveRecord::Base
  belongs_to :user
  belongs_to :role
end

class Role < ActiveRecord::Base
  has_many :assignments
  has_many :users, :through => :assignments
end
```

You can assign roles using checkboxes when creating or updating a user model.

```rhtml
<% for role in Role.all %>
<div>
  <%= check_box_tag "user[role_ids][]", role.id, @user.roles.include?(role) %>
  <%=h role.name %>
</div>
<% end %>
<%= hidden_field_tag "user[role_ids][]", "" %>
```

Or you may want to [[use Formtastic|http://railscasts.com/episodes/185-formtastic-part-2]] for this.

Next you need to determine if a user is in a specific role. You can create a method in the User model for this.

```ruby
# in models/user.rb
def has_role?(role_sym)
  roles.any? { |r| r.name.underscore.to_sym == role_sym }
end
```

And then you can use this in your Ability.

```ruby
# in models/ability.rb
def initialize(user)
  user ||= User.new # in case of guest
  if user.has_role? :admin
    can :manage, :all
  else
    can :read, :all
  end
end
```

That's it!