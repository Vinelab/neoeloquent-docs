## Working with Relations

Relations NeoEloquent are defined by their cardinality.
Main relations type are:
- One-To-One
- One-To-Many
- Many-To-Many

And

- [Polymorphic relations](https://github.com/Vinelab/neoeloquent-docs/blob/draft-docs/polymorphic-relations.md)

We recommend that special care should be put into naming relations methods, as a general guidline we recommend it to be readable in a fluent context i.e. method chaining.

### Creating a Relation

The generic process of creating a relation:

```php
$relation = $user->address()->save($address);
```
Where $user is a `:User` node, `address()` is the user class method that define the relation and it's type and $address is a `:Address` node.

> Saving a relation to a non existent node will create the node.

### Detaching a Relation

```php
$user->address()->detach($address);
```

### One-To-One Relations

One to one relations are relations with a cardinality of 1 on both it's ends. Declaration of One-To-One relations goes as follow:

```php
class User extends Node
{
    public function phone()
    {
        return $this->hasOne('...\Phone', 'HAS_PHONE');
    }
}
```
This represent an `OUTGOING` relation from `:User` to `:Phone`.

Define an inverse relation like so:

```php
class Phone extends Node
{
    public function user()
    {
        return $this->belongsTo('...\User', 'HAS_PHONE');
    }
}
```

Where `Phone::user()` is an 'INCOMING' relation from `:User` to `:Phone`.

> A One-To-One relation is unique, so if a new/different node is saved it will replace the previous one.

For existing nodes, persisting a One-To-One relationship can be done associating a node to another:

Using the node:

```php
$user = User::create(...);
$phone = Phone::create(...);

$user->phone()->associate($phone);
```

Or the id:

```php
$user->phone()->associate($phone->getKey());
```

### One-To-Many Relations

One to many relations are relations with a cardinality of 1 on the start/parent node and * on the end/related node. Declaration of One-To-Many relations goes as follow:

```php
class User extends Node
{
    public function countryOfOrigin()
    {
        return $this->hasOne('...\Country', 'BORN_IN');
    }
}
```

This represent an `OUTGOING` relation from `:User` to `:Country`.

Define an inverse relation like so:

```php
class Country extends Node
{
    public function citizens()
    {
        return $this->belongsToMany('...\User', 'BORN_IN');
    }
}
```

Where `Country::citizens()` is an 'INCOMING' relation from `:User` to `:Country`.

Persisting a One-To-Many relationship can be done attaching a node to another:

Using the node:

```php
$user = User::create(...);
$country = Country::create(...);

$user->country()->attach($country));
```

Or the id:

```php
$user->country()->attach($country->getKey());
```

If you are using the invers of the relation, then you should use associate:

```php
$country->citizens()->associate($user);
```

### Many-To-Many Relations

Many to many relations are relations with a cardinality of * on both it's ends. Declaration of One-To-Many relations goes as follow:

```php
class User extends Node
{
    public function interests()
    {
        return $this->hasMany('...\Interest', 'INTERESTED_IN');
    }
}
```

This represent an `OUTGOING` relation from `:User` to `:Interest`.

Define an inverse relation like so:

```php
class Interest extends Node
{
    public function interestedUsers()
    {
        return $this->belongsToMany('...\User', 'INTERESTED_IN');
    }
}
```

Where `Interest::interestedUsers()` is an 'INCOMING' relation from `:User` to `:Interest`.

Persisting a Many-To-Many relationship can be done attaching a node to another:

Using the node:

```php
$user = User::create(...);
$interest = Interest::create(...);

$user->insterests()->attach($interest->getKey()));
```

> Please note that it is not compulsary to define invers relations as long as you do not intend to create and/or read in the
invers direction.

In general, use associate when creating a relation to an end node of cardinality equal to 1, and attach to an end node of a * cardinality.

### Dynamic Properties

Dynamic properties is a simple way to access the relation as if it were a property.

```php

// return the user's interests as a collection
$user->interests;

// return the interest's users as a collection
$user->interests;

// return the user's country
$user->countryOfOrigin;
```

> If you noticed, dynamic properties return type depends on the relation being queried, if it has a many cardinality it retuns a collection of node objects, otherwise it returns a single node object.

### Usage

Writing complex queries in NeoEloquent, is a matter of chaining the adequate query comands.

Assuming we have a User class:

```php
class User extends Node
{
    protected $fillable = ['name', 'email', 'age', 'is_active'];

    public function ownsAccounts ()
    {
        return $this->hasMany('...\Account', 'OWNS_ACCOUNT');
    }
}
```

And an Account class:

```php
class Account extends Node {
    protected $fillable = ['username', 'registration_date', 'is_active'];

    public function  users ()
    {
        return $this->belongsToMany('...\User', 'OWNS_ACCOUNT');
    }
}
```

Where a `:User` node has a many to many outgoing relations `ownsAccounts` to an `:Account` node.

#### Querying Relationship Existence

##### Has
If you wish to limit the query result to the existence of a relation you can use the `has()` method. In this case, get all users that owns an account:

```php
User::has('ownsAccounts')->get();
```

You may also use operators:

```php
User::has('ownsAccounts', '>=', 2)->get();
```

Which will return all users who owns more then or equal to two accounts.

##### WhereHas
You may use `whereHas()` to add more contrain on the relation with function closures:

```php
User::whereHas('ownsAccounts', function($query){
    $query->where('is_active', '=', true);
});
```

You can chain multiple `whereHas()` for more complex queries, you can use the `orWhereHas()` method and even nest multiple closure functions.

#### Relation Properties
Just like a node, a relation can hold proprties:

you can add properties to a relation:

```php
$relation = $user->countryOfOrigin()->save($country);

$relation->birthday = '1987-05-11';
$relation->town = 'Zahle';
```

You can access it's properties:

```php
$relation = $user->countryOfOrigin()->edge();
$birthday = $relation->birthday;
```

You may also specify the node at the end of the relation:

```php
$interest = Interest::where('title', 'sports')->first();

$relation = $user->interests()->edge($interest);

$priority = $relation->priority;
```
