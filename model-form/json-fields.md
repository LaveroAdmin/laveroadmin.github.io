# JSON format field processing

Model form provides the following components to process fields in `JSON` format, which is convenient for processing objects in the format of `JOSN`, one-dimensional arrays, two-dimensional arrays and other objects.

Before using the following components, **must** first set the cast of this field in the model

```
class Foo extends Model
{
    protected $casts = [
        'column_name' =>'json',
    ];
}
```

## Key-value objects

> **Preview:** *Keyvalue*

If your field stores the `{"field":"value"}` format , you can use the `keyValue` component:

```
$form->keyValue('column_name');

// Set verification rules
$form->keyValue('column_name')->rules('required|min:5');

// enable sorting
$form->keyValue('column_name')->sortable();
```

## Fixed key-value object

> **Preview:** *Embeds*

Used to process `JSON` type field data of `mysql` or `object` type data of `mongodb`, and also store data values ​​of multiple fields in the form of `JSON` string in `mysql` string type In the field

Applicable to JSON type fields with fixed key values

```
$form->embeds('column_name', function ($form) {

    $form->text('key1')->rules('required');
    $form->email('key2')->rules('required');
    $form->datetime('key3');

    $form->dateRange('key4','key5','Range')->rules('required');
});

// Custom title
$form->embeds('column_name','Field title', function ($form) {
    ...
});

// Enable sortable
$form->embeds('column_name', function ($form) { ... })->sortable();
```

The method call inside the callback function to construct the form element is the same as outside.

## One-dimensional array

> **Preview:** *List*

If your field is used to store a one-dimensional array in the format [[foo], "Bar"], you can use the list component:

```
$form->list('column_name');

// Set verification rules
$form->list('column_name')->rules('required|min:5');

// Enable sortable
$form->list('column_name')->sortable();
```

## Two-dimensional array

> **Preview:** *Table*

If a field stores a two-dimensional array in `json` format, you can use the `table` form field:

```
$form->table('column_name', function ($table) {
    $table->text('key')->icon("icon-key");
    $table->text('value');
    $table->text('desc')->icon("icon-info-circle");
});

// to align fields at the top / middle / bottom use:
$form->table('column_name', function ($table) { ...})->verticalAlign("middle"); // top, middle , bottom

// If you want empty values not be save in your json arr set this to false
// by default values will saved as null, for more info see: Kernel.php: \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
$form->table('column_name', function ($table) { ...})->saveNullValues(false); // true or false

// enable sortable
$form->table('column_name', function ($table) { ...})->sortable();
```

At the same time, you need to set the accessor and modifier for this field in the model:

```
public function getColumnNameAttribute($value)
{
    return array_values(json_decode($value, true) ?: []);
}

public function setColumnNameAttribute($value)
{
    $this->attributes['column_name'] = json_encode(array_values($value));
}
```

This component is similar to the `hasMany` component, but it is used to handle the case of a single field and is suitable for simple two-dimensional data.
