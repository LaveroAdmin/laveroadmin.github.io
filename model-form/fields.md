# Builtin form fields

There are a lots of form components built into the `model-form` to help you quickly build forms.

## Public methods

### Set the value

```
$form->text('columnName','fieldLabel')->value('text...');
```

### Set default value

```
$form->text('columnName')->default('text...');

// also accepts a closure function
$form->text('columnName')->default(function ($form){
    return 'some value';
});
```

### Set default value on empty

Sets the value to this value when storing an empty value in the database

```
$form->text('columnName')->defaultOnEmpty('value_for_db');

// also accepts a closure function
$form->text('columnName')->defaultOnEmpty(function ($form){
    return 'some value';
});
```

### Set help message

```
$form->text('columnName')->help('help...');
```

### Set attributes of field element

```
$form->text('columnName')->attribute(['data-title' => 'title...']);

$form->text('columnName')->attribute('data-title', 'title...');
```

### Set placeholder

```
$form->text('columnName')->placeholder('Please input...');
```

### Set required

```
$form->text('columnName')->required();
```

### Setting pattern

```
$form->text('columnName')->pattern('[A-z]{3}');
```

### Setting readonly

```
$form->text('columnName')->readonly();
```

since: v1.0.23 now also accecepts false to turn readonly off.

```
$form->text('columnName')->readonly($boolean);
```

Can be nicely used in combination with the default/value for settings values from the query string

```
$form
    ->number('comment_id', __('Comment id'))
    ->default((request()->comment_id ?? ''))
    ->readonly((request()->comment_id ? true : false));
```

### Setting disable

```
$form->text('columnName')->disable();
```

### Setting disabled

since: v1.0.23

```
$form->text('columnName')->disabled($boolean);
```

### Setting autofocus

```
$form->text('columnName')->autofocus();
```

### Show As Section

Creates as page wide section title for the field label

```
$form->text('columnName')->showAsSection();
```

### Model-form-tab

If the form contains too many fields, will lead to form page is too long, in which case you can use the tab to separate the form:

```
$form->tab('Basic info', function ($form) {

    $form->text('title');
    $form->email('email');
    $form->url('url');

})->tab('Seo', function ($form) {

   $form->text('meta_title');
   $form->textarea('meta_description');
   $form->text('meta_title');

})->tab('Image', function ($form) {

    $form->image('thumbnail');
    $form->multipleImage('images', __('Images'));

});
```

> **Preview:** *Model form tabs*

## Text input

> **Preview:** *Text*

```
$form->text($column, [$label]);

// Add a submission validation rule
$form->text($column, [$label])->rules('required|min:10');

// Set FontAwesome icon
$form->text($column, [$label])->icon('fa-pencil');

// Set datalist
$form->text($column, [$label])->datalist(['key' => 'value']);

// Set inputmask, see https://github.com/RobinHerbots/Inputmask
$form->text('code')->inputmask(['mask' => '99-9999999']);
```

## Textarea

> **Preview:** *Textarea*

```
$form->textarea('column','label')->rows(10);
```

## Radio

Inline

> **Preview:** *Radio*

```
$form->radio('column','label')->options(['1' => 'Option 1', '2' => 'Option 2','3' => 'Option 3'])->default('1');
```

Stacked

> **Preview:** *Radio stacked*

```
$form->radio('column','label')->options(['1' => 'Option 1', '2' => 'Option 2','3' => 'Option 3'])->default('1')->stacked();
```

## Checkbox

> **Preview:** *Checkbox*

