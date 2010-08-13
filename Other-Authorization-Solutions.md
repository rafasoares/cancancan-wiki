There are [[many authorization solutions|http://steffenbartsch.com/blog/2008/08/rails-authorization-plugins/]] available, and it is important to find one which best meets the application requirements. I've tried to keep CanCan minimal yet extendable so it can be used in many situations, but there are times it doesn't fit the best.

In earlier versions of CanCan there was no support for using the Ability logic in database queries, but 1.1 allows one to define permissions through a conditions hash which is usable in the database. See [[Upgrading to 1.1]] for details on this new feature.

If you find the conditions hash to be too limiting I encourage you to check out [[declarative_authorization|http://github.com/stffn/declarative_authorization]] which offers a sophisticated DSL for handling more complex permission scenarios. This allows one to generate complex database queries based on the permissions but at the cost of a more complex DSL.

Also consider, if you have very unique authorization requirements, the best choice may be to write your own solution instead of trying to shoe-horn an existing plugin.

*If you find a case where another tool meets a specific requirement better than CanCan, please edit this wiki page and add it.*