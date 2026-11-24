# Extension development

`LaveroAdmin` supports the installation of extension tools to help enrich your background functionality, currently under [https://github.com/LaveroAdmin/](https://github.com/LaveroAdmin/ "https://github.com/LaveroAdmin/") There have been more than a dozen extensions.

If you add some of your own features or components to the `LaveroAdmin` process, you can make a `LaveroAdmin` extension, which will help other `LaveroAdmin` users. And improve the quality of the extension in the feedback of others.

This document will take the development of a `phpinfo` extension as an example, develop an extension step by step, and publish it to others for use.

## Create composer package

The `LaveroAdmin` package will be installed with composer, so first create a composer package. You can use the built-in `admin:extend` command. Generate an extended skeleton

> When running the command, you may be prompted to enter a directory to store your extension file. You can add a configuration `'extension_dir' => admin_path('extensions'),` in `config/admin.php`, The extension files will be stored in the `app/Admin/extensions` directory, but you can also put them in any other directory.

```
php artisan admin:extend lavero-admin-ext/phpinfo --namespace=LaveroAdmin\\PHPInfo
```

Where `lavero-admin-ext/phpinfo` is the package name, and the `namespace` option is the top-level namespace used by this package. After running this command, the directory will be generated in the extension directory `lavero-admin-ext/phpinfo` set in `config/admin.php`. and the following file structure

```
├── LICENSE
├── README.md
├── composer.json
├── database
│         ├── migrations
│         └── seeds
├── resources
│         ├── assets
│         └── views
│             └── index.blade.php
├── routes
│         └── web.php
└── src
    ├── PHPInfo.php
    ├── PHPInfoServiceProvider.php
    └── Http
        └── Controllers
            └── PHPInfoController.php
```

`resources` is used to place view files and static assets, `src` is mainly used to place logic code, `routes/web.php` is used to store the extension's routing settings, and `database` is used to place database migration files and `seeders`.

## Installation

To start using your extention you need make sure it's installend via composer. While developing your plugin you can install it locally as follows:

Open the `composer.json` file in your project and add the following configuration

```
"repositories": [
    {
        "type": "path",
        "url": "app/Admin/extensions/lavero-admin-ext/phpinfo"
    }
]
```

Then run `composer require lavero-admin-ext/phpinfo` to complete the installation. If there are static files to be published, run the following command.

```
php artisan vendor:publish --provider=LaveroAdmin\PHPInfo\PHPInfoServiceProvider
```

In case the `composer require` command didn't works as expected you also add this line the require section:

```
    "require": {
        ...
        "lavero-admin-ext/phpinfo": "dev-master"
    }
```

and run `composer update`, note; this will also update other dependencies if available.

Now our plugin should be installed. Let's take a look dashboard and see if your plugin is in the decpencencies list. We can also try to navigate to `admin/phpinfo` to see if our route is picked up. If you see 'Welcome to LaveroAdmin' everything seems to going correcly.

## Write code

This extended function is mainly used to integrate the page displayed by the `phpinfo` function in PHP into `LaveroAdmin`. It will have a route and a view file. There is no database file and static resource file. We can Clean up any files or directories that are not in the directory. The directory files after the cleanup are:

```
├── LICENSE
├── README.md
├── composer.json
├── resources
│         └── views
│             └── index.blade.php
├── routes
│         └── web.php
└── src
    ├── PHPInfo.php
    ├── PHPInfoServiceProvider.php
    └── Http
        └── Controllers
            └── PHPInfoController.php
```

### Routes

First lets take a look the routes, a routing configuration has been automatically generated in `routes/web.php`
That should look like;

```
<?php
use LaveroAdmin\PHPInfo\Http\Controllers\PHPInfoController;

Route::get('phpinfo', PHPInfoController::class.'@index');
```

Accessing the path `phpinfo` will handle this request by the `index` method of the `LaveroAdmin\PHPInfo\Http\Controllers\PHPInfoController` controller.

### Setting extension attributes

`src/PHPInfo.php` as an extension class for setting extended properties

```
<?php

namespace LaveroAdmin\PHPInfo;

use LaveroAdmin\Admin\Extension;

class PHPInfo extends Extension
{
    public $name = 'phpinfo';

    public $views = __DIR__.'/../resources/views';

    public $assets = __DIR__.'/../resources/assets';

    public $menu = [
        'title' => 'Phpinfo',
        'path'  => 'phpinfo',
        'icon'  => 'icon-cogs',
    ];
}
```

This file is used to set some properties of this extension. `$name` is the name of this extension. If this extension has a view file that needs to be rendered, you must specify the `$views` attribute of the extension. Also, if there is a static resource file to be published. , you must set the `$assets` property. If you need to add a menu button to the left sidebar, set the `$menu` property.

You can remove unnecessary attributes as needed. After modifying the `src/PHPInfo.php` file, the code is:

```
<?php

namespace LaveroAdmin\PHPInfo;

use LaveroAdmin\Admin\Extension;

class PHPInfo extends Extension
{
    public $name = 'phpinfo';

    public $views = __DIR__ . '/../resources/views';

    public $menu = [
        'title' => 'PHP info',
        'path'  => 'phpinfo',
        'icon'  => 'icon-exclamation',
    ];
}
```

To import the menu item you should run:

```
php artisan admin:import phpinfo
```

Then open `src/PHPInfoServiceProvider.php`, this `ServiceProvider` will run when `laravel` is started, which is used to register some data of this extension into the application.

### Loading the view

If this extension needs to load the view file, add the following code to the `handle` method of `src/PHPInfoServiceProvider.php`:

```
if ($views = $extension->views()) {
    $this->loadViewsFrom($views, 'phpinfo');
}
```

The first parameter of the `loadViewsFrom()` method is the view property set in the extension class `src/PHPInfo.php`, and the second parameter is the namespace of the view file directory. After setting it to `phpinfo`, in the controller. Use `view('phpinfo::index')` to load the view file in the `resources/views` directory.

### Introducing static resources

If you have static resource files in your project that need to be imported, first put the files in the `resources/assets` directory, such as `resources/assets/foo.js` and `resources/assets/bar.css`. file

Then set the `$assets` property in the extension class `src/PHPInfo.php`

```
    public $assets = __DIR__.'/../resources/assets';
```

Then set the release directory in the `handle` method of `src/PHPInfoServiceProvider.php`

```
if ($this->app->runningInConsole() && $assets = $extension->assets()) {
    $this->publishes(
        [$assets => public_path('vendor/lavero-admin-ext/phpinfo')],
        'phpinfo'
    );
}
```

After the installation is complete, run:

```
php artisan vendor:publish --provider=LaveroAdmin\PHPInfo\PHPInfoServiceProvider
```

and the asset files will be copied to the `public/vendor/lavero-admin-ext/phpinfo` directory.

We need to add these two files into the page when `LaveroAdmin` is started. You need to add the following code in the `handle` method of `src/PHPInfoServiceProvider.php`.

```
use use LaveroAdmin\Admin\Admin;

...

Admin::booting(function () {
    Admin::js('vendor/lavero-admin-ext/phpinfo/foo.js');
    Admin::css('vendor/lavero-admin-ext/phpinfo/bar.css');
});
```

This completes the import of static resources. In the extension of `phpinfo`, since no static resources need to be introduced, this step can be ignored.

### Code Logic Development

The logic of this extension is to extract the PHP configuration data displayed by the `phpinfo` function and then render the new view rendering output.

The next step is to get the phpinfo configuration data in the controller by calling the `PHPInfo::toCollection` method, and then render it into the view.

```
<?php

namespace LaveroAdmin\PHPInfo\Http\Controllers;

use LaveroAdmin\Admin\Layout\Content;
use Illuminate\Routing\Controller;
use LaveroAdmin\PHPInfo\PHPInfo;

class PHPInfoController extends Controller
{
    public function index(Content $content,PHPInfo $info)
    {
        $info = $info::toCollection();

        return $content
            ->title('Title')
            ->description('Description')
            ->body(view('phpinfo::index', ["info"=>$info]));
    }
}
```

Such a complete extension is now developed.

### Modify composer.json & README.md

After the code is partially completed, you need to modify the contents of `composer.json`, replace the contents of `description`, `keywords`, `license`, `authors` with your information, and then don't forget to update `README.md`, supplementary use of documents and other related information.

### Remote installation

If the development is complete and you want to open it up for everyone, follow the steps below.

#### Upload to github

Login to your Github first, create a repository, and follow the prompts on the page to push your code up.

```
git init
git remote add origin https://github.com/<your-name>/<your-repository>.git
git add .
git commit -am "Initial commit."
git push origin master
```

#### Release release

You can publish the version locally in the following way

```
git tag 0.0.1 && git push --tags
```

It can also be manually set on the `Releases` page of Github's repository.

#### Submit to Packagist.org

The next step is to submit your project to `Packagist.org`. If you don't have an account, register one first, then open the [Submit](https://packagist.org/packages/submit "https://packagist.org/packages/submit") in the top navigation and fill in the repo's github url to submit.

By default, Packagist.org does not automatically update when you push new code, so you need to create a [GitHub Service Hook](https://packagist.org/about#how-to-update-packages "https://packagist.org/about#how-to-update-packages"). You can also manually update it using the "Update" button on the page, but I recommend automating this process. After the submission is complete, you can install your extension via composer.

#### Join [https://github.com/LaveroAdmin](https://github.com/LaveroAdmin "https://github.com/LaveroAdmin")

If you want to contribute an extension you developed, please contact us or open a repository at [https://github.com/LaveroAdmin](https://github.com/LaveroAdmin "https://github.com/LaveroAdmin") so that more people can see and use the tools you built.
