## Relations

Relations NeoEloquent are defined by their cardinality.
Main relations type are:
- One-To-One
- One-To-Many
- Many-To-Many

// Review
We recommend that special care should be put into naming relations, as a general guidline we recommend:
- Explicitly infer cardinality
- Explicitly infer direction (HOW TO?)

### Creating a Relation

The generic process of creating a relation:

```php
$relation = $user->address()->save($address);
```
Where $user is a `:User` node, `address()` is the user class method that define the relation and it's type and $address is a `:Address` node.

> Saving a relation to a non existent node will create the node.

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

> Please note that it is not compulsary to define invers relations as long as you do not intend to create and/or read in the
> invers direction.

### Usage

Writing complex queries in NeoEloquent, is a matter of chaining the adequate query comands.

Assuming we have a User class:

```php
class User extends Node
{
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
