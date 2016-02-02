### Create

If you just landed here, you should go through [Nodes], to make some sense of basic nodes concepts.
`Fillables` is an array of attribute that are mass assignable to node clases. You should note that no attributes missing for the fillable array will be persisted in storage, with an exception of the node's id. Some default attributes, such as `created_at`, `updated_at` are added to a node if and only if the timestamps property of the node class is set to `true`.

Assuming we have a User class

`class User extends Node {}`

It is as simple as calling the `create()` method on a User instance while passing the properties that it should include:
``` php
  User::create(
  'id'      =>    '2434',
  'name'    =>    'Alen',
  'email'   =>    'alenemail@mail.com',
  'age'     =>    27,
  'active'  =>    true
  );
```

### Update
An update operation can be done in two forms:

#### Update
Calling `update()` will suffice to update the specified properties, but you will need to retrieve the node from storage:
``` php
  User::where('id', '2434')
        ->update(
          'name'    =>   'Alen',
          'email'   =>   'alennewemail@mail.com',
          'age'     =>   27,
          'active'  =>   true
  );
```

#### Fill
Or you can use `fill()` to fill the class attributes and then save to storage. If no corresponding node exists in storage a new instance is created. It is important to note that `fill()` will only fill the class's attributes, and then you will need to call `save()` on the class instance in order to persist it in storage:
``` php
  User::fill([
     'name'    =>   'Alen',
     'email'   =>   'alennewemail@mail.com',
     'age'     =>   27,
     'active'  =>   true
  ]);

  User::save();
```
