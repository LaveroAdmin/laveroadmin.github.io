# Model-tree

Can be achieved through a `model-tree` to a tree-like components, you can drag the way to achieve the level of data, sorting and other operations, the following is the basic usage.

## Table structure and model

To use `model-tree`, you have to follow the convention of the table structure:

```
categories
    id          - integer
    parent_id   - integer
    order       - integer
    title       - string
```

The above table structure has three necessary fields `parent_id`, `order`, `title`, and the other fields are not required.

The corresponding model is `app/Models/Category.php`:

```
<?php

namespace App\Models;

use LaveroAdmin\Admin\Traits\AdminBuilder;
use LaveroAdmin\Admin\Traits\ModelTree;
use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    use ModelTree, AdminBuilder;

    protected $table = 'demo_categories';

    public function __construct(array $attributes = [])
    {
        parent::__construct($attributes);

        $this->setParentColumn('parent_id');
        $this->setOrderColumn('order');
        $this->setTitleColumn('title');
    }
}
```

## Usage

This code below only creates a sortable tree. You need to add you own logic to create / alter and save the strucure. Happy coding, on request we might extend this part of code to be more usefull :-)z

```
<?php

namespace App\Admin\Controllers;

use App\Http\Controllers\Controller;
use App\Models\Category;
use LaveroAdmin\Admin\Form;
use LaveroAdmin\Admin\Facades\Admin;
use LaveroAdmin\Admin\Layout\Content;
use LaveroAdmin\Admin\Controllers\ModelForm;
use LaveroAdmin\Admin\Tree;

class CategoryController extends Controller
{
    use ModelForm;

    public function index()
    {
        return Admin::content(function (Content $content) {
            $content->header('Categories');
            $content->body(Category::tree());
        });
    }
}
```

You can modify the display of branch in the following ways:

```
Category::tree(function ($tree) {
    $tree->branch(function ($branch) {
        $src = config('admin.upload.host') . '/' . $branch['logo'] ;
        $logo = "<img src='$src' style='max-width:30px;max-height:30px' class='img'/>";

        return "{$branch['id']} - {$branch['title']} $logo";
    });
})
```

The `$branch` parameter is array of current row data.

If you want to modify the query of the model, use the following way:

```
Category::tree(function ($tree) {

    $tree->query(function ($model) {
        return $model->where('type', 1);
    });

})
```
