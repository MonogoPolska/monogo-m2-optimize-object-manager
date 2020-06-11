#### Magento 2 module to optimize Compiled ObjectManager.

This module will work only with Magento 2.3.4 and higher

# **Important**
This is experimental functionality, You are using it at your own risk ! 

# **Install**

### Composer
In perfect world, command 
```composer require monogo/optimize-object-manager``` 
should do the job.

But in few cases, composer do not recognize patch files. 

If you don't see line 
```
  - Applying patches for magento/framework"
```

run 
```
composer require cweagans/composer-patches
```

Add in your composer.json

```
"extra": {
           "composer-exit-on-patch-failure": true,
           "patches": {
               "magento/framework": {
                   "Optimize Magento ObjectManager ConfigLoader: ": "https://raw.githubusercontent.com/MonogoPolska/monogo-m2-optimize-object-manager/master/patches/composer/optimize-config-loader.diff"
               }
           }
       }
```

Run 
```
composer install
```

You should see line 
```
  - Applying patches for magento/framework"
```

if not, go to vendor/magneto and remove framework directory, then re-run 

```
composer install
```

### Magento Setup
- Run Magento commands

```php bin/magento setup:upgrade```

```php bin/magento setup:di:compile```

```php bin/magento setup:static-content:deploy```

- Make sure your that Magento 2 instance is running in Production mode and after installation **generated/metadata** directory is not empty

# **Configuration Options**

Edit app/etc/env.php

add lines:

```    
    'monogo_autoload_optimization' => [
        'enabled' => 1,
        'allowed_area' => [
            'frontend',
        ],
    ],
```


- enabled - 0 or 1
- allowed_area - Magento 2 area (frontend,adminhtml,crontab,webapi_rest,webapi_soap,...) . 

# **Check**

After installation and configuration, you should see new files in **generated/metadata** , for example:
- __frontend_global_diff.php
- __adminhtml_global_diff.php
  
# **How it works**
When you are using M2 in Production mode and **generated/metadata** directory is not empty, magento is using Compiled mode in Object Manager.

Generated files contains all classes and are heavy.

In my case it was >8 MB per one area file.


Magento is loading global.php file and after that your area file (for example frontend.php).

This module is calculating diff between global.php and given area, and it is saving new file __[area_code]_global_diff.php

If diff file exists, it is coping loaded global config to given area, and it is patching config with the diff.

The original area file (for example frontend.php) is skipped.

In my case (API call) it improved simple request by 14% of time and 33% of memory used.
