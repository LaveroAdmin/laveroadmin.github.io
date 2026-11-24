# Fields management

## Remove field

The built-in `map` and `editor` fields requires the front-end files via cdn, and if there are problems with the network, they can be removed in the following ways

Locate the file `app/Admin/bootstrap.php`. If the file does not exist, update `LaveroAdmin` and create this file.

```
<?php

use LaveroAdmin\Admin\Form;

Form::forget('map');
Form::forget('editor');

// or

Form::forget(['map', 'editor']);
```

This removes the two fields, which can be used to remove the other fields.

## Extend the custom field

As an example for a custom plugin we'll create a PHP Code editor base on [codemirror](http://codemirror.net/index.html "http://codemirror.net/index.html") with the following steps.

Download and unzip the [codemirror](http://codemirror.net/ "http://codemirror.net/") library to the front-end resource directory, for example, in the directory `public/packages/codemirror/`.

Create a new field class `app/Admin/Extensions/Form/PHPEditor.php`:

```
<?php

namespace App\Admin\Extensions\Form;

use LaveroAdmin\Admin\Form\Field;

class PHPEditor extends Field
{
    protected $view = 'admin.fields.php-editor';

    protected static $css = [
        '/packages/codemirror/lib/codemirror.css',
    ];

    protected static $js = [
        '/packages/codemirror/lib/codemirror.js',
        '/packages/codemirror/addon/edit/matchbrackets.js',
        '/packages/codemirror/mode/htmlmixed/htmlmixed.js',
        '/packages/codemirror/mode/xml/xml.js',
        '/packages/codemirror/mode/javascript/javascript.js',
        '/packages/codemirror/mode/css/css.js',
        '/packages/codemirror/mode/clike/clike.js',
        '/packages/codemirror/mode/php/php.js',
    ];

    public function render()
    {
        $this->script = <<<EOT

CodeMirror.fromTextArea(document.getElementById("{$this->id}"), {
    lineNumbers: true,
    mode: "text/x-php",
    extraKeys: {
        "Tab": function(cm){
            cm.replaceSelection("    " , "end");
        }
     }
});

EOT;
        return parent::render();

    }
}
```

> Static resources in the class can also be imported from outside, for example. These resources (css & js) will be automaticly collected and added to the code.
>
> ```
> protected static $js = [
> '//cdn.somer-random-server.com/1.5.10/standard/script.js',
> ];
> ```

Create a view file `resources/views/admin/fields/php-editor.blade.php`:

```
@include("admin::form._header")
    <textarea class="form-control {{$class}}" id="{{$id}}" name="{{$name}}" placeholder="{{ $placeholder }}" {!! $attributes !!} >{{ old($column, $value) }}</textarea>
@include("admin::form._footer")
```

Finally, find the file `app/Admin/bootstrap.php`, if the file does not exist, update `LaveroAdmin`, and then create this file, add the following code:

```
<?php

use App\Admin\Extensions\Form\PHPEditor;
use LaveroAdmin\Admin\Form;

Form::extend('php', PHPEditor::class);
```

And then you can use PHP editor in [model-form](README.md):

```
$form->php('code');
```

In this way, you can add any form fields you want to add.

## Chainability

To make your new form field have a chainability you

```
<?php

namespace App\Admin\Extensions\Form;

use LaveroAdmin\Admin\Form\Field;

class CustomField extends Field
{
    protected $options [];

    public function options($arr = []){
        $this->options = array_merge($this->options,$arr);

        // NOTE; this is the part that allows for chainability
        return $this;
    }

    public function render()
    {
        $settings = json_encode($this->options);
        $this->script = "var cf = initYourCustomField({$settings});";

        return parent::render();
    }
}
```

In bootstrap.php

```
<?php

use App\Admin\Extensions\Form\CustomField;
use LaveroAdmin\Admin\Form;

//please note that fields are writen in lowercase
Form::extend('customfield', CustomField::class);
```

For example; this will allow you to use the following:

```
$form->customfield('column','label')->options(["direction"=>'left','total'=>10]);
```
