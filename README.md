# GuzzleBundleRetryPlugin
[![Build Status](https://travis-ci.org/EugenGanshorn/GuzzleBundleRetryPlugin.svg?branch=master)](https://travis-ci.org/EugenGanshorn/GuzzleBundleRetryPlugin)
[![Coverage Status](https://coveralls.io/repos/github/EugenGanshorn/GuzzleBundleRetryPlugin/badge.svg?branch=master)](https://coveralls.io/github/EugenGanshorn/GuzzleBundleRetryPlugin?branch=master)

## Requirements
 - PHP 7.3 or above
 - [Guzzle Bundle][1]
 - [Guzzle Retry middleware][2]

## Installation
Using [composer][3]:

##### composer.json
``` json
{
    "require": {
        "eugenganshorn/guzzle-bundle-retry-plugin": "^1.0"
    }
}
```

##### command line
``` bash
$ composer require eugenganshorn/guzzle-bundle-retry-plugin
```
## Usage
### Enable bundle

#### Symfony 2.x and 3.x
Plugin will be activated/connected through bundle constructor in `app/AppKernel.php`, like this:

``` php 
new EightPoints\Bundle\GuzzleBundle\EightPointsGuzzleBundle([
    new EugenGanshorn\Bundle\GuzzleBundleRetryPlugin\GuzzleBundleRetryPlugin(),
])
```

#### Symfony 4
The registration of bundles was changed in Symfony 4 and now you have to change `src/Kernel.php` to achieve the same functionality.  
Find next lines:

```php
foreach ($contents as $class => $envs) {
    if (isset($envs['all']) || isset($envs[$this->environment])) {
        yield new $class();
    }
}
```

and replace them by:

```php
foreach ($contents as $class => $envs) {
    if (isset($envs['all']) || isset($envs[$this->environment])) {
        if ($class === \EightPoints\Bundle\GuzzleBundle\EightPointsGuzzleBundle::class) {
            yield new $class([
                new \EugenGanshorn\Bundle\GuzzleBundleRetryPlugin\GuzzleBundleRetryPlugin(),
            ]);
        } else {
            yield new $class();
        }
    }
}
```
#### Symfony 5 & 6
Override the `registerBundles` method in `src/Kernel.php` to connect the plugin to the bundle:

```php
public function registerBundles(): iterable
{
    $contents = require $this->getBundlesPath();
    foreach ($contents as $class => $envs) {
        if ($envs[$this->environment] ?? $envs['all'] ?? false) {
            if ($class === EightPointsGuzzleBundle::class) {
                yield new $class([
                    new GuzzleBundleRetryPlugin(),
                ]);
            } else {
                yield new $class();
            }
        }
    }
}
```

### Basic configuration
``` yaml
# app/config/config.yml // config/packages/eight_points_guzzle.yaml

eight_points_guzzle:
    clients:
        your_client:
            base_url: "http://api.domain.tld"

            # plugin settings
            plugin:
                retry:
                    ~
```
### Advanced configuration
See middleware options:
https://github.com/caseyamcl/guzzle_retry_middleware#options

## License
This middleware is licensed under the MIT License - see the LICENSE file for details

[1]: https://github.com/8p/EightPointsGuzzleBundle
[2]: https://github.com/caseyamcl/guzzle_retry_middleware
[3]: https://getcomposer.org/
