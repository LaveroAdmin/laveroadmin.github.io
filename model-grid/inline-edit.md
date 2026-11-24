# Inline editing

The data table has a number of methods to help you edit the data directly in the table.

> IMPORTANT! Note: For each inline edit column, you need to have a corresponding field in the form.

## Popovers edits

The grid has a couple of methods, allowing you to click on the data in the table and edit the saved data in the pop-up dialog box.

### Text input

```
$grid->column('title')->text();
```

> **Preview:** *Column inline edit text*

### Textarea input

```
$grid->column('title')->textarea());
```

> **Preview:** *Column inline edit textarea*

### Select

The second parameter is the option selected by select

```
$grid->column('title')->select([1 => 'option1', 2 => 'option2', 3 => 'option3']);
```

> **Preview:** *Column inline edit select*

### Date selection

```
$grid->column('birth')->date();
```

> **Preview:** *Column inline edit date*

### Date and time selection

```
$grid->column('published_at')->datetime($options = []);
// $options passes settings object to flatpickr (date selector);
```

> **Preview:** *Column inline edit datetime*

### Time selection

```
$grid->column('time')->time();
```

> **Preview:** *Column inline edit time*

### Radio

Set the column to the `radio` component and set the same `radio` for these fields in the `form` method.

```
$grid->column('options')->radio([
                1 => 'Sed ut perspiciatis unde omni',
                2 => 'voluptatem accusantium doloremque',
                3 => 'dicta sunt explicabo',
                4 => 'laudantium, totam rem aperiam',
]);
```

> **Preview:** *Column inline edit radio*

### Checkbox

Set this column to the `checkbox` component and also set the same `checkbox` for these fields in the `form` method.

```
$grid->column('options')->checkbox([
                1 => 'Sed ut perspiciatis unde omni',
                2 => 'voluptatem accusantium doloremque',
                3 => 'dicta sunt explicabo',
                4 => 'laudantium, totam rem aperiam',
]);
```

> **Preview:** *Column inline edit checkbox*

## In-Table edits

The grid also has options to switch values in the table itself, Single or grouped.

### Switch

> Note: In the `grid`, set a `switch` for a field, and you need to set the same `switch` for the field in `form`.

Quickly turn the column into a switch component, using the following:

```
$grid->column('status')->switch();

// set text, color, and stored values
$states = [
                'on' => ['value' => 1, 'text' => 'open', 'color' => 'primary'],
                'off' => ['value' => 2, 'text' => 'close', 'color' => 'default'],
];
$grid->column('status')->switch($states);
```

> **Preview:** *Column inline edit swtich*

### Switch group

> Note: `switch` is set for some fields in `grid`, and the same `switch` should be set for these fields in `form`.

Quickly turn the column into a switch component group, using the following:

```
$states = [
                'on' => ['text' => 'YES'],
                'off' => ['text' => 'NO'],
];

$grid->column('switch_group')->switchGroup([
                'hot' => 'hot',
                'new' => 'latest',
                'recommend' => 'recommended',
], $states);
```

> **Preview:** *Column inline edit switchgroup*

## Relationsships

Please rever to [model-grid-edit-relationships](edit-relationships.md)
