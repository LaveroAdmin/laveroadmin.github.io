# Quick start

## Helpers Scaffolding

There is special helper to easly create tables, models & controllers, this is the most easy way to get your admin pages ready. You can install the helpers with composer

```
$ composer require laveroadmin/helpers

$ php artisan admin:import helpers
```

[https://github.com/LaveroAdmin/helpers](https://github.com/LaveroAdmin/helpers "https://github.com/LaveroAdmin/helpers")

## Explained

To get started without the helpers, lets take look at an existing table. Let's use `users` table come with `Laravel` for example,the structure of table is:

```
users
    id          - integer
    name        - string
    email       - string
    password    - string
    created_at  - timestamp
    updated_at  - timestamp
```

[testlink](installation.md#static-assets "installation.md#static-assets")

And the model for this table is `App\Models\User.php`

You can follow these steps to setup `CURD` interfaces of table `users`:

## Add controller

Use the following command to create a controller for `App\Models\User` model

```
php artisan admin:controller \\App\\Models\\User
```

The above command will create the controller in `app/Admin/Controllers/UserController.php`.

## Add route

Add a route in `app/Admin/routes.php`：

```
$router->resource('demo/users', UserController::class);
```

## Add menu item

Open `http://your-host/admin/auth/menu`, add menu link and refresh the page, then you can find a link item in left menu bar.

> Where `uri` fills in the path part that does not contain the prefix of the route, such as the full path `http://your-host/admin/demo/users`, just input `demo/users`, If you want to add an external link, just fill in the full url, such as `http://lavero-admin.org/`.

## Write CURD page logic

The controller `app/Admin/Controllers/UserController.php` created by the `admin:make` command is as follows:

```
<?php

namespace App\Admin\Controllers;

use App\Models\User;
use LaveroAdmin\Admin\Controllers\AdminController;
use LaveroAdmin\Admin\Form;
use LaveroAdmin\Admin\Grid;
use LaveroAdmin\Admin\Show;

class UserController extends AdminController
{
    protected $title ='Users';

    protected function grid()
    {
        $grid = new Grid(new User());

        $grid->column('id', __('Id'));
        $grid->column('name', __('Name'));
        $grid->column('email', __('Email'));
        $grid->column('password', __('Password'));
        $grid->column('created_at', __('Created at'));
        $grid->column('updated_at', __('Updated at'));

        return $grid;
    }

    protected function detail($id)
    {
        $show = new Show(User::findOrFail($id));

        $show->field('id', __('Id'));
        $show->field('name', __('Name'));
        $show->field('email', __('Email'));
        $show->field('password', __('Password'));
        $show->field('created_at', __('Created at'));
        $show->field('updated_at', __('Updated at'));

        return $show;
    }

    protected function form()
    {
        $form = new Form(new User());

        $form->textarea('name', __('Name'));
        $form->textarea('email', __('Email'));
        $form->textarea('password', __('Password'));

        return $form;
    }
}
```

The `$title` attribute is used to set the title of this CURD module, which can be modified to any other string.

The `grid` method corresponds to the `list` page of the data. Refer to [model-grid documentation](../model-grid/README.md) to implement the related functional logic of the list page.

The `detail` method corresponds to the `details` page of the data, click on the `detail display` button in the operation column of the list page, and refer to [model-show documentation](../model-show/README.md) to realize the details page Related functional logic.

The `form` method corresponds to the `create` and `edit` pages of the data. Refer to [model-form documentation](../model-form/README.md) to implement the relevant functional logic of the data creation and edit pages.
