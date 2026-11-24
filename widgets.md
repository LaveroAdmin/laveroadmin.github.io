# Web widgets

## Box

`LaveroAdmin\Admin\Widgets\Box` used to generate box components:

```
use LaveroAdmin\Admin\Widgets\Box;

$box = new Box('Box Title', 'Box content', 'Box footer');

// you can also use:
$box->header("My Footer");
$box->content("My Conten");
$box->footer("My Footer");

//box options
$box->removable();
$box->collapsable();
$box->styles(["border"=>"1px solid #FFAA00","margin-top"=>"20px"]);

echo $box; // this use the magic __toString function that calls the render() functions
// you can also use:
$box->render();
```

The `$content` parameter is the content element of the Box, which can be either an implementation of the `Illuminate\Contracts\Support\Renderable` interface, or other printable variables.

## Collapse

`LaveroAdmin\Admin\Widgets\Collapse` class used to generate folding components:

```
use LaveroAdmin\Admin\Widgets\Collapse;

$collapse = new Collapse();

$collapse->add('Bar', 'xxxxx');
$collapse->add('Orders', 'test');
$collapse->add('Last', 'last');

echo $collapse->render();
```

The `Collapse::add($title, $content)` method is used to add a collapsed item to the collapsing component. The `$title` parameter sets the title of the item. The`$content` parameter is used to .

## Form

`LaveroAdmin\Admin\Widgets\Form` class is used to quickly build a form：

```
$form = new Form();

$form->action('example');

$form->email('email')->default('qwe@aweq.com');
$form->password('password');
$form->text('name');
$form->url('url');
$form->color('color');
$form->map('lat', 'lng');
$form->date('date');
$form->json('val');
$form->dateRange('created_at', 'updated_at');

echo $form->render();
```

`Form::__construct($data = [])` generates a form object. If the `$data` parameter is passed, the elements in the`$data` array will be filled into the form.

`Form::action($uri)` method is used to set the form submission address.

`Form::method($method)` method is used to set the submit method of the form form, the default is `POST` method.

`Form::disablePjax()` disable pjax for form submit.

## Infobox

The `LaveroAdmin\Admin\Widgets\InfoBox` class is used to generate the information presentation block:

```
use LaveroAdmin\Admin\Widgets\InfoBox;

$infoBox = new InfoBox('New Users', 'users', 'success', '/admin/users', '1024');
$infoBox->setID("my-box-id");
echo $infoBox->render();

$infoBoxError = new InfoBox('Error', 'users', 'warning', '/admin/users', '22');
$infoBoxError->link("/admin/users?filter=error");
$infoBoxError->link_text("Show error users");
$infoBoxError->color("danger");
$infoBoxError->title("Error users");
$infoBoxError->icon("user-slash");
$infoBoxError->info("23");

echo $infoBoxError->render();
```

Refer to the section on the `InfoBox` in the`index()`method of the home page layout file [HomeController.php](https://github.com/LaveroAdmin/LaveroAdmin/blob/master/src/Console/stubs/HomeController.stub).

## Tab component

The `LaveroAdmin\Admin\Widgets\Tab` class is used to generate the tab components:

```
use LaveroAdmin\Admin\Widgets\Tab;

$tab = new Tab();

$tab->add('Pie', $pie);
$tab->add('Table', new Table());
$tab->add('Text', 'blablablabla....');

echo $tab->render();
```

The `Tab::ad ($title, $content)` method is used to add a tab, `$title` for the option title, and the`$content` tab for the content.

## Table

`LaveroAdmin\Admin\Widgets\Table` class is used to generate forms：

```
use LaveroAdmin\Admin\Widgets\Table;

// table 1
$headers = ['Id', 'Email', 'Name', 'Company'];
$rows = [
    [1, 'labore21@yahoo.com', 'Ms. Clotilde Gibson', 'Goodwin-Watsica'],
    [2, 'omnis.in@hotmail.com', 'Allie Kuhic', 'Murphy, Koepp and Morar'],
    [3, 'quia65@hotmail.com', 'Prof. Drew Heller', 'Kihn LLC'],
    [4, 'xet@yahoo.com', 'William Koss', 'Becker-Raynor'],
    [5, 'ipsa.aut@gmail.com', 'Ms. Antonietta Kozey Jr.'],
];

$table = new Table($headers, $rows);

echo $table->render();

// table 2
$headers = ['Keys', 'Values'];
$rows = [
    'name'   => 'Joe',
    'age'    => 25,
    'gender' => 'Male',
    'birth'  => '1989-12-05',
];

$table = new Table($headers, $rows);

echo $table->render();
```
