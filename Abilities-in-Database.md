What if a non-programmer needs to modify the user abilities, or you want to change them without having to re-deploy the application? In that case it may be best to store the permission logic in a separate model, let's call it Permission. It is easy to use the database records when defining abilities.

For example, let's assume that each user has_many :permissions, and each permission has "action", "subject_type" and "subject_id" columns. The last of which is optional.

```ruby
class Ability
  include CanCan::Ability

  def initialize(user)
    can do |action, subject_class, subject|
      user.permissions.find_all_by_action(action).each do |permission|
        permission.subject_class == subject_class.to_s &&
          (subject.nil? || permission.subject_id.nil? || permission.subject_id == subject.id)
      end
    end
  end
end
```

An alternative approach is to define a separate "can" ability for each permission.

```ruby
def initialize(user)
  user.permissions.each do |permission|
    can permission.action.to_sym, permission.subject_class.constantize do |subject|
      permission.subject_id.nil? || permission.subject_id == subject.id
    end
  end
end
```

The actual details will depend largely on your application requirements, but hopefully you can see how it's possible to define permissions in the database and use them with CanCan.

You can mix-and-match this with defining permissions in the code as well. This way you can keep the more complex logic in the code so you don't need to shoe-horn every kind of permission rule into an overly-abstract database.