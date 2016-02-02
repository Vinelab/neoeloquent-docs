### Writing Queries

Writing complex queries in NeoEloquent, is a matter of chaining the adequate query comands.

Assuming we have a User class:

`class User extends Node {}`

And an Account class:

```php
class Account extends Node {
    protected $fillable = ['username', 'registration_date', 'is_active'];
}
```

Where a User node has a one to many outgoing relations `ownsAccount` to an Account node. Please note that `ownsAccount` is the relation method of the User class i.e. `User::ownsAccount()`.

#### Querying Relationship Existence

##### Has
If you wish to limit the query result to the existence of a relation you can use the `has()` method. In this case, get all users that owns an account:

`User::has('ownsAccount')->get();`

You may also use operators:

`User::has('ownsAccount', '>=', 2)->get();`

Which will return all users who owns more then or equal to two accounts.

##### WhereHas
You may use `whereHas()` to add more contrain on the relation with function closures:

```php
User::whereHas('ownsAccount', function($query){
    $query->where('is_active', '=', true);
});
```

You can chain multiple `whereHas()` for more complex queries, you can use the `orWhereHas()` method and even nest multiple closure functions.

##### Take
Is the analogue for limit:

`User::take(15)->get();`

##### Skip
Is the analogue for offset

`User::skip(5)->get();`

##### OrderBy
Should be positioned just before the execution command (e.g. get(), first(), etc.), Descending order being the default, the order can be specified as the second argument:

`User::orderBy('updated_at')->get();`

or

`User::orderBy('updated_at', 'Asc')->get();`


