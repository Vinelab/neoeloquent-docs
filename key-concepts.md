## Key Concepts

In this section we adress some of NeoEloquent's key concepts.

### Primary Key
NeoEloquent will also assume that each node has a primary key named `id`. You may define a `$primaryKey` property to override this convention:

```php
class User extends Node
{
    /**
     * The node's primary key
     *
     * @var string
     *
     */
    protected $primaryKey = 'uuid';

    // ...
}

```

In addition, it assumes that the primary key is an incrementing integer value. If you wish to use a non-incrementing primary key, you must set the `$incrementing` property on your node to false.

### Timestamps

By default, NeoEloquent expects `created_at` and `updated_at` properties to exist on your nodes. If you do not wish to have these properties automatically managed by NeoEloquent, set the `$timestamps` property on your node to false:

```php
class User extends Node
{
    /**
     * Indicates if the node should be timestamped
     *
     * @var bool
     *
     */
    public $timestamps = false;

    // ...
}
```

If you need to customize the format of your timestamps, set the `$dateFormat` property on your model. This property determines how date attributes are stored in the database, as well as their format when the model is serialized to an array or JSON:

```php
class User extends Node
{
    /**
     * The storage format of the node's date properties.
     *
     * @var string
     */
    protected $dateFormat = 'U';
}
```

### Database Connection

By default, all NeoEloquent models will use the default database connection configured for your application. If you would like to specify a different connection for the model, use the `$connection` property:

```php
class User extends Node
{
    /**
     * The connection name for the node.
     *
     * @var string
     */
    protected $connection = 'connection-name';
}
```

### Mass Assignment

When inserting a node using the `create` method to save a new node the inserted node instance will be returned to you from the method. However, before doing so, you will need to specify either a `fillable` or `guarded` attribute on the node, as all NeoEloquent nodes protect against mass-assignment.

A mass-assignment vulnerability occurs when a user passes an unexpected HTTP parameter through a request, and that parameter changes a property in your database you did not expect. For example, a malicious user might send an `is_admin` parameter through an HTTP request, which is then mapped onto your model's `create` method, allowing the user to escalate themselves to an administrator.

So, to get started, you should define which node attributes you want to make mass assignable. You may do this using the `$fillable` property on the node. For example, let's make the `name` attribute of our `User` node mass assignable:

```php
class User extends Node
{
    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = ['name'];
}
```

> Once we have made the attributes mass assignable, we can use the create method to insert a new record in the database. The create method returns the saved model instance

While `$fillable` serves as a "white list" of attributes that should be mass assignable, you may also choose to use `$guarded`. The `$guarded` property should contain an array of attributes that you do not want to be mass assignable. All other attributes not in the array will be mass assignable. So, `$guarded` functions like a "black list". Of course, you should use either `$fillable` or `$guarded` - not both:

```php
class User extends Node
{
    /**
     * The attributes that aren't mass assignable.
     *
     * @var array
     */
    protected $guarded = ['social_security_number'];
}
```

In the example above, all attributes except for `social_security_number` will be mass assignable.

### Collections

A `Collection` class implements `ArrayAccess`, `Arrayable`, `Countable`, `IteratorAggregate` among other interfaces.
Basically a `Collection` is returned whenever an executing commands is used for retrieving multiple results.

