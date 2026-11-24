# Model-show Fields

## Insert a divider

If you want to add a divider between the fields:

```
$show->divider();
```

## Modify the display

Modify the display content as follows

```
$show->field("column","label")->as(function ($title) {
    return "<{$title}>";
});

$show->field("column","label")->as(function ($content) {
    return "{$content}";
});
```

Here are a few common display style methods implemented by the `as` method:

## image

The content of the field `avatar` is the path or url of the image, which can be displayed as an image:

```
$show->field("column","label")->image($base_url = '', $width = 200, $height = 200));
```

## file

The content of the field `document` is the path or url of the file, which can be displayed as an file:

```
$show->field("column","label")->file($server = '', $download = true);
```

The parameters of the `file()` method are referenced to [Field::file()](https://github.com/LaveroAdmin/LaveroAdmin/blob/master/src/Show/Field.php#L216)

## link

The content of the field `homepage` is a url link that can be displayed as an HTML link:

```
$show->field("column","label")->link($href = '', $target = '_blank');
```

## label

Show the contents of the field `tag` as label:

```
$show->field("column","label")->label($style = 'success');
```

## badge

Show the contents of the field `rate` as badge:

```
$show->field("column","label")->badge($style = 'primary');
```

## using

If the value of the field `gender` is `f`, `m`, it needs to be displayed with `Female` and `Male` respectively.

```
$show->field("column","label")->using(['f' => 'Female', 'm' => 'Male']);
```

## json

Show json in formated style

```
$show->field("column","label")->json();
```
