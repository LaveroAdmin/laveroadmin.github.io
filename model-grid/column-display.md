# Column display

By default, the column shows the most primitive data in the database. If you need to modify the display of the column data, refer to the following method.

## Display callback

The `display()` method is used to process the value of the current column via the passed callback function:

```
$grid->column('title')->display(function ($title) {

                return "<span style='color:blue'>$title</span>";

});
```

In the incoming anonymous function, the data can be processed in any way. In addition, the anonymous function binds the data of the current column as a parent object, and can call the data of the current row in the function:

```
$grid->column('first_name');

$grid->column('last_name');

// `full_name` field that does not exist
$grid->column('full_name')->display(function () {
                return $this->first_name . ' ' . $this->last_name;
});
```

Calling for displaying related models

```
$grid->tags()->display(function ($tags) {
    $str = '';
    foreach ($tags as $tag) {
        $str .= '<span class="badge rounded-pill bg-success">'.$tag['tag'].'</span> ';
    }
    return $str;
});
```

> **Preview:** *Tags*

> Note: Try to avoid querying the database or calling the external API in the callback function. This will run the query or the call of the external interface when each row is rendered, which seriously affects the performance. A good way is to establish an association relationship with the model. Use the model's `with` method to query the associated data together.

## Collection callback

This method is different from the `display` callback in that it can modify data in batches. For example you can alter a set data before it is passed to grid. For example retrieving data from external source outside the model. Refer to the following use cases in the following examples:

```
use Illuminate\Database\Eloquent\Collection;

$grid->model()->collection(function(Collection $collection) {
    // 1. You can add fields to each column, similar to the effect of the display callback above
    foreach ($collection as $item) {
        $item->full_name = $item->first_name. ''. $item->last_name;
    }

    // 2. Add a sequence column to the table
    foreach ($collection as $index => $item) {
        $item->number = $index;
    }
    // 3. Get data from external interface and fill it into the model collection
    $ids = $collection->pluck('id');
    $data = getDataFromApi($ids);
    foreach ($collection as $index => $item) {
        $item->column_name = $data[$index];
    }
    // Finally must return the collection object
    return $collection;
});
```

`$collection` represents the current model collection of tabular data. You can read or modify its data according to your needs.

## Display different components according to conditions

If this column is to be displayed as a different component based on certain criteria

```
$grid->column('title')->display(function ($title, $column) {
                
                / / If the value of the status field of this column is equal to 1, directly display the title field
                If ($this->status == 1) {
                                return $title;
                }
                
                // Otherwise it is displayed as editable
                return $column->editable();
});
```

## Content Mapping

If the value of the field `gender` is `f`, `m`, it needs to be displayed with `female` and `male` respectively.

```
$grid->column('gender')->using(['f' => 'female', 'm' => 'male']);
```

## Content replacement

If you need to replace the whole value ​​of this column with an other value to display:

```
$grid->column('cost')->replace([0 => '-']);
```

## Column view

Use the `view()` method to make the current column render a view display output, such as having a view `resources/views/content.blade.php`

```
<p>

{{ $value }}

{{ $model->id }}

</p>
```

By default, two variables of the view are passed in, `$model` is the model of the current row, and `$value` is the value of the current column.

Then use the following call to render this view output

```
$grid->column('content')->view('content');
```

This method can be used to render complex column content.

## Column expansion

If there are more fields in a row, you can hide too much content by using the column expansion feature. Expand the display by clicking on the column, or click to expand other related data, such as the following example, to expand the 10 latest comments under an article:

```
use LaveroAdmin\Admin\Widgets\Table;

$grid->column('title', 'title')->expand(function ($model) {

                $comments = $model->comments()->take(10)->get()->map(function ($comment) {
                                return $comment->only(['id', 'content', 'created_at']);
                });

                return new Table(['ID', 'content', 'release time'], $comments->toArray());
});
```

The effect is as follows:

> **Preview:** *Screen recording 2021 11 25 at 22 24 32*

### Expand using Renderable

You can also pass a class with the Renderable interface to let the exanpand content render dynamicly.

First create a class:

