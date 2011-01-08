**CanCan 1.5** is currently in beta. To use this version you will need to mention it specifically in your Gemfile.

```ruby
gem "cancan", "1.5.0.beta1"
```

See [[Upgrading to 1.4]] if you have not upgraded to 1.4 yet. Also check out the [[CHANGELOG|http://github.com/ryanb/cancan/blob/master/CHANGELOG.rdoc]] for the full list of changes.

## Ability Generator

A Rails 3 generator has been added for creating the `Ability` class when starting out with CanCan. After adding and installing the `cancan` gem, run the generator with this command.

```bash
rails g cancan:ability
```

Comments are included in the generated file with more information on using the Ability class.

## Mongoid and DataMapper Support

There is improved support with `Mongoid` and `DataMapper`. It now works with [[Fetching Database Records]]. This also means you can use their query syntax in the `can` hash conditions.

```ruby
# in Ability
can :read, Article, :priority.lt => 5
```

CanCan will handle this behavior automatically, just be certain to add the Mongoid or DataMapper gem before CanCan in the Gemfile.

A great way to contribute to CanCan is to create a model adapter for other ORMs. See [[mongoid_adapter.rb|https://github.com/ryanb/cancan/blob/master/lib/cancan/model_adapters/mongoid_adapter.rb]] for an example. Specs should also be included. See [[spec/README|https://github.com/ryanb/cancan/blob/master/spec/README.rdoc]] for running specs for specific adapters.

Thank you [[bowsersenior|https://github.com/bowsersenior]] for the Mongoid behavior and [[natemueller|https://github.com/natemueller]] for the DataMapper behavior.

## Skip Loading and Authorizing

Three new methods have been added to the controller: `skip_load_and_authorize_resource`, `skip_load_resource`, and skip_authorize_resource`. Each of these can be used to skip either the loading or authorization behavior in CanCan. You can pass `:only` or `:except` option to either one to specify which actions to skip. For example.

```ruby
class ProjectsController < ApplicationController
  load_and_authorize_resource
  skip_load_resource :only => :index
end
```

You can optionally pass the name of the resource as the first argument just like you can in `load_and_authorize_resource`.

**Special thanks to everyone who helped contribute to this release. See the issue tracker for the history.**
