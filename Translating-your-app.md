To use translations in your app define some yaml like this:

    # en.yml
    en:
      unauthorized:
        manage:
          all: "You have no access!"

## Translation for individual abilities
If you want to castomize messages for some model or even for some ability define translation like this:

    # models/ability.rb
    ...
    can :create, Article
    ...

    # en.yml
    en:
      unauthorized:
        create:
          article: "Only admin may do this!"

### Translating custom abilities
Also translations is available for your custom abilities:

    # models/ability.rb
    ...
    can :vote, Article
    ...

    # en.yml
    en:
      unauthorized:
        vote:
          article: "Only users whitch have one or more article may do this!"

## Variables for translations
Finally you may use `action`(whitch contain ability like 'create') and `subject`(for example 'article') variables in your translattion:

    # en.yml
    en:
      unauthorized:
        manage:
          all: "You have not access to %{action} %{subject}!"

Enjoy!