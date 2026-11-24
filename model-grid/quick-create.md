# Quick create

After enable this feature in the grid, a form will be added to the table header to create the data. For some simple form pages, it is convenient to quickly create data without jumping to the create page.

> **UI Preview:** An inline single-row input form embedded directly into the table header for rapid data entry.

```
$grid->quickCreate(function (Grid\Tools\QuickCreate $create) {
    $create->text('name', 'Name');
    $create->email('email', 'Email');
});
```

The form supports als fields available to the form class, please refer to: [Builtin form fields](../model-form/fields.md "../model-form/fields.md")
