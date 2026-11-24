# Column filter

This feature sets a filter for the corresponding column in the header, which makes it easier to perform data table filtering based on this column.

> **Preview:** *Filter colum checkbox*

There are three types of filters:

## String comparison query

```
$grid->column('code')->filter();
```

The above call can add a `input` type filter to the header of the `code` column. Click the filter icon to expand the filter. After the query is submitted, the `equal` query will be executed for this column.

If you need a like query:

```
$grid->column('title')->filter('like');
```

> **Preview:** *Filter column text*

If the field is a time, date related field, you can use the following method

```
// uses flatPickr
$grid->column('date')->filter('date');
$grid->column('datetime')->filter('datetime');

// time uses inputMask
$grid->column('time')->filter('time');
```

> **Preview:** *Filter column date*

## Multiple select query

Suppose you need to filter one or more status data in the table data through the `status` field. It is very convenient to use `Multiple select query`.

```
$grid->column('status')->filter([
    0 => 'Unknown',
    1 => 'Ordered',
    2 => 'Paid',
    3 => 'Cancelled',
]);
```

> **Preview:** *Filter colum checkbox*

## Range query

Suppose you need to filter out data in a price range by using the `price` field.

```
$grid->column('price')->filter('range');
```

> **Preview:** *Fitler range*

Or filtering of time and date range

```
$grid->column('date')->filter('range', 'date');

$grid->column('time')->filter('range', 'time');

$grid->column('datetime')->filter('range', 'datetime');
```

> **Preview:** *Filter range date*
