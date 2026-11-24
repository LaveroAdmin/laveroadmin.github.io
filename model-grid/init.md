# Grid initialization settings

The initial setting function of the table can be used to set the defautl table globally.

For example, if you need to disable some operations in all tables, you can add the following code in `app/Admin/bootstrap.php`:

```
use LaveroAdmin\Admin\Grid;

Grid::init(function (Grid $grid) {

    $grid->enableDblClick();

    $grid->disableActions();

    $grid->disablePagination();

    $grid->disableCreateButton();

    $grid->disableFilter();

    $grid->disableRowSelector();

    $grid->disableColumnSelector();

    $grid->disableTools();

    $grid->disableExport();

    $grid->actions(function (Grid\Displayers\Actions\Actions $actions) {
        $actions->disableView();
        $actions->disableEdit();
        $actions->disableDelete();
    });
});
```

This way you don't have to set it in the code of each controller.

If you want to enable settings in one of the tables, such as opening the display action column, call `$grid->disableActions(false);` on the corresponding instance.
