## Pagination

### Paginate
Is a ready to use pagination method. The only argument passed to `paginate()` is the number of items you would like displayed "per page".

```php
User::paginate(17);
```

### Simple Paginate
Is a simple pagination method that only provide a "Next" and "Previous" methods. The difference with `paginate()` is that `simplePaginate()` is not aware of the total number of nodes to be return, thus cannot provide direct access to random pages.

```php
User::simplePaginate(15);
```

### Limit
Specifies the limit the query should account for. `take()`, is an alias for `limit()` that you can also use following the same syntax.

```php
User::limit(15)->get();
```

### Offset
Specifies the offset the query should account for. `skip()`, is an alias for `offset()` that you can also use following the same syntax.

```php
User::offset(5)->get();
```

### Count
You can use `count()` to have a node count!

```php
User::count();
```

```php
User::where('age', '>=', 20)
        ->where('age', '<=', 30)
        ->count();
```

### Custom Pagination
Sometimes you may wish to create a pagination instance manually, passing it an array of items. You may do so by creating either an `Vinelab\NeoEloquent\Pagination\Paginator` or `Vinelab\NeoEloquent\Pagination\LengthAwarePaginator` instance, depending on your needs.

The `Paginator` class does not need to know the total number of items in the result set; however, because of this, the class does not have methods for retrieving the index of the last page. The `LengthAwarePaginator` accepts almost the same arguments as the Paginator; however, it does require a count of the total number of items in the result set.

In other words, the `Paginator` corresponds to the `simplePaginate` method, while the `LengthAwarePaginator` corresponds to the `paginate` method.

When manually creating a `paginator` instance, you should manually "slice" the array of results you pass to the paginator. If you're unsure how to do this, check out the array_slice PHP function.

