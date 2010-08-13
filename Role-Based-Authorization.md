CanCan is decoupled from how you implement roles in the User model, but how might one set up basic role-based authorization?

While you can create a [[Separate Role Model]], I recommend not doing so if you are defining the role abilities in Ruby. This is because you will need to edit both the Ruby code and the database in order to add new roles.

Since there is such a tight coupling between the list of roles and abilities, I recommend keeping the list of roles in Ruby. You can do so in a constant under the User class.

```ruby
class User < ActiveRecord::Base
  ROLES = %w[admin moderator author banned]
end
```

But now, how do you set up the association between the user and the roles? You'll need to decide if the user can have many roles or just one.

## One role per user

If a user can have only one role, it's as simple as adding a `role` string column to the `users` table.

```bash
script/generate migration add_role_to_users role:string
rake db:migrate
```

Now you can provide a select-menu for choosing the roles in the view.

```rhtml
<!-- in users/_form.html.erb -->
<%= f.collection_select :role, User::ROLES, :to_s, :humanize %>
```

You may not have considered using `collection_select` when you aren't working with an association, but it will work perfectly. In this case the user will see the humanized name of the role, and the simple lower-cased version will be passed in as the value when the form is submitted.

It's then very simple to determine the role of the user in the Ability class.

```ruby
can :manage, :all if user.role == "admin"
```


## Many roles per user

It is possible to assign multiple roles to a user and store it into a single integer column using a [[bitmask|http://en.wikipedia.org/wiki/Mask_(computing)]]. First add a `roles_mask` integer column to your `users` table.

```bash
script/generate migration add_roles_mask_to_users roles_mask:integer
rake db:migrate
```

Next you'll need to add the following code to the User model for getting and setting the list of roles a user belongs to. This will perform the necessary bitwise operations to translate an array of roles into the integer field.

```ruby
# in models/user.rb
def roles=(roles)
  self.roles_mask = (roles & ROLES).map { |r| 2**ROLES.index(r) }.sum
end

def roles
  ROLES.reject do |r|
    ((roles_mask || 0) & 2**ROLES.index(r)).zero?
  end
end
```

You can use checkboxes in the view for setting these roles.

```rhtml
<% for role in User::ROLES %>
  <%= check_box_tag "user[roles][]", role, @user.roles.include?(role) %>
  <%=h role.humanize %><br />
<% end %>
<%= hidden_field_tag "user[roles][]", "" %>
```

Finally, you can then add a convenient way to check the user's roles in the Ability class.

```ruby
# in models/user.rb
def is?(role)
  roles.include?(role.to_s)
end

# in models/ability.rb
can :manage, :all if user.is? :admin
```

See [[Custom Actions]] for a way to restrict which users can assign roles to other users.

This functionality has also been extracted into a little gem called [[role_model|http://rubygems.org/gems/role_model]] ([[code & howto|http://github.com/martinrehfeld/role_model]]).