The `Collection` class provides [a variety of helpful methods](https://github.com/Vinelab/neoeloquent-docs/blob/draft-docs/collections.md) for working with your NeoEloquent results.

### Executing Commands

Executing commands finalizes NeoElquent's builder query. Theses commands sends the fluently built query into execution.
These commands are:

|            |             |
|------------|-------------|
| all        | first       |
| count      | firstOrFail |
| find       | get         |
| findBy     | limit       |
| findOrFail | paginate    |

> Not Found Exceptions: Sometimes you may wish to throw an exception if a model is not found. This is particularly useful in routes or controllers. The `findOrFail` and `firstOrFail` methods will retrieve the first result of the query. However, if no result is found, a `Vinelab\NeoEloquent\ModelNotFoundException` will be thrown.

#### Retrieving Multiple Nodes

From the above executing commands these returns a collection of nodes: `all`, `get`, `paginate` and `limit`.

#### Retrieving Single Node

From the above executing commands these returns a single node: `find`, `findBy`, `findOrFail`, `first` and `firstOrFail`.


### Query Scopes

#### Global Scopes

Global scopes allow you to add constraints to all queries for a given node. Writing your own global scopes can provide a convenient, easy way to make sure every query for a given node receives certain constraints.

##### Writing Global Scopes

Writing a global scope is simple. Define a class that implements the `Vinelab\NeoEloquent\Scope` interface. This interface requires you to implement one method: `apply`. The `apply` method may add `where` constraints to the query as needed:

```php
use Illuminate\Database\Eloquent\Scope;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Builder;

class AgeScope implements Scope
{
    /**
     * Apply the scope to a given NeoEloquent query builder.
     *
     * @param  \Vinelab\NeoEloquent\Builder  $builder
     * @param  \Vinelab\NeoEloquent\Model  $model
     * @return void
     */
    public function apply(Builder $builder, Model $model)
    {
        return $builder->where('age', '>', 200);
    }
}
```
##### Applying Global Scopes

To assign a global scope to a node, you should override a given node's `boot` method and use the `addGlobalScope` method:

```php
use App\Scopes\AgeScope;
use Vinelab\NeoEloquent\Node;

class User extends Node
{
    /**
     * The "booting" method of the node.
     *
     * @return void
     */
    protected static function boot()
    {
        parent::boot();

        static::addGlobalScope(new AgeScope);
    }
}
```

After adding the scope, a query to `User::all()` will produce the following Cypher:

```php
MATCH (users:User)
WHERE users.age > 200
RETURN users
```

##### Anonymous Global Scopes

NeoEloquent also allows you to define global scopes using Closures, which is particularly useful for simple scopes that do not warrant a separate class:

```php
use Vinelab\NeoEloquent\Model;
use Vinelab\NeoEloquent\Builder;

class User extends Node
{
    /**
     * The "booting" method of the node.
     *
     * @return void
     */
    protected static function boot()
    {
        parent::boot();

        static::addGlobalScope('age', function(Builder $builder) {
            $builder->where('age', '>', 200);
        });
    }
}
```

The first argument of the `addGlobalScope()` serves as an identifier to remove the scope:

##### Removing Global Scopes

If you would like to remove a global scope for a given query, you may use the `withoutGlobalScope` method:

```php
User::withoutGlobalScope(AgeScope::class)->get();
```

If you would like to remove several or even all of the global scopes, you may use the `withoutGlobalScopes` method:

```php
User::withoutGlobalScopes()->get();

User::withoutGlobalScopes([FirstScope::class, SecondScope::class])->get();
```
#### Local Scopes

Local scopes allow you to define common sets of constraints that you may easily re-use throughout your application. For example, you may need to frequently retrieve all users that are considered "popular". To define a scope, simply prefix an NeoEloquent node method with scope.

Scopes should always return a query builder instance:

```php
class User extends Model
{
    /**
     * Scope a query to only include popular users.
     *
     * @return \Vinelab\NeoEloquent\Builder
     */
    public function scopePopular($query)
    {
        return $query->where('votes', '>', 100);
    }

    /**
     * Scope a query to only include active users.
     *
     * @return \Vinelab\NeoEloquent\Builder
     */
    public function scopeActive($query)
    {
        return $query->where('active', 1);
    }
}
```

##### Utilizing A Query Scope

Once the scope has been defined, you may call the scope methods when querying the model. However, you do not need to include the `scope` prefix when calling the method. You can even chain calls to various scopes, for example:

```php
$users = User::popular()->active()->orderBy('created_at')->get();
```

##### Dynamic Scopes

Sometimes you may wish to define a scope that accepts parameters. To get started, just add your additional parameters to your scope. Scope parameters should be defined after the $query argument

```php
class User extends Node
{
    /**
     * Scope a query to only include users of a given type.
     *
     * @return \Vinelab\NeoEloquent\Builder
     */
    public function scopeOfType($query, $type)
    {
        return $query->where('type', $type);
    }
}
```

Now, you may pass the parameters when calling the scope:

```php
$users = App\User::ofType('admin')->get();
```

### Events

NeoEloquent models fire several events, allowing you to hook into various points in the model's lifecycle using the following methods: `creating`, `created`, `updating`, `updated`, `saving`, `saved`, `deleting`, `deleted`, `restoring`, `restored`. Events allow you to easily execute code each time a specific node class is saved or updated in the database.

#### Basic Usage

Whenever a new node is saved for the first time, the `creating` and `created` events will fire. If a node already existed in the database and the `save` method is called, the `updating` / `updated` events will fire. However, in both cases, the `saving` / `saved` events will fire.

For example, let's define an NeoEloquent event listener in a service provider. Within our event listener, we will call the `isValid` method on the given node, and return `false` if the node is not valid. Returning `false` from an NeoEloquent event listener will cancel the `save` / `update` operation:

```php
use App\User;
use Vinelab\NeoEloquent\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        User::creating(function ($user) {
            if ( ! $user->isValid()) {
                return false;
            }
        });
    }

    /**
     * Register the service provider.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```
