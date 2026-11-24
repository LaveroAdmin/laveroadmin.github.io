# Customize the head navigation bar

You can add the html element to the top navigation bar, open `app/Admin/bootstrap.php`：

```
use LaveroAdmin\Admin\Facades\Admin;

Admin::navbar(function (\LaveroAdmin\Admin\Widgets\Navbar $navbar) {

    $navbar->left('html...');

    $navbar->right('html...');

    // adds fullscreen option
    $navbar->right(new LaveroAdmin\Admin\Widgets\Navbar\Fullscreen());

    // adds ajax refresh button
    $navbar->right(new LaveroAdmin\Admin\Widgets\Navbar\RefreshButton());

});
```

method `left` nad `right` is used to add the contents of the left and right sides of the head, the method parameters can be any object can be rendered( objects which impletements `Htmlable`、`Renderable`, or has method `__toString()`) or strings.

## Add elements to the left

For example, add a search bar on the left, first create a view `resources/views/admin/search-bar.blade.php`：

```
<style>

    .search-form {
        width: 250px;
        border-radius: 3px;
        float: left;
        height:auto;
    }

    .search-form .input-group{
        padding-top:0.825rem;
    }

    .search-form input[type="text"] {
        color: #666;
        height:2.3rem;
    }

    .search-form .btn {
        height:2.3rem;
        border-color:#ced4da;
    }

</style>

<form action="/admin/pages" method="get" class="search-form" pjax-container>
    <div class="input-group input-group-sm">
        <input type="text" name="title" class="form-control" placeholder="Search...">
        <button type="submit" name="search" id="search-btn" class="btn btn-outline-primary"><i class="icon-search"></i></button>
    </div>
</form>
```

Then add it to the head navigation bar:

```
$navbar->left(view('admin.search-bar'));
```

## Add elements to the right

You can only add the `<li>` tag on the right side of the navigation, such as adding some prompt icons, creating a new rendering class `app/Admin/Extensions/Nav/Links.php`

```
<?php

namespace App\Admin\Extensions\Nav;

class Links
{
    public function __toString()
    {
        return <<<HTML

<li class="nav-item">
    <a href="#" class="nav-link">
      <i class="icon-envelope"></i>
      <span class="badge bg-success">4</span>
    </a>
</li>

<li class="nav-item">
    <a href="#" class="nav-link">
      <i class="icon-bell"></i>
      <span class="badge bg-warning">7</span>
    </a>
</li>

<li class="nav-item">
    <a href="#" class="nav-link">
      <i class="icon-flag"></i>
      <span class="badge bg-danger">9</span>
    </a>
</li>

HTML;
    }
}
```

Then add it to the head navigation bar:

```
$navbar->right(new \App\Admin\Extensions\Nav\Links());
```

Or use the following html to add a drop-down menu:

```
<style>
    .notify-menu{
        line-height:2rem;
    }
</style>
<li class="nav-item dropdown ">
    <a href="#" class="nav-link dropdown-toggle" data-bs-toggle="dropdown" aria-expanded="false">
        <i class="icon-bell"></i>
        <span class="badge bg-warning">10</span>
    </a>

    <ul class="dropdown-menu notify-menu">

    <li><h2 class="dropdown-header">You have 10 notifications</h2></li>
    <li>
        <a class="dropdown-item" href="#">
        <i class="icon-users text-info"></i> 5 new members joined today
        </a>
    </li>
    <li>
        <a class="dropdown-item" href="#">
        <i class="icon-exclamation-triangle text-warning"></i> Very long description here that may not fit into the page and may cause design problems
        </a>
    </li>
    <li>
        <a class="dropdown-item" href="#">
        <i class="icon-users text-danger"></i> 5 new members joined
        </a>
    </li>
    <li>
        <a class="dropdown-item" href="#">
        <i class="icon-shopping-cart text-success"></i> 25 sales made
        </a>
    </li>
    <li>
        <a class="dropdown-item" href="#">
        <i class="icon-user text-info"></i> You changed your username
        </a>
    </li>
    <li><hr class="dropdown-divider"></li>
    <li>
        <a class="dropdown-item" href="#">
        <i class="icon-arrow-alt-circle-right text-secondary"></i>View all</a>
    </li>
    </ul>
</li>
```

More components can be found here [Bootstrap 5: Navbar](https://getbootstrap.com/docs/5.0/components/navbar/ "https://getbootstrap.com/docs/5.0/components/navbar/")
