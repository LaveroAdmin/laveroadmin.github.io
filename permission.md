# Access Control

`LaveroAdmin` has built-in`RBAC` permissions control module, expand the left sidebar `Auth`, you can see user, permissions and roles management panel, the use of permissions control as follows:

## Route permission

In the `LaveroAdmin`, the permissions and routes are bound together, in the edit permission page which set the current permissions can access the routing, in the `HTTP method` select box to select the method of access to the path, in the `HTTP path` textarea fill in the path to access.

For example, to add a permission, the permission can access the path `/admin/users` in GET method, then `HTTP method` select `GET`, `HTTP path` fill in `/users`.

If you want to access all paths with the prefix `/admin/users`, then the `HTTP path` fill in `/users*`, if the permissions include multiple access paths, wrap the line for each path.

## Page permission

If you want to control the user's permissions in the page, you can refer to the following example

### example1

For example, there is now a scene, here is a article module, we use create articles as an example

At first open `http://localhost/admin/auth/permissions`, fill up slug field with text `create-page`, and `Create Page` in name field, then assign this permission to a role.

In your AdminController action:

```
use LaveroAdmin\Admin\Auth\Permission;
use LaveroAdmin\Admin\Layout\Content;

// hook into the an Admincontroller
class PageController extends AdminController
{
    public function create(Content $content)
    {
        // check permission, only the roles with permission `create-page` can visit this action
        Permission::check('create-page');
        return parent::create($content);
    }
    ...
```

### example2

If you want to control the page elements of the user's display, then you need to first define permissions, such as `delete-image` and `view-title-column`, respectively, to control the permissions to delete pictures and display a column in grid, then assign these two permissions to roles, add following code to the grid：

```
$grid->actions(function ($actions) {

    // The roles with this permission will not able to see the delete button in actions column.
    if (!Admin::user()->can('delete-image')) {
        $actions->disableDelete();
    }
});

// Only roles with permission `view-title-column` can view this column in grid
if (Admin::user()->can('view-title-column')) {
    $grid->column('title');
}
```

## Other methods

Get current user object.

```
Admin::user();
```

Get current user id.

```
Admin::user()->id;
```

Get user's roles.

```
Admin::user()->roles;
```

Get user's permissions.

```
Admin::user()->permissions;
```

> Note: only permission direcly assigned to uses, so not the permissions from the assigned roles)

User is role.

```
Admin::user()->isRole('developer');
```

User has permission.

```
Admin::user()->can('create-post');
```

User don't has permission.

```
Admin::user()->cannot('delete-post');
```

Is user super administrator.

```
Admin::user()->isAdministrator();
```

Is user in one of roles.

```
Admin::user()->inRoles(['editor', 'developer']);
```

## Permission middleware

You can use permission middleware in the routes to control the routing permission

```
// Allow roles `administrator` and `editor` access the routes under group.
Route::group([
    'middleware' => 'admin.permission:allow,administrator,editor',
], function ($router) {

    $router->resource('users', UserController::class);
    ...

});

// Deny roles `developer` and `operator` access the routes under group.
Route::group([
    'middleware' => 'admin.permission:deny,developer,operator',
], function ($router) {

    $router->resource('users', UserController::class);
    ...

});

// User has permission `edit-post`、`create-post` and `delete-post` can access routes under group.
Route::group([
    'middleware' => 'admin.permission:check,edit-post,create-post,delete-post',
], function ($router) {

    $router->resource('posts', PostController::class);
    ...

});
```
