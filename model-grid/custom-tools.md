# Custom tools

## Custom header tools

`model-grid` has `batch delete` and `refresh` operations tools as default, `model-grid` provides custom tool functionality if there are more operational requirements, the following example will show you how to add a `Gender selector` button group tool.

First define the tool class `app/Admin/Extensions/Tools/UserGender.php`：

```
<?php

namespace App\Admin\Extensions\Tools;

use LaveroAdmin\Admin\Admin;
use LaveroAdmin\Admin\Grid\Tools\AbstractTool;
use Illuminate\Support\Facades\Request;

class UserGender extends AbstractTool
{
    protected function script()
    {
        $url = Request::fullUrlWithQuery(['gender' => '_gender_']);

        return <<<EOT
document.querySelectorAll('.user-gender').forEach(el => {
    el.addEventListener('click',function () {
        var url = "$url".replace('_gender_', this.dataset.option);
        admin.ajax.navigate( url);
    })
});
EOT;
    }

    public function render()
    {
        Admin::script($this->script());

        $options = [
            'all'   => 'All',
            'm'     => 'Male',
            'f'     => 'Female',
        ];

        return view('admin.tools.gender', compact('options'));
    }
}
```

The blade file of view `admin.tools.gender` is `resources/views/admin/tools/gender.blade.php`:

```
<div class="btn-group" data-toggle="buttons">
    @foreach($options as $option => $label)
    <label class="btn btn-sm user-gender {{ \Request::get('gender', 'all') == $option ? 'btn-secondary' : 'btn-light' }}" data-option="{{$option}}">
        {{$label}}
    </label>
    @endforeach
</div>
```

Import this tool in `model-grid` and apply to model query：

```
use App\Admin\Extensions\Tools\UserGender;

$grid->tools(function ($tools) {
    $tools->append(new UserGender());
});

// and pass `gender` query to model：
if (in_array(Request::get('gender'), ['m', 'f'])) {
    $grid->model()->where('gender', Request::get('gender'));
}
```

## Disable Batch Operations

At present, the default implementation of the batch delete operation, if you want to turn off the batch delete operation:

```
$grid->tools(function ($tools) {
    $tools->batch(function ($batch) {
        $batch->disableEdit();
        $batch->disableDelete();
    });
});
// or by using batchActons
$grid->batchActions(function ($batch) {
    $batch->disableEdit();
    $batch->disableDelete();
});
```

## Custom Batch operation

If you want to add a custom batch operation, you can refer to the following example.

The following example will show you how to implements a `post batch release` operation:

First define the tool class `app/Admin/Extensions/Tools/ReleasePost.php`：

```
<?php

namespace App\Admin\Extensions\Tools;

use LaveroAdmin\Admin\Grid\Tools\BatchAction;

class ReleasePost extends BatchAction
{
    protected $action;

    public function __construct($action = 1)
    {
        $this->action = $action;
    }

    public function script()
    {
        return <<<EOT

document.querySelector('{$this->getElementClass()}').addEventListener('click', function(e) {
    let url = '{$this->resource}/release';
    let data = {
        ids: admin.grid.selected,
        action: {$this->action}
    };

    admin.ajax.post(url,data,function(data){
        if(data.status == 200){
            admin.ajax.reload();
            if (data.data == 1){
                admin.toastr.success('Released');
            }else{
                admin.toastr.success('Un Released');
            }
        }
    })
    e.preventDefault();
});

EOT;

    }
}
```

See the code above, use ajax to pass the selected `ids` to back-end api through a POST request, the back-end api modifies the state of the corresponding data according to the received `ids`, and then front-end refresh the page (pjax reload), and pop-up a `toastr` prompt operation is successful.

Import this operation in `model-grid`：

```
$grid->batchActions(function ($batch) {
    $batch->add('Release page', new \App\Admin\Extensions\Tools\ReleasePage(1));
    $batch->add('Unrelease page', new \App\Admin\Extensions\Tools\ReleasePage(0));
});
// or
use App\Admin\Extensions\Tools\ReleasePage;
...
$grid->batchActions(function ($batch) {
    $batch->add('Release page', new ReleasePage(1));
    $batch->add('Unrelease page', new ReleasePage(0));
});
```

So that the batch operation of the drop-down button will add the following two operations, the final step is to add an api to handle the request of the batch operation, the api code is as follows:

```
use Illuminate\Support\Facades\Request;

class PageController extends Controller
{
    ...

    public function release(Request $request)
    {
        foreach (Page::find($request::post('ids')) as $page) {
            $page->status = $request::post('action');
            $page->save();
        }
        return $request::post('action');
    }

    ...
}
```

Then add a route for the api above:

```
use App\Admin\Controllers\PageController as RoutePageController;

$router->post('pages/release', [RoutePageController::class,'release']);
```

This completes the entire process.
