# Form Initialization Settings

With the initial setting function of the form you can set the properties of the form globally.

For example, if you need to disable some operations in all forms, you can add the following code in `app/Admin/bootstrap.php`:

```
Form::init(function (Form $form) {

    $form->disableEditingCheck();

    $form->disableCreatingCheck();

    $form->disableViewCheck();

    $form->tools(function (Form\Tools $tools) {
        $tools->disableDelete();
        $tools->disableView();
        $tools->disableList();
    });
});
```

If you want to open the settings in one of the forms, for example, open the checkbox that displays 'Continue Editing', call `$form->disableEditingCheck(false)` on the corresponding instance;
