# LaveroAdmin - grid-sortable

This extension can help you sort by dragging the rows of the data list, the front end is based on [sortableJS](https://github.com/SortableJS/Sortable "https://github.com/SortableJS/Sortable"), and the back end is based on [eloquent-sortable](https://github.com/spatie/eloquent-sortable "https://github.com/spatie/eloquent-sortable")

> **Preview:** *Grid sortable*

## Installation

```
composer require laveroadmin/grid-sortable
```

## Usage

Define your model

```
<?php

use Illuminate\Database\Eloquent\Model;
use Spatie\EloquentSortable\Sortable;
use Spatie\EloquentSortable\SortableTrait;

class MyModel extends Model implements Sortable
{
    use SortableTrait;

    public $sortable = [
        'order_column_name' => 'order_column',
        'sort_when_creating' => true,
    ];
}
```

Use in grid

```
$grid = new Grid(new MyModel);

$grid->sortable();
```

This will add a column to the grid. After dragging one row, a `Save order` button will appear at the top of the grid. Click to save order.

## Translation

The default text for the button is `Save order`. If you use an other language, such as Simplified Chinese, you can add a translation to the `resources/lang/zh-CN.json` file.

```
{
    "Save order": "保存排序"
}
```

## License

Licensed under [The MIT License (MIT)](https://opensource.org/licenses/MIT).
