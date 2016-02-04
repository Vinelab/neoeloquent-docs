### Pagination

#### Limit/Take
Is the analogue for limit:

```php
User::take(15)->get();
```

#### Offset/Skip
Is the analogue for offset

```php
User::skip(5)->get();
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
