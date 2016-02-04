### Examples

```php

User::where('is_active', true)->whereHas('ownsAccount', function ($query) use ($accountId) {
        $query->where((new Account())->getKeyName(), $accountId);
        })->skip($offset)->take($limit)->orderBy('updated_at')->get();
```

>Note: `use` allow passing a external variable to the closure function.

