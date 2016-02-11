## Configuration

```php
[
    'connections' => [

        'default' => [
            'host' => 'server.host',
            'port' => 7474,
            'username' => 'theuser',
            'password' => 'thepass',
        ],

    ],
]
```

### Default

The following are the parameters that will be used in a connection if no configuration was provided.

```php
[
    'host' => 'localhost',
    'port' => 7474
]
```

### Multiple Connections

```php
[
    'default' => 'server1',

    'connections' => [

        'server1' => [
            'host' => 'server1.host',
            'username' => 'theuser',
            'password' => 'thepass',
        ],

        'server2' => [
            'host' => 'server2.host',
            'username' => 'anotheruser',
            'password' => 'anotherpass',
        ],

    ],

]
```

### Replication/HA (High-Availability)

```php
[
    'replication' => true,

    'connections' => [
    
       'master' => [
            'host' => 'server1.ip.address',
            'username' => 'theuser',
            'password' => 'dapass'
       ],
       
       'slaves' => [
            [
                'host' => 'server2.ip.address',
                'username' => 'anotheruser',
                'password' => 'somepass',
            ],
            [
                'host' => 'server3.ip.address',
                'username' => 'moreusers',
                'password' => 'differentpass',
            ]
       ],
       
    ],
]
```
