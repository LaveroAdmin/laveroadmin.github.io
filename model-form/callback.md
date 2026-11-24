# Model form callback

`model-form` currently has three methods for receiving callback functions:

```
// callback after form submission
$form->submitted(function (Form $form) {
    //...
});

// callback before save
$form->saving(function (Form $form) {
    //...
});

// callback after save
$form->saved(function (Form $form) {
    //...
});
```

If required, you can add additional fields to ignore using the submitted function e.g.

```
$form->submitted(function (Form $form) {
    $form->ignore('username');
 });
```

The form data that is currently submitted can be retrieved from the callback parameter `$form`:

```
$form->saving(function (Form $form) {

    dump($form->username);

});
```

Assign value to a field from anther：

```
$form->text('name');
$form->hidden('slug');

$form->saving(function (Form $form) {

    $form->slug = $form->name;

});
```

Get data in model

```
$form->saved(function (Form $form) {

    $form->model()->id;

});
```

Can redirect other urls by returning an instance of `Symfony\Component\HttpFoundation\Response` directly in the callback:

```
$form->saving(function (Form $form) {
    // returns a simple response
    // not this is executed before saving, form won't be save;
    return response('xxxx');
});

$form->saving(function (Form $form) {
    // redirect url
    return redirect('/admin/users');
});

$form->saving(function (Form $form) {
    // throws an exception
    throw new \Exception('Error friends. . .');
});

$form->saved(function (Form $form) {
    // returns a simple response
    // not this is executed affore saving, form will be save;
    return response('xxxx');
});
```

Return error or success information on the page:

```
use Illuminate\Support\MessageBag;

// redirect back with an error message
$form->saving(function ($form) {
    if ($form->title == "asdf") {
        $error = new MessageBag([
            'title'   => 'Title error',
            'message' => 'this can not be <b>asdf</b>',
        ]);
        return back()->with(compact('error'));
    }
});

// redirect back with a successful message
$form->saved(function ($form) {
    $success = new MessageBag([
        'title'   => 'Saved',
        'message' => 'Sweet this is saved',
    ]);
    return back()->with(compact('success'));
});
```
