# Data export

## CSV Export

`model-grid` has built-in export of csv format files. By default, the data displayed in the list is exported to csv format files.

Using the default csv export, there are the following methods available. If you want to export the whole table with all the columns please refer to laravel-excel export function below.

```
$grid->export(function ($export) {

    // Filename for export, the default is `table name.csv`
    $export->filename('Filename.csv');

    // Exclude colums from the export
    $export->except(['column1', 'column2' ...]);

    // The method is used to specify which columns can only be exported.
    $export->only(['column3', 'column4' ...]);

    // In many cases, certain columns will be modified and displayed on the page.
    // For example, after using the `$grid->column('name')->label()` the value is modified
    // If you need the original content stored in the database, use the `originalValue` method.
    $export->originalValue(['column1', 'column2' ...]);

    // Finally, if you want to customize the export content of certain columns, use the `column` method
    $export->column('column_5', function ($value, $original) {
        // modify value here, or modify orginal (db) value
        return $value;
    )};
});
```

You can disable the export function by using

```
$grid->disableExport();
```

## Excel Export

You can export to excel using `Laravel-Excel 3.1`, first install [Laravel-Excel](https://docs.laravel-excel.com/3.1/getting-started/installation.html "https://docs.laravel-excel.com/3.1/getting-started/installation.html").

```
composer require maatwebsite/excel
```

Then create an export class:

```
<?php
Namespace App\Admin\Extensions;

Use LaveroAdmin\Admin\Grid\Exporters\ExcelExporter;

class PageExporter extends ExcelExporter
{
    protected $fileName = 'Article list.xlsx';

    protected $columns = [
        'id' => 'ID',
        'title' => 'title',
        'body' => 'body',
    ];
}
```

Then use this export class in the Grid:

```
Use App\Admin\Extensions\PageExporter;

$grid->exporter(new PageExporter());
```