```
<?php

namespace App\Admin\Extensions;

use Illuminate\Contracts\Support\Renderable;

class Preview implements Renderable
{
    public function render()
    {
        $key = request()->key;
        $date = date_format(now(), 'Y-m-d H:i:s');
        return "This is the content is rendered at ".$date." with id: ".$key;
    }
}
```

Then you can pass the class like this:

```
use App\Admin\Extensions\Preview;

$grid->column('title', 'title')->expand(Preview::class);
```

## Popup modal box

Similar to the `column expansion` function, you can display more content by popping up the modal box.

```
$grid->column('title', 'title')->modal('latest comment', function ($model) {

                $comments = $model->comments()->take(10)->get()->map(function ($comment) {
                                return $comment->only(['id', 'content', 'created_at']);
                });

                return new Table(['ID', 'content', 'release time'], $comments->toArray());
});
```

The effect is as follows:

> **Preview:** *Screen recording 2021 11 25 at 22 25 42*

### Popup modal using Renderable

You can also pass a class with the Renderable interface to let the modal content render dynamicly.

First create a class:

```
<?php

namespace App\Admin\Extensions;

use Illuminate\Contracts\Support\Renderable;

class Preview implements Renderable
{
    public function render()
    {
        $key = request()->key;
        $date = date_format(now(), 'Y-m-d H:i:s');
        return "This is the content is rendered at ".$date." with id: ".$key;
    }
}
```

Then you can pass the class like this:

```
use App\Admin\Extensions\Preview;

$grid->column('title', 'title')->modal('Preview', Preview::class);
```

## Gavatar

If this column of data is a email, you want to display it as a Gavatar:

```
$grid->column('email', 'avatar')->gravatar();

// set the size
$grid->column('email', 'avatar')->gravatar(45);
```

## File size

If the data in this column is the number of bytes representing the file size, you can display the more readable text by calling the `filezise` method.

```
$grid->column('file_size')->filesize();
```

Such a value `199812019` will be displayed as `190.56 MB`

## Download link

If the data in this column stores the path to the uploaded file, you can set this column as a downloadable link by calling the `downloadable` method.

```
$grid->column('file_path')->downloadable();
```

## Copy button

With the following call, a copy icon will appear in front of each line of text in this column, click on it to copy its value

```
$grid->column('title')->copyable();
```

## QR code

