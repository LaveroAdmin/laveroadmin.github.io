# Integrate CKEDITOR into LaveroAdmin

This is a `lavero-admin` extension that integrates `CKEDITOR` into the `lavero-admin` form.

## Installation

```
composer require laveroadmin/ckeditor
```

Then

```
php artisan vendor:publish --tag=lavero-admin-ckeditor
```

Forcing vendor publish after upgrade / downgrade

```
php artisan vendor:publish --tag=lavero-admin-ckeditor --force
```

## Configuration

In the `extensions` section of the `config/admin.php` file, add some configuration that belongs to this extension.

```
    'extensions' => [

        'ckeditor' => [

            //Set to false if you want to disable this extension
            'enable' => true,

            // Editor configuration
            'config' => [

            ]
        ]
    ]
```

The configuration of the editor can be found in [CKEditor Documentation](https://ckeditor.com/docs/ckeditor4/latest/guide/ "https://ckeditor.com/docs/ckeditor4/latest/guide/"), such as configuration language and height.

```
    'config' => [
        'language'      => 'de',
        'height'        => 500,
        'contentsCss'   => '/css/frontend-body-content.css',
    ]
```

## Usage

Use it in the form:

```
$form->ckeditor('content');

// Set config
$form->ckeditor('content')->options(['lang' => 'fr', 'height' => 500,'contentsCss' => '/css/frontend-body-content.css']);
```

## Problems?

If ckeditor is not showing up and tells you that it's not found run the lines below to clear the compiled services and packages.

```
php artisan optimize:clear
```

## License

Licensed under [The MIT License (MIT)](https://opensource.org/licenses/MIT).
