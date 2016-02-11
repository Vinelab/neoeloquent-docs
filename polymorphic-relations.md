## Polymorphic Relations

A polymorphic relation involves three nodes: the parent (start), hyper (middle) and the related (end) nodes.

![](https://s3-eu-west-1.amazonaws.com/vinelab-docs/Polymorphic+Relations.png);

### Defining Relations
As a use case we will consider a `:User` writing a `:Comment` on a `:Post` or `:Video`.

```php
class User extends Node {

    public function comments($morph = null)
    {
        return $this->hyperMorph($morph, '...\Comment', 'COMMENTED', 'ON');
    }
}
```

```php
class Post extends Node {

    public function comments()
    {
        return $this->morphMany('Comment', 'ON');
    }
}
```

```php
class Video extends Node {

    public function comments()
    {
        return $this->morphMany('Comment', 'ON');
    }
}
```

```php
class Comment extends Node {

    public function commentable()
    {
        return $this->morphTo();
    }
}
```

>In the above definitions a comment relation can be created between a `:User` and either a `:Post` or a `:Video`. Hence the name of this relation type.

>When using `commentable()` as a [dynamic property], both videos and posts objects are returned

```php
$media = $user->commentable;
```

You can specify the type of morph you would like returned:

```php
class Comment extends Node {

    public function post()
    {
        return $this->morphTo('...\Post', 'ON');
    }

    public function video()
    {
        return $this->morphTo('...\Video', 'ON');
    }
}
```

### Creating Polymorphic Relations
To create a polymorphic relation use the following syntax:

```php
$user = User::create([...]);
$comment = Comment::create([...]);

$user->comments($post)->save($comment);
```
### Accessing Nodes
You can access the nodes involved in a polymorphic relation, like so:

```php
$relation = $user->comments($post)->save($comment);

$parent = $relation->parent();
$hyper = $relation->hyper();
$related = $relation->related();
```

