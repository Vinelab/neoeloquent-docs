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

### Collections

A `Collection` class implements `ArrayAccess`, `Arrayable`, `Countable`, `IteratorAggregate` among other interfaces.
Basically a `Collection` is returned whenever an executing commands is used for retrieving multiple results.

The `Collection` class provides [a variety of helpful methods](https://github.com/Vinelab/neoeloquent-docs/blob/draft-docs/collections.md) for working with your NeoEloquent results.

### Executing Commands

Executing commands finalizes NeoElquent's builder query. Theses commands sends the fluently built query into execution.
These commands are:

| all           | first         |
| count         | firstOrFail   |
| find          | get           |
| findBy        | limit         |
| findOrFail    |               |

### Retrieving Multiple Nodes

From the above executing commands these returns a collection of nodes: `all`, `get` and `limit`.

### Retrieving Single Node

From the above executing commands these returns a single node: `find`, `findBy`, `findOrFail`, `first` and `firstOrFail`.
