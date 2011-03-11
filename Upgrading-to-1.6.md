This document explains what's new in CanCan 1.6. If you are just getting started with CanCan, first check out the [[README|https://github.com/ryanb/cancan]].

See [[Upgrading to 1.5]] if you are using version 1.4 or older. Also check out the [[CHANGELOG|http://github.com/ryanb/cancan/blob/master/CHANGELOG.rdoc]] for the full list of changes.

## MetaWhere Support

If you have [[MetaWhere|http://metautonomo.us/projects/metawhere/]] installed, it is now possible to use those queries in abilities.

```ruby
can :read, Project, :priority.lt => 3
can :update, Article, :published_at.not_eq => nil
```

The `&` and `|` joining is not supported yet but hopefully will be soon. If you are interested in helping add this please see the issue tracker.

## Scopes

It is now possible to pass a scope instead of an SQL string when using a block in an ability.

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

It is only recommended to use scopes if a situation is too complex for a simple hash condition.

## Conditionally Check Authorization

The `check_authorization` method now supports `:if` and `:unless` options. Either one takes a method name as a symbol. This method will be called to determine if the authorization check will be performed. This makes it very easy to skip this check on all Devise controllers since they provide a `devise_controller?` method.

```ruby
class ApplicationController < ActionController::Base
  check_authorization :unless => :devise_controller?
end
```

Here's another example where authorization is only ensured for the admin subdomain.

```ruby
class ApplicationController < ActionController::Base
  check_authorization :if => :admin_subdomain?
  private
  def admin_subdomain?
    request.subdomain == "admin"
  end
end
```

Note: The `check_authorization` only ensures that authorization is performed. If you have `authorize_resource` the authorization will still be preformed no matter what is returned here.

## Other Fixes

This update includes many other minor fixes and improvements.

* a collection resource is now loaded by default with `load_and_authorize_resource` if `params[:id]` isn't present
* added a `:prepend` option to `load_and_authorize_resource` to add it before any previously defined filters
* the current controller action is now passed to `accessible_by` instead of always using `:read` (thanks amw)
* fixed multi-word model name spacing issues in I18n messages
* many Inherited Resources fixes (thanks aq1018, tanordheim and stefanoverna)

Check out the [[CHANGELOG|http://github.com/ryanb/cancan/blob/master/CHANGELOG.rdoc]] for the full list of changes and credits.
