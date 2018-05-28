---
layout: post
title:      "Building A Place To Belong"
date:       2018-05-22 17:31:41 -0400
permalink:  building_a_place_to_belong
---


Small changes can sometimes have subtle effects that lead to frustrating bugs.

When creating a migration with ActiveRecord, it is possible to define a column type that explicitly states a model references another model in a "belongs_to" relationship. It looks like this:

```ruby
def change 
  create_table :stories do |t| 
    t.references :user, foreign_key: true

  ...

```

This is very similar to simply creating a column with an integer type and calling it "user_id". In fact, this migration will produce a model that has a field called `user_id`, which serves as a foreign key for the table that is referenced by the symbol, `:user`.

In the case of creating the integer column, it is only true that the column *can* be used as a foreign key. But a confusing bug in my application forced me to realize that the explicit `references` column type sets up a relationship that **must** be used as a foreign key.

In other words, when I went to create or save a Story model, I was finding that the object was not being saved to the database. I carefully combed through the strong params I had defined for the model, and all of them were present and named correctly. But my model still wasn't being saved.

The problem was that I created the Story model as a standalone object, like this:

```
story = Story.create(story_params)
```

I was intending to assign the story to a user later, but first I just wanted to make sure that the model was created correctly. But the model would not save to the database no matter what I did. It turned out this was because defining a `references :user` column meant that a Story model was required to have a reference to a user before ActiveRecord would allow it to be saved to the database. Of course, in hindsight this makes perfect sense. A column with an integer type may be used for all sorts of things, but it is not inherently meant to be a foreign key reference to another table. That is just how we, as programmers, intend to use it. But a column that is defined as a reference to another table can only be considered valid if it actually references another table.

This whole debacle was solved by building my new Story model directly on to the current user's collection of stories.

```
story = current_user.stories.build(story_params)
```

This ensured that the story model could be saved to the database now that it had a valid reference to the current user. Presumably, this could have also been solved by assigning this story to the current user before attempting to save the Story model, but this is the exact kind of situation that the `build` method was designed for. I remember being confused when I read that the `build` method was really just another name for the `new` method. That was confusing, because it is really only part of the story. The `build` method is another name for `new`, but it is also a call to `new` that is specifically within the context of *building* a new model as an element in an existing collection for another model.

This bug helped me understand ActiveRecord migrations a little better and led me to a more complete grasp of the purpose of the `build` method. The `references` column type is a way of stating explicitly that a table will reference another table, instead of simply creating an integer column that is intended to reference another table. This makes the purpose of your data much more clear and precise, and, as I found out, it enlists ActiveRecord's builtin validations to help ensure the database relations are correct.
