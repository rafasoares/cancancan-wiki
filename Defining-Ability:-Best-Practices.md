# Defining Ability: Best Practice

Here is the single best practice for defining `Ability` in real-world applications more complicated than your standard CRUD.

## TL;DR: Use hash conditions!

Here's why:

1. Although scopes are fine for fetching, they pose a problem when authorizing a discrete action.

  For example, this declaration in Ability:

  ```ruby
  can :read, Article, Article.published
  ```

  causes this `CanCan::Error`:

  ```
  The can? and cannot? call cannot be used with a raw sql 'can' definition.
  The checking code cannot be determined for :read #<Article ..>.
  ```

  The fix is:

  ```
  can :read, Article, is_published: true
  ```

2. Hash conditions are DRYer.

  By using hashes instead of blocks for all actions, you won't have to worry about translating blocks used for member controller actions (`:create`, `:destroy`, `:update`) to equivalent blocks for collection actions (`:index`, `:show`).

3. Hash conditions are OR'd in SQL, giving you maximum flexibilty.

  Even though `ActiveRecord` treats chained scopes as SQL `AND`s, CanCanCan treats hash conditions for a given model/action pair as SQL `OR`s.

  So if, in addition to the `is_published` condition above, Ability elsewhere allows authors to see their drafts:

  ```ruby
    can :read, Article, author_id: @user.id,
                        is_published: [false, nil]                      
  ```

  Then an authorization request that fires both would generate this SQL:

  ```sql
  SELECT `articles`.*
  FROM   `articles`
  WHERE  `articles`.`is_published` = 1
  OR     (
                `articles`.`author_id` = x'08d4fda58b0d40c789f036c0bcf9248a'
         AND    (
                       `articles`.`is_published` = 0
                OR     `articles`.`is_published` IS NULL )
  ```

4. For complex object graphs, hash conditions easily accommodate `joins`. See https://github.com/CanCanCommunity/cancancan/wiki/defining-abilities#hash-of-conditions.
