# Model-Form

The `LaveroAdmin\Admin\Form` class is used to generate a data model-based form. For example, there is a`movies` table in the database

```
movies
    id          - integer
    title       - string
    director    - integer
    describe    - string
    rate        - tinyint
    released    - enum(0, 1)
    release_at  - timestamp
    created_at  - timestamp
    updated_at  - timestamp
```

The corresponding data model is `App\Models\Movie`, and the following code can generate the`movies` data form:

```
use App\Models\Movie;
use LaveroAdmin\Admin\Form;
use LaveroAdmin\Admin\Facades\Admin;

$form = new Form(new Movie);

// Displays the record id
$form->display('id', 'ID');

// Add an input box of type text
$form->text('title', 'Movie title');

$directors = [
    'John'  => 1,
    'Smith' => 2,
    'Kate'  => 3,
];

$form->select('director', 'Director')->options($directors);

// Add textarea for the describe field
$form->textarea('describe', 'Describe');

// Number input
$form->number('rate', 'Rate');

// Add a switch field
$form->switch('released', 'Released?');

// Add a date and time selection box
$form->dateTime('release_at', 'release time');

// Display two time column
$form->display('created_at', 'Created time');
$form->display('updated_at', 'Updated time');
```

## Custom tools

The top right corner of the form has two button tools by default. You can modify it in the following way:

```
$form->tools(function (Form\Tools $tools) {

    // Disable `List` btn.
    $tools->disableList();

    // Disable `Delete` btn.
    $tools->disableDelete();

    // Disable `Veiw` btn.
    $tools->disableView();

    // Add a button, the argument can be a string, or an instance of the object that implements the Renderable or Htmlable interface
    $tools->add('<a class="btn btn-sm btn-danger"><i class="fa fa-trash"></i>&nbsp;&nbsp;delete</a>');
});
```

## Form footer

Use the following method to remove the elements of the form foot

```
$form->footer(function ($footer) {

    // disable reset btn
    $footer->disableReset();

    // disable submit btn
    $footer->disableSubmit();

    // disable `View` checkbox
    $footer->disableViewCheck();

    // disable `Continue editing` checkbox
    $footer->disableEditingCheck();

    // disable `Continue Creating` checkbox
    $footer->disableCreatingCheck();

});
```

## Other methods

Disable submit btn:

```
$form->disableSubmit();
```

Disable reset btn:

```
$form->disableReset();
```

Ignore fields to store

```
$form->ignore('column1', 'column2', 'column3');
```

Set width for label and field

```
$form->setWidth(10, 2);
```

Set form action

```
$form->setAction('admin/users');
```

Determine if the current form page is creating or updating

```
$form->isCreating();

$form->isEditing();
```

Submit confirmation

```
$form->confirm('Are you sure you want to save this page?', 'edit');

$form->confirm('Are you sure you want create this page？', 'create');

$form->confirm('Are you sur eyou want to save？');
```
