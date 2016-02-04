### Pagination

#### Limit
Specifies the limit the query should account for. `take()`, is an alias for limit that you can also use following the same syntax.

```php
User::limit(15)->get();
```

#### Offset
Specifies the offset the query should account for. `skip()`, is an alias for offset that you can also use following the same syntax.

```php
User::offset(5)->get();
```

#### Count
You can use `count()` to have a node count!

```php
User::count();
```

```php
User::where('age', '>=', 20)
        ->where('age', '<=', 30)
        ->count();
```
