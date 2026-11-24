# Specification selector

This feature is used to build specifications like:

> **UI Preview:** A panel with attribute pill tags (e.g., Category: [All] [Books] [Electronics]; Status: [All] [Active] [Draft]) displayed above the table for multi-dimensional filtering.

## Basic usage

As shown in the following code, assuming that the four values of the `Status` field correspond to four statuses, the following way will build a specification selector for `status`.

```
$grid->selector(function (Grid\Tools\Selector $selector) {
    $selector->select('status', 'Status', [
        0 => 'offline',
        1 => 'online',
        2 => 'problem',
        3 => 'warning',
    ]);
});
```

The `select` method is multi-select by default. Click the 'plus' on the right side of each option on the page. The query for this field will add a query option. If the field filter only allows one to be selected, use the `selectOne` method.

```
$selector->selectOne('status', 'Status', [
    0 => 'offline',
    1 => 'online',
    2 => 'problem',
    3 => 'warning',
]);
```

## Custom Query

The above method will use the value selected on the selector to query as a query condition, but in some cases you need more flexible control query mode, you can customize the query in the following way:

```
$selector->selectOne('score', 'Score', ['0-999', '1000-1999', '2000-2999'], function ($query, $value) {
    $between = [
        [0, 999],
        [1000, 1999],
        [2000, 2999],
    ];
    $query->whereBetween('score', $between[$value]);
});
```

As shown above, passing an anonymous function as the fourth parameter, after the price field `score` is selected, the logic in the anonymous function will be used for data query, so that you can define any query method.

> The second parameter of the `select` and `selectOne` methods is the selector label, which can be omitted. If omitted, the field name will be used automatically.
