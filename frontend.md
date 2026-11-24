# CSS / JavaScript

## Setting up the website favicon

Add the following code in `app/Admin/bootstrap.php` to set the website's favicon:

```
Use LaveroAdmin\Admin\Admin;

Admin::favicon('/your/favicon/path');
```

## Introducing CSS and JavaScript files

If you need to import CSS or JavaScript files, you can add the following code in `app/Admin/bootstrap.php`:

```
Admin::css('/your/css/path/style.css');
Admin::js('/your/javascript/path/js.js');
```

The path to the file is relative to the `public` directory, and it also supports the introduction of external files:

```
Admin::js('https://cdn.bootcss.com/vue/2.6.10/vue.min.js');
```

## Insert JS Script Code

If you want to add a JS script code to the current page, you can use `Admin::script()`

```
Use LaveroAdmin\Admin\Admin;

Admin::script('console.log("hello world");');
```

This code can be inserted anywhere in the code file where the current request is run, such as some linkage effects between form items, which can be implemented by inserting JS script code.

## Insert CSS Style

If you want to add a CSS code to the current page, you can use `Admin::style()`

```
Use LaveroAdmin\Admin\Admin;

Admin::style('.form-control {margin-top: 10px;}');
```

This code can be inserted anywhere in the code file to which the current request is run.

## Insert HTML Code

If you want to add a piece of HTML code to the current page, you can use `Admin::html()`

```
Use LaveroAdmin\Admin\Admin;

Admin::html('<template>...</template>');
```

This code can be implemented anywhere by inserting the code file to which the current request is run, such as when adding some hidden HTML code to the current page.

## Scripts tags in views

Scripts inside views that are loaded through ajax will be loaded by the following mechanisms:

```
<script>alert('This script will be invoked by the `eval` function')</script>
```

```
// this script will be inserted at the top of the #app div after the template is parsed into place
<script src="/load-me.js"></script>
```

## Minify CSS and JavaScript

This feature is used to compress the local CSS and JS introduced in the background to speed up page access in the background.

This feature relies on [[matthiasmullie/minify]](https://github.com/matthiasmullie/minify "https://github.com/matthiasmullie/minify") as a compression library, which needs to be installed before use:

```
composer require matthiasmullie/minify --dev
```

### Compression

Then run the command `php artisan admin:minify` in the project root directory:

```
$ php artisan admin:minify

JS and CSS are successfully minified:
        vendor/LaveroAdmin/LaveroAdmin.min.js
        vendor/LaveroAdmin/LaveroAdmin.min.css

Manifest successfully generated:
        vendor/LaveroAdmin/minify-manifest.json
```

This command will generate three files, you can see the effect by looking at the background page source.

### Cleaning up

Run the command `php artisan admin:minify --clear` to clean up the compressed file generated above and return to the state before compression.

```
$ php artisan admin:minify --clear

Following files are cleared:
        vendor/LaveroAdmin/LaveroAdmin.min.js
        vendor/LaveroAdmin/LaveroAdmin.min.css
        vendor/LaveroAdmin/minify-manifest.json
```

### Configuration

If your developing and don't want assets to be minified you can exclude them in the configuration: `config/admin.php`:

```
'minify_assets' => [
    // Assets will not be minified.
    'excepts' => [
        "vendor/lavero-admin/fields/number-input.js"
    ],
],
```
