## Eager Loading Relations

Eager loading refer in NeoEloquent context means loading additional relations on a queried node in one Database hit. It provides the least performance overhead possible. Eager loading is made possible by using the `with()` method:

Having a `:User` node:

```php
class User extends Node
{
    public function interests()
    {
        return $this->hasMany('...\Interest', 'INTERESTED_IN');
    }
}
```

An `:Interest` node

```php
class Interest extends Node
{
    public function interestedUsers()
    {
        return $this->belongsToMany('...\User', 'INTERESTED_IN');
    }
}
```

And a relation between `:User` and `:Interest`

```php
$user->insterests()->attach($interest->getKey()));
```

Eager loading:

```php
$user->with('interests')->where('is_active', =, true)->get();
```

The above query will return all active users with their interestes as a relation in the node object.