Through the following call, a QR code icon will appear in front of each line of text in this column. Click on it to expand a small bullet box, which will display the QR code of this column value. (uses: [https://api.qrserver.com/](https://api.qrserver.com/ "https://api.qrserver.com/"))

```
$grid->column('link')->qrcode();
```

## Display image

If the `picture` field holds the full address of the picture, or the path, the column can be rendered as a picture display in the following way.

Multi-graph display is supported, and field output is required as an array.

```
$grid->column('picture')->image();

// Set the server and width
$grid->column('picture')->image('http://content-delivery-url.com', 100, 100);

// show multiple images
$grid->column('pictures')->display(function ($pictures) {

                return json_decode($pictures, true);

})->image('http://content-delivery-url.com', 100, 100);
```

## Display label

Display the field as a `label` tag. If the field is output as an array, it will appear as multiple `label` tags.

```
$grid->column('name')->label();

// Set the color, the default `success`, optional `danger`, `warning`, `info`, `primary`, `default`, `success`
$grid->column('name')->label('danger');

// receive array
$grid->column('keywords')->label();
```

If you need to display the different values ​​of the `status` field as labels of different colors

```
$grid->column('step', __('Step'))->label([
    "new"=>"info",
    "expired"=>"danger",
    "question"=>"warning",
    "done"=>"success"
]);
```

## Show icon

Display the field as a `font-awesome` icon, more icons refer to [http://fontawesome.io/icons/](http://fontawesome.io/icons/ "http://fontawesome.io/icons/")

```
$grid->column('status')->icon([
                0 => 'toggle-off',
                1 => 'toggle-on',
], $default = '');
```

## Link

Display the field as a link.

```
// When the link method does not pass parameters, the linked `href` and `text` are the values ​​of the current column.
$grid->column('homepage')->link();

// or pass in a specified href
$grid->column('homepage')->link($href);
```

## Table

Display the field as a table, requiring the value of the current column to be a two-dimensional array

```
// table method does not pass parameters, the title of the table is the key of each column of the two-dimensional array
$grid->column('settings')->table();

// example
$grid->tags()->display(function ($tags) {
    return [["k"=>"1","val"=>'value'],["k"=>"2","val"=>'value 2']];
})->table();

// You can specify the key for each column by the following method
$grid->column('settings')->table(['k' => 'key', 'val' => 'value']);
```

## Progress bar

Display the field as a progress bar, the value of the current column needs to be a value, the default maximum value is 100,

```
$grid->column('progress')->progressBar();

// optional parameters
$grid->column('progress')->progressBar($style = 'primary', $size = 'sm', $max = 100);
```

`$style` is used to set the style, optional values ​​`danger`, `warning`, `info`, `primary`, `default`, `success`

`$size` is used to set the size. The optional values ​​are `sm`, `xs`, `xxs`, `$max` to set the maximum range.

## Loading status

```
$grid->column('status')->loading([1, 2, 3]);
```

If the value of status is one of `[1, 2, 3]`, it will be displayed as a loading icon.

Show other field values ​​displayed

```
$grid->column('status')->loading([1, 2, 3], [
                4 => 'Complete'
]);
```

## Image Carousel

If the field value is an image array, you can use the following call to display the image carousel component.

```
$grid->column('images')->carousel();

// Set the display size and image server
$grid->column('images')->carousel($width = 300, int $height = 200, $server);
```

## Date format

If the field value is a timestamp, you can format the output with the `date` method.

```
$grid->column('created_at')->dateFormat('Y-m-d');
```

For the format parameters, please refer to PHP's [date](http://php.net/manual/en/function.date.php "http://php.net/manual/en/function.date.php") function.

## Boolean display

> **Preview:** *Wx20190830 002713*

After converting this column to bool value, it will be displayed as `✓` and `✗`.

```
$grid->column('approved')->bool();
```

You can also specify the display according to the value of this column, such as the value of the field `Y` and `N` means `true` and `false`

```
$grid->column('approved')->bool(['Y' => true, 'N' => false]);
```

## Dot prefix

This method is used to add a colored dot to the front of the column text to provide a more intuitive and clear visual effect.

```
$grid->column('status')->using([
                1 => 'Audit passed',
                2 => 'Draft',
                3 => 'Publish',
                4 => 'Other',
], 'Unknown')->dot([
                1 => 'danger',
                2 => 'info',
                3 => 'primary',
                4 => 'success',
], 'warning');
```

According to the above code, use the `using` method to map the display text, and then use `dot` to set the color of the dot. The values ​​of the color are `danger`, `info`, `primary`, `success`, `warning`. These five choices.

## Secret

> **Preview:** *Grid secret*

Hides the value and replaces it with astrixes. In the source code the actual is available so not really secret. Don't use for values that should be really secret.

```
$grid->column('status')->secret();
```

## Column action

> **Preview:** *Wx20190830 001824*

> Note: Please read [[Custom Action - row action]](model-grid-custom-actions#row-action "model-grid-custom-actions#row-action") before using this method.

This function can display a column as an actionable button. For example, the above figure shows a column operation of `Stars` and 'Cancel Stars'. After clicking the star icon in this column, the background will switch the state of the field:star of the Model Page. Then the page icon is also followed by the switch, the specific implementation code is as follows:

```
<?php

namespace App\Admin\Actions\Page;

use App\Models\Page;
use LaveroAdmin\Admin\Actions\RowAction;

class StarPage extends RowAction
{
    public function handle(Page $page)
    {
        $page->star = (int) !$page->star;
        $page->save();

        $html = $page->star ? "<i class=\"icon-star\"></i>" : "<i class=\"icon-star opacity-25\"></i>" ;

        return $this->response()->html($html);
    }

    public function display($star)
    {
        return $star ? "<i class=\"icon-star\"></i>" : "<i class=\"icon-star opacity-25\"></i>";
    }
}
```

The above file is saved as: `App\Admin\Actions\Page\StarPage.php`

Finally, use this action in grid:

```
use App\Admin\Actions\Page\StarPage;

$grid->column('star')->action(StarPage::class);
```
