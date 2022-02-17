# Yii2 Maintenance mode component
[![Latest Stable Version](https://poser.pugx.org/brussens/yii2-maintenance-mode/v/stable)](https://packagist.org/packages/brussens/yii2-maintenance-mode)
[![Total Downloads](https://poser.pugx.org/brussens/yii2-maintenance-mode/downloads)](https://packagist.org/packages/brussens/yii2-maintenance-mode)
[![License](https://poser.pugx.org/brussens/yii2-maintenance-mode/license)](https://packagist.org/packages/brussens/yii2-maintenance-mode)
## Install
Either run
```
php composer.phar require --prefer-dist kagir/yii2-maintenance-mode "*"
```

or add

```
"kagir/yii2-maintenance-mode": "*"
```

to the require section of your `composer.json` file.

Add to your config file:
```php
'bootstrap' => [
    'kagir\maintenance\Maintenance'
],
...
'container' => [
    'singletons' => [
        'kagir\maintenance\Maintenance' => [
            'class' => 'kagir\maintenance\Maintenance',

            // Route to action
            'route' => 'maintenance/index',

            // Filters. Read Filters for more info.
            'filters' => [
                [
                    'class' => 'kagir\maintenance\filters\RouteFilter',
                    'routes' => [
                        'debug/default/toolbar',
                        'debug/default/view',
                        'site/login',
                    ]
                ]
            ],

            // HTTP Status Code
            'statusCode' => 503,

            //Retry-After header
            'retryAfter' => 120 // or Wed, 21 Oct 2015 07:28:00 GMT for example
        ],
        'kagir\maintenance\StateInterface' => [
            'class' => 'kagir\maintenance\states\FileState',

            // optional: use different filename for controlling maintenance state:
            // 'fileName' => 'myfile.ext',

            // optional: use different directory for controlling maintenance state:
            // 'directory' => '@mypath',
        ]
    ]
]
```

## Filters
You can use filters for allow excepts:

```php
'container' => [
    'singletons' => [
        'kagir\maintenance\Maintenance' => [
            'class' => 'kagir\maintenance\Maintenance',
            // Route to action
            'route' => 'maintenance/index',
            // Filters. Read Filters for more info.
            'filters' => [
                //Allowed routes filter. Your can allow debug panel routes.
                [
                    'class' => 'kagir\maintenance\filters\RouteFilter',
                    'routes' => [
                        'debug/default/toolbar',
                        'debug/default/view',
                        'site/login',
                    ]
                ],
                // Allowed roles filter
                [
                    'class' => 'kagir\maintenance\filters\RoleFilter',
                    'roles' => [
                        'administrator',
                    ]
                ],
                // Allowed IP addresses filter
                [
                    'class' => 'kagir\maintenance\filters\IpFilter',
                    'ips' => [
                        '127.0.0.1',
                    ]
                ],
                //Allowed user names
                [
                    'class' => 'kagir\maintenance\filters\UserFilter',
                    'checkedAttribute' => 'username',
                    'users' => [
                        'Kagir',
                    ],
                ]
            ],
        ]
    ]
]
```
You can create custom filter:
```php
class MyCustomFilter extends Filter
{
    public $time;

    /**
     * @return bool
     */
    public function isAllowed()
    {
        return (bool) $this->time > 3600;
    }
}
```

## Set maintenance mode by console or dashboard

Add to your console or common config file:
```php
'container' => [
    'singletons' => [
        'kagir\maintenance\StateInterface' => [
            'class' => 'kagir\maintenance\states\FileState',
            // optional: use different filename for controlling maintenance state:
            // 'fileName' => 'myfile.ext',

            // optional: use different directory for controlling maintenance state:
            // 'directory' => '@mypath',
        ]
    ]
],
'controllerMap' => [
      'maintenance' => [
          'class' => 'kagir\maintenance\commands\MaintenanceController',
      ],
],

```

Now you can set mode by command:
```
php yii maintenance/enable
```
```
php yii maintenance/disable
```
