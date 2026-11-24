# Model-show

`LaveroAdmin\Admin\Show` is used to show the details of the data. Let's take an example. There is a `Pages` table in the database:

```
posts
    id          - integer
    date        - date
    slug        - string
    title       - string
    intro       - text
    body        - text
    created_at  - timestamp
    updated_at  - timestamp
```

The corresponding data model is `App\Models\Page`, and the following code can show the data details of the `pages` table.
Most likey the show section of your AdminController generated with the scaffoling helper / or `php artisan admin:controller` command will look something like:

```
<?php

namespace App\Admin\Controllers;

use LaveroAdmin\Admin\Controllers\AdminController;
use LaveroAdmin\Admin\Form;
use LaveroAdmin\Admin\Grid;
use LaveroAdmin\Admin\Show;
use \App\Models\Page;

class PageController extends AdminController
{
    protected function grid()
    {
        // ...
    }
    /**
     * Make a show builder.
     *
     * @param mixed $id
     * @return Show
     */
    protected function detail($id)
    {
        $show = new Show(Blog::findOrFail($id));

        $show->field('id', __('Id'));
        $show->field('date', __('Date'));
        $show->field('slug', __('Slug'));
        $show->field('title', __('Title'));
        $show->field('intro', __('Intro'));
        $show->field('body', __('Body'));
        $show->field('created_at', __('Created at'));
        $show->field('updated_at', __('Updated at'));

        return $show;
    }

    protected function form()
    {
        // ...
    }
}
```

## Basic usage

### Escape content

In order to prevent XSS attacks, the default output content will be HTML escape, if you don't want to escape the output HTML, you can call the `unescape` method:

```
$show->field("column","label")->unescape()->as(function ($url) {

     return "<img src='{$url}' />";

});
```

### Modify the style and title of the panel

```
$show->panel()
    ->style('danger')
    ->title('post detail...');
```

The value of `style` could be `primary`, `info`, `danger`, `warning`, `default`

### Panel tool settings

There are three buttons `Edit`, `Delete`, `List` in the upper right corner of the panel. You can turn them off in the following ways:

```
$show->panel()
    ->tools(function ($tools) {
        $tools->disableEdit();
        $tools->disableList();
        $tools->disableDelete();
    });;
```
