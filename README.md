#### Magento 2 module to optimize Compiled ObjectManager.

This module will work only with Magento 2.3.3 and higher

# **Important**
This is experimental functionality, You are using it at your own risk ! 

# **Install**

### Composer
```"monogo/optimize-object-manager```

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

  
# **How it works**
When you are using M2 in Production mode and **generated/metadata** directory is not empty, magento is using Compiled mode in Object Manager.

Generated files contains all classes and are heavy.

In my case it was >8 MB per one area file.


Magento is loading global.php file and after that your area file (for example frontend.php).

This module is calculating diff between global.php and given area, and it is saving new file __[area_code]_global_diff.php

If diff file exists, it is coping loaded global config to given area, and it is patching config with the diff.

The original area file (for example frontend.php) is skipped.

In my case (API call) it improved simple request by 14% of time and 33% of memory used.
