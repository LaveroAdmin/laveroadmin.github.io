# Model grid row actions

A grid row has actions and batchActions these can be extended and shown as either '

## Action views

There are three different views (displayers) for the actions. These are:

* `Actions`
* `DropdownActions`
* `ContextMenuActions`

They can by either set in the `config/admin.php` by:

```
/*
|--------------------------------------------------------------------------
| The global Grid action display class. (Actions::class, DropdownActions::class or ContextMenuActions::class)
|--------------------------------------------------------------------------
*/
'grid_action_class' => \LaveroAdmin\Admin\Grid\Displayers\Actions\Actions::class, // this is the default class
'grid_action_class' => \LaveroAdmin\Admin\Grid\Displayers\Actions\DropdownActions::class,
'grid_action_class' => \LaveroAdmin\Admin\Grid\Displayers\Actions\ContextMenuActions::class,
```

Or by you set this in a controller by using:

```
use LaveroAdmin\Admin\Grid\Displayers\Actions\DropdownActions;

$grid->setActionClass(DropdownActions::class);
```

### Actions

> **Preview:** *Actions buttons*

You can show them with or without labels

```
//
$grid->actions(function ($actions) {
  $actions->showLabels(true);
  //$actions->showLabels(false);
});
```

### Dropdown

> **Preview:** *Actions dropdown*

### ContextMenu Actions

ContextMenu Actions: the action column does not appear in the last column of the table. Right-click on the list to display the action drop-down menu.

## Default actions