`checkbox` can store values in two ways, default is stored as json, but can be modified by the model, see [multiple select](#multiple-select "#multiple-select")

The `options()` method is used to set options:

```
$form->checkbox('column','label')->options([1 => 'foo', 2 => 'bar', 'val' => 'Option name']);

$form->checkbox('column','label')->options([1 => 'foo', 2 => 'bar', 'val' => 'Option name'])->stacked();

// Setting options through closures
$form->checkbox('column','label')->options(function () {
    return [1 => 'foo', 2 => 'bar', 'val' => 'Option name'];
});
```

## Select

> **Preview:** *Select*

```
$form->select('column','label')->options([1 => 'foo', 2 => 'bar', 'val' => 'Option name']);
```

Using a related model (don't use with large datasets, then use ajax)

```
$form->select('relation_id', __("relation label"))->options(ModelName::all()->pluck('title', 'id'));
```

If have too many options, you can load option by ajax:

```
$form->select('user_id')->options(function ($id) {
    $user = User::find($id);

    if ($user) {
        return [$user->id => $user->name];
    }
})->ajax('/admin/api/users');
```

The controller method for api `/admin/api/users` is:

```
use App\Models\User;
use Illuminate\Support\Facades\Request;
...
public function users(Request $request)
{
    $query = $request->input("query");
    return User::where('name', 'like', "%$query%")->limit(10)->get(['id', 'name as text']);
}
```

The json returned from api `/admin/api/users`:

```
[
    {"id":1,"text":"User 1"},
    {"id":2,"text":"User 2"},
    {"id":3,"text":"User 3"},
    ...
]
```

### Cascading select

`select` component supports one-way linkage of parent-child relationship:

```
$form->select('province')->options(...)->load('city', '/api/city');

$form->select('city');
```

Where `load('city', '/api/city');` means that, after the current select option is changed, the current option will call the api `/api/city` via the argument`q` api returns the data to fill the options for the city selection box, where api `/api/city` returns the data format that must match:

```
[
    {"id": 1,"text": "foo"},
    {"id": 2,"text": "bar"},
    ...
]
```

The code for the controller action is as follows:

```
public function city(Request $request)
{
    $provinceId = $request->get('q');
    return Province::city()->where('province_id', $provinceId)->get(['id', DB::raw('name as text')]);
}
```

## Multiple select

> **Preview:** *Multipleselect*

```
$form->multipleSelect('column','label')->options([1 => 'foo', 2 => 'bar', 'val' => 'Option name', 'two' => 'Option Two']);
```

You can store value of multiple select in two ways, one is `many-to-many` relation.

```
class Post extends Models
{
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
}

$form->multipleSelect('tags')->options(Tag::all()->pluck('name', 'id'));
```

The second is to store the option array into a single field. If the field is a string type, it is necessary to define [accessor and Mutator](https://laravel.com/docs/8.x/eloquent-mutators "https://laravel.com/docs/8.x/eloquent-mutators") for the field.

For example, the field `tags` is stored as a string and separated by a comma `,`, then its accessors and modifiers are defined as follows:

```
class Post extends Model

    public function getTagsAttribute($value)
    {
        return explode(',', $value);
    }

    public function setTagsAttribute($value)
    {
        $this->attributes['tags'] = implode(',', $value);
    }
}
```

If have too many options, you can load option by ajax

```
$form->select('user_id')->options(function ($ids) {
    // please note that the options have a different format as the ajax result.
    return User::find($ids)->pluck("name","id");
})->ajax('/admin/api/users');
```

The controller method for api `/admin/api/users` is:

```
public function users(Request $request)
{
    $q = $request->get('q');

    return User::where('name', 'like', "%$q%")->paginate(null, ['id', 'name as text']);
}
```

## Listbox

> **Preview:** *Listbox*

The usage is as same as mutipleSelect.

```
$form->listbox('column','label')->options([1 => 'foo', 2 => 'bar', 'val' => 'Option name']);

// Set height
$form->listbox('column','label')->height(200);
```

## Email input

> **Preview:** *Email*

```
$form->email('column','label');
```

## Password input

> **Preview:** *Password*

```
$form->password('column','label');

// added in V1.0.19
$form->password('column','label')->toggleShow(); // default true
$form->password('column','label')->toggleShow(false); // hide
```

Please note that passwords in this field are **not secure** by default

## URL input

> **Preview:** *Url*

```
$form->url('column','label');
```

## Ip input

> **Preview:** *Ip*

```
$form->ip('column','label');
```

## Phone number input

> **Preview:** *Phonenumber*

```
$form->phonenumber('column','label')->options(['mask' => '999 9999 9999']);
```

## Color selector

> **Preview:** *Color*

```
$form->color('title', 'color')->default('#ccc')->alpha(false)->format('hex');
//format can be: hex / rgb / hsl / mixed
// or use options:
$form->color('title', 'color')->options(["alpha"=>false,"format"=>"hex]);
//See: https://github.com/mdbassit/Coloris/ for options
```

# Date & Time inputs

Date & time inputs are based on the [FlatPickr](https://flatpickr.js.org/ "https://flatpickr.js.org/") with a bit of custom styling.
Take a look at the options: [https://flatpickr.js.org/options/](https://flatpickr.js.org/options/ "https://flatpickr.js.org/options/") to see what settings can be passed with the `->options({})` function

```
$form->time('column','label')->options({'format':'HH:mm:ss'});
$form->date('column','label')->options({'enableTime':false});
```

## Time input

> **Preview:** *Time*

```
$form->time('column','label');

// Set the time format, more formats reference http://momentjs.com/docs/#/displaying/format/
$form->time('column','label')->format('HH:mm:ss');
```

## Date input

> **Preview:** *Date*

```
$form->date('column','label');

// Date format setting, more format please see http://momentjs.com/docs/#/displaying/format/
// Please note that the database field must be compatible with the format, so either format with the value inside your model or use varchar as field type (nahh, better just format it :- )
$form->date('column','label')->format('d-m-Y');
```

## Datetime input

> **Preview:** *Datetime*

```
$form->datetime('column','label');

// Set the date format, more format reference http://momentjs.com/docs/#/displaying/format/
$form->datetime('column','label')->format('YYYY-MM-DD HH:mm:ss');
```

## Time range select

> **Preview:** *Timerange*

`$startTime`、`$endTime`is the start and end time fields:

```
$form->timeRange($startTime, $endTime, 'Time Range');
```

## Date range select

> **Preview:** *Daterage*

`$startDate`、`$endDate`is the start and end date fields:

```
$form->dateRange($startDate, $endDate, 'Date Range');
```

## Datetime range select

> **Preview:** *Datetime range*

`$startDateTime`、`$endDateTime` is the start and end datetime fields:

```
$form->datetimeRange($startDateTime, $endDateTime, 'DateTime Range');
```

## Currency input

```
$form->currency('column','label');

// set the unit symbol
$form->currency('column','label')->symbol('￥');
```

## Number input

```
$form->number('column','label');

// set max value
$form->number('column','label')->max(100);

// set min value
$form->number('column','label')->min(10);
```

## Rate input

```
$form->rate('column','label');
```

## Slider

Can be used to select the type of digital fields, such as age:

```
$form->slider('column','label')->attributes(['max' => 100, 'min' => 1, 'step' => 1]);
```

## Rich text editor

The rich text editing components are maintained outside this project, you can activate them using plugins, please choose one of the following rich text editor extension:

| Extension | Url |
| --- | --- |
| Ckeditor | [https://github.com/LaveroAdmin/ckeditor](https://github.com/LaveroAdmin/ckeditor "https://github.com/LaveroAdmin/ckeditor") |

// more will follow soon

## Hidden field

```
$form->hidden($column);
```

## Switch

> **Preview:** *Switch*

`On` and `Off` pairs of switches with the values `1` and`0`:

```
$form->switch('column','label');
```

## Map

> **Preview:** *Map*

The map field refers to the network resource, and if there is a problem with the network refer to [form Component Management](field-management.md) to remove the component.

Used to select the latitude and longitude, provide 2 table columns for this field.

```
$form->map('column_latitude', 'column_longitude', 'label');

// set default lat lng
$form->map('column_latitude', 'column_longitude', 'label')->default([
    'lat' => 51.378900135815,
    'lng' => 41.9005728960037,
]);

// in config/admin.php
/*
| Supported: "openstreetmaps", "tencent", "google", "yandex".
|
*/
'map_provider' => 'openstreetmaps',
```

## Display field

Only display the fields and without any action:

```
$form->display('column','label');
```

## Divider

```
$form->divider();

// OR

$form->divider('Title');
```

## Html

insert html,the argument passed in could be objects which impletements `Htmlable`、`Renderable`, or has method `__toString()`

```
$form->html('html contents');
```

## Tags

> **Preview:** *Tags*

Insert the comma (,) separated string `tags`

```
$form->tags('column','label');
```

## Icon

> **Preview:** *Icon*

Select the `font-awesome` icon.

```
$form->icon('column','label');
```

## Timezone

> **Preview:** *Timezone*

```
$form->timezone('conlumn','label');
```
