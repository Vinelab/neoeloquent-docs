### Working With Nodes

#### Create

If you just landed here, you should go through [Nodes], to make some sense of basic nodes concepts.

`Fillables` is an array of attribute that are mass assignable to node clases. You should note that no attributes missing for the fillable array will be persisted in storage, with an exception of the node's id. Some default attributes, such as `created_at`, `updated_at` are added to a node if and only if the timestamps property of the node class is set to `true`.

Assuming we have a User class

`class User extends Node {}`

It is as simple as calling the `create()` method on a User instance while passing the properties that it should include:
``` php
$user = User::create(
              'name'    =>    'Alen',
              'email'   =>    'alenemail@mail.com',
              'age'     =>    27,
              'active'  =>    true
        );
```

#### Read
A read operation can be performed using different methods depending on the result you are looking for. We recommend that you check the [key concept] for differences amon these.

##### Where
Retreiving a collection of nodes from storage using the `where()` method and an adequate execution comand (for more on execution comands check [Key Concepts]):

```php
User::where('id', $user->getKey())->get();
```

##### Find
Retreiving one node from storage by id:

```php
User::find($user->getKey());
```

##### FindBy
Retreiving one node from storage by property:

```php
User::findBy('email', 'alenemail@mail.com');
```

And you can use operators:
```php
User::findBy('age', '<=', 27);
```

#### Update
An update operation can be done in two forms:

##### Update
Calling `update()` will suffice to update the specified properties, but you will need to retrieve the node from storage:
``` php
  User::where('id', 2434)
        ->update(
          'name'    =>   'Alen',
          'email'   =>   'alennewemail@mail.com',
          'age'     =>   27,
          'active'  =>   true
  );
```

##### Fill
Or you can use `fill()` to fill the class attributes and then save to storage. If no corresponding node exists in storage a new instance is created.

>It is important to note that `fill()` will only fill the class's attributes, and then you will need to call `save()` on the
>class instance in order to persist it in storage.

``` php
  User::fill([
        'name'    =>   'Alen',
        'email'   =>   'alennewemail@mail.com',
        'age'     =>   27,
        'active'  =>   true
  ]);

  User::save();
```

#### Delete
NeoEloquent allow, permanent and soft delete operations.

##### Delete
In order to permanently delete a node, you should query the node and then call `delete()`:

```php
User::find($user->getKey())->delete();
```

##### Soft Delete
Soft delete is a way to exclude entities from the result. The behaviour is that of deleting an entity while in reality it still exists.
In order to allow soft delete on a node class, you should use the `SoftDeletes` trait and add a protected '$dates' attribute to the class. Like so:

```php
class User extends Node
{
    use SoftDeletes;

    /**
     * The attributes that should be mutated to dates.
     *
     * @var array
     */
    protected $dates = ['deleted_at'];
}
```

Then you can soft delete a node using the same syntax:

```php
User::find($user->getKey())
      ->delete();
```

###### Querying Soft Deleted Nodes
Using the `withTrashed()` method will returned soft deleted nodes along with exsiting nodes:

```php
User::withTrashed()
      ->where('id', $user->getKey())
      ->get();
```

Using the `onlyTrashed()` method will returned only soft deleted nodes:

```php
User::onlyTrashed()
      ->where('age', '<=', 30)
      ->where('age', '>=', 25)
      ->get();
```

###### Force Delete
In order to permanently delete a soft deleted node you will need to use the `forceDelete()` method:

```php
User::withTrashed()
      ->find(2434)
      ->forceDelete();
```

###### Restore Soft Deleted Node
To restore a soft deleted node you should use `restore()` method:

```php
User::withTrashed()
      ->find(2434)
      ->restore();
```