`model-grid` By default, there are three actions `show`,`edit` and `delete`. The actions of a row can be extened using [custom actions](#custom-actions "#custom-actions") which can be turned off in the following way:

```
$grid->actions(function ($actions) {
  $actions->disableDelete();
  $actions->disableEdit();
  $actions->disableShow();
});
```

You can get the data for the current row by `$actions` parameter passed in:

```
$protect_ids = [1, 2];
$grid->actions(function ($actions) use ($protect_ids) {
  // the array of data for the current row
  $actions->row;

  // gets the current row primary key value
  $actions->getKey();

  // disabled delete
  if (in_array($actions->getkey(), $protect_ids)) {
    $actions->disableDelete();
  }
});
```

## Custom Actions

Suppose you want to add a `copy` action to the article list. After clicking and copying the data of this line, run the following command to generate the action class:
Also known as: duplicate / dublicate

```
php artisan admin:action Replicate --grid-row --name="copy"
```

The above command will generate the class file `app/Admin/Actions/Replicate.php`:

```
<?php

namespace App\Admin\Actions;

use LaveroAdmin\Admin\Actions\RowAction;
use Illuminate\Database\Eloquent\Model;

class Replicate extends RowAction
{
    public $name = 'copy';

    public $icon = 'icon-copy';

    public function handle(Model $model)
    {
        // $model ...
        return $this->response()->success('Success message.')->refresh();
    }
}
```

The commit of the action will handle by the `handle` method in the class, so we only need to simply modify the logic of the `handle` method:

```
public function handle(Model $model)
{
    // Here the model's `replicate` method is called to copy the data, then call the `save` method to save it.
    $model->replicate()->save();

    // return a success message with the content "copy success" and refresh the page
    return $this->response()->success('copy success.')->refresh();
}
```

The first parameter `$model` of the `handle` method is the Eloquent model of the current row data, which can be manipulated directly.

The final step is to add to the grid:

```
use App\Admin\Actions\Post\Replicate;

$grid->actions(function ($actions) {
  $actions->add(new Replicate());
});
```

This is added to the buttons or action drop-down menu:

> **Preview:** *Actions buttons replicate*
> **Preview:** *Actions dropdown replicate*

### Popup dialogue box

For example, the above copy action, after clicking, you need to pop up a dialog box to confirm the action, add a dialog method to the class:

```
    public function dialog()
    {
        $this->question('Are you sure to copy this row?', 'This will copy all the data into a new entity', ['icon'=>'question','confirmButtonText'=>'Yes']);
    }
```

In this way, after clicking Copy, the following confirmation box will pop up, and the action will be submitted after confirmation.

> **Preview:** *Actions dialog*

### Page redirection

If your `row action` just click to jump to another page:

```
<?php

namespace App\Admin\Actions;

use LaveroAdmin\Admin\Actions\RowAction;

class ViewComments extends RowAction
{
    public $name = 'View comments';

    public $icon = 'icon-comments';

    /**
        * @return  string
        */
    public function href()
    {
        return "/your/uri/path";
    }
}
```

Save as `App/Admin/Actions/ViewComments.php` and add to the grid:

```
use App\Admin\Actions\ViewComments;

$grid->actions(function ($actions) {
    $actions->add(new ViewComments());
});
```

In the `href` method, you can use `$this->getResource()` to get the current resource root path. You can use `$this->getKey()` to get the primary key value of the current row.

### Popup form

Suppose that the 'list' has a report action. After clicking, you need to pop up a form, fill in the type and reason of the report, and refer to the following method:

You can add the form() method to your actions class:

```
public function form()
{
    $types = [
        1 => 'Advertising',
        2 => 'Illegal',
        3 => 'Fishing',
    ];
    $this->date('date', 'date');
    $this->checkbox('type', 'type')->options($types);
    $this->textarea('body', 'body')->rules('required');
}
```

When the action is clicked, a `form` form pops up:

> **Preview:** *Actions form*

In the `handle` method, we need to add a second parameter to get the form value:

```
use Illuminate\Http\Request;

public function handle(Model $model, Request $request)
{
    $model->date = $request->get('date');
    $model->type = $request->get('type');
    $model->body = $request->get('body');
    $model->save();

    return $this->response()->success('Success message.')->refresh();
}
```

The current method support for the form reference:

```
public function form()
{
    // disable filling the form with values from the related model
    $this->addValues(false); // must be placed on top, default = true

    // text input box
    $this->text('name', 'label')->rules('required|min:10');

    // Email input box
    $this->email('name', 'label');

    // IP input box
    $this->ip('name', 'label');

    // URL input box
    $this->url('name', 'label');

    // password input box
    $this->password('name', 'label');

    // mobile phone number input box
    $this->mobile('name', 'label');

    // text field input box
    $this->textarea('name', 'label');

    // Single box
    $this->select('name', 'label')->options([]);

    // Checkbox
    $this->multipleSelect('name', 'label')->options([]);

    // Checkbox
    $this->checkbox('name', 'label')->options([]);

    // Radio
    $this->radio('name', 'label')->options([]);

    // upload files
    $this->file('name', 'label');

    // upload image
    $this->image('name', 'label');

    // time and date selection
    $this->datetime('name', 'label');

    // date selection
    $this->date('name', 'label');

    // time selection
    $this->time('name', 'label');

    // hide
    $this->hidden('name');
}
```

## Batch action

Suppose you want to add a `batch copy` action, first run the following command to generate a batch action class:

```
php artisan admin:action BatchReplicate --grid-batch --name="Batch copy"
```

Generate the class file `app/Admin/Actions/BatchReplicate.php`:

```
<?php

namespace App\Admin\Actions;

use LaveroAdmin\Admin\Actions\BatchAction;
use Illuminate\Database\Eloquent\Collection;

class BatchReplicate extends BatchAction
{
    public $name = 'Batch copy';
    public $icon = 'icon-copy';

    public function handle(Collection $collection)
    {
        foreach ($collection as $model) {
            // ...
        }

        return $this->response()->success('Success message...')->refresh();
    }
}
```

Similarly, we only need to simply modify the logic of the `handle` method:

```
public function handle(Collection $collection)
{
    // copy the data model for each row
    foreach ($collection as $model) {
        $model->replicate()->save();
    }

    / / return a success message of "copy success" and refresh the page
    return $this->response()->success('copy success.')->refresh();
}
```

Add to the batch action:

```
use App\Admin\Actions\BatchReplicate;

$grid->batchActions(function ($batch) {
    $batch->add(new BatchReplicate());
});
```

Then select a few rows of data in the list, click on it to see the new `batch copy`:

> **Preview:** *Batch copy*

## Grid Tools

Custom actions, in addition to being displayed in the action column of the data table and in the drop-down menu of the batch action, can also be displayed in the tool area of ​​the table (Filter button line)

### Batch action

If you want to put the button for the batch action in the tool area instead of the drop-down menu for the batch action, you need to make some changes to the action class.

*Note: row values are not passed into the form like with forms on row-actions.*

```
<?php

namespace App\Admin\Actions\Page;

use LaveroAdmin\Admin\Actions\BatchAction;
use Illuminate\Database\Eloquent\Collection;
use Illuminate\Http\Request;

class ReportPage extends BatchAction
{
    protected $selector = '.report-pages';

    public function handle(Collection $collection, Request $request)
    {
        foreach ($collection as $model) {
            $model->reason = $request->input("reason");
            $model->save();
        }

        return $this->response()->success('Report submitted!')->refresh();
    }

    public function form()
    {
        $this->textarea('reason', 'reason')->rules('required');
    }

    public function html()
    {
        // '.show-on-rows-selected' toggles '.d-none' when rows are seleted
        return "<a class='report-pages btn btn-sm btn-danger show-on-rows-selected d-none me-1'><i class='icon-info-circle'></i>Report</a>";
    }
}
```

The value of `$selector` corresponds to the button CSS selector in the `html` method. Click the 'Report' button to submit the action.

Then add it to the toolbar of the form:

```
use App\Admin\Actions\Page\ReportPage;

$grid->tools(function (Grid\Tools $tools) {
    $tools->append(new \App\Admin\Actions\Page\ReportPage());
});
```

The display effect is as follows; a report button with a form:

> **Preview:** *Actions tools*

### Normal action

Suppose you need to add an 'Import Data` button to the header of the form. After clicking, pop up the form upload file to import the data. Refer to the steps below.

Create a normal action class by running the following command:

```
php artisan admin:action Page\\ImportPage --name="import page"
```

The generated class file `app/Admin/Actions/Page/ImportPage.php`:

```
<?php

namespace App\Admin\Actions\Page;

use LaveroAdmin\Admin\Actions\Action;
use Illuminate\Http\Request;

class ImportPage extends Action
{
    protected $selector = '.import-page';

    public function handle(Request $request)
    {
        // $request ...

        return $this->response()->success('Success message...')->refresh();
    }

    public function html()
    {
        return <<<HTML
        <a class="btn btn-sm btn-default import-post">Import page</a>
HTML;
    }
}
```

Modify this class to provide the ability to upload files and add logic to process the data:

```
<?php

namespace App\Admin\Actions\Page;

use LaveroAdmin\Admin\Actions\Action;
use Illuminate\Http\Request;

class ImportPage extends Action
{
    public $name = 'import page';

    protected $selector = '.import-page';

    public function handle(Request $request)
    {
        // The following code gets the uploaded file, then uses the package `maatwebsite/excel` to process and upload your file and save it to the database.
        $path = $request->file('file');

        return $this->response()->success('Import complete!'.$path)->refresh();
    }

    public function form()
    {
        $this->file('file', 'Please select file');
    }

    public function html()
    {
        return <<<HTML
        <a class="btn btn-sm btn-light import-page"><i class="icon-upload"></i>Import Page</a>
HTML;
    }
}
```

> **Preview:** *Mode grid action normal*

> **Preview:** *Mode grid action normal form*

## Response

Here's an example of the simplest return:

```
return $this->response()->success('Success!');
```

After the action class is processed, return a `Success! The success of ``s tips, here are a few other types of returns:

```
// Handling errors
Try {
    // Processing logic...

    return $this->response()->success('success...');
} catch (Exception $e) {
    return $this->response()->error('generating error: '.$e->getMessage());
}

// return prompt message
return $this->response()->info('prompt information...');

// return warning message
return $this->response()->warning('warning information...');
```

Refresh the page after returning:

```
return $this->response()
  ->success('Success!')
  ->refresh();
```

Redirect to other pages after returning:

```
return $this->response()
  ->success('Success!')
  ->redirect('/admin/your-uri');
```

Download the file after returning:

```
return $this->response()
  ->success('Success!')
  ->download('http://www.xxx.com/file.zip');
```

The toastr message is displayed at the top center by default. You can modify its display position in the following ways:

```
// display on top right
return $this->response()->topRight()->success('Success!')->refresh();

// display in the middle of the bottom
return $this->response()-?bottomCenter()->error('Error!')->refresh();
```

The display position control methods are `topCenter`,`topLeft`, `topRight`,`bottomLeft`, `bottomCenter`,`bottomRight`, `topFullWidth`,`bottomFullWidth`.

The toastr message disappears after 5 seconds by default. You can control the disappearance time by the `timeout` method:

```
 // disappears after 3 seconds
 return $this->response()->success('Success!')->timeout(3000)->refresh();

 // never hide use -1
 return $this->response()->success('Success!')->timeout(-1)->refresh();
```

## Permission control

If you need to do the permission control after the action is submitted and before being processed, add an `authorize` method to the operation class by referring to the following method:

```
public function authorize($user, $model)
{
    return false;
}
```

The first parameter is the current operation user model, the second parameter is the data model or model collection of the current column, and the `authorize` method controls whether or not to interrupt the execution of the operation by returning `true` or `false`.
