# File/Image upload

You need to complete the upload configuration before using the image upload function. Please read the configuration section of the document on this page.

## Upload image

Using local upload, the image upload directory is configured in `upload.image` in the file `config/admin.php`. If the directory does not exist, you need to create the directory and open the write permission.

Various methods such as compression, cropping, and watermarking can be used, and you need to install [intervention/image](https://image.intervention.io/v2 "https://image.intervention.io/v2") first.

For more usage methods, please refer to [[Intervention](https://image.intervention.io/v2 "https://image.intervention.io/v2")]:

```
$form->image('column', 'label');

// Modify the image upload path and file name
$form->image('column', 'label')->move($dir, $name);

// crop the picture
$form->image('column', 'label')->crop(int $width, int $height, [int $x, int $y]);

// add watermark
$form->image('column', 'label')->insert($watermark,'center');

// Add picture delete button
$form->image('column', 'label')->removable(); // default true

// keep pictures when deleting data
$form->image('column', 'label')->retainable(); // default false, if true no delete confirm message is shown

// Enable Media Selector (laveroadmin/media-manager must be installed)
$form->image('column', 'label')->pick(); // also set retainable to true to prevent file deleteion
$form->image('column', 'label')->pick("/images"); // you can also supply to path to wich you want to navigate
$form->image('column', 'label')->pick()->disableUpload(); // to disable the file upload

// Add a download button, click to download
$form->image('column', 'label')->downloadable(); // default true

// You can also set the setting by using the options function
$form->image('column', 'label')->options(["confirm_delete"=>false]);

// defaults are:
/*
"retainable"=>false,
"sortable"=> true,
"download" => true,
"delete" => true,
"confirm_delete" => true,
*/
```

Generate thumbnails after uploading pictures

```
// Generate thumbnails while uploading pictures
$form->image('column', 'label')->thumbnail('small', $width = 300, $height = 300);

// or multiple thumbnails
$form->image('column', 'label')->thumbnail([
    'small' => [100, 100],
    'small' => [200, 200],
    'small' => [300, 300],
]);
```

Custom thumbnail functions passing Instanstance of Intervention Image

```
// custom crop function
$form->image('column', 'label')->thumbnailFunction('small', function ($image) {
    $image->fit(300, 300, function ($constraint) {
        $constraint->upsize();
    });
    return $image;
});
```

Use thumbnails in models

```
class Photo extends Model
{
    use \LaveroAdmin\Admin\Traits\Resizable;
}

// To access thumbnail
$photo->thumbnail('small','photo_column');
```

## File Upload

Using local upload, the file upload directory is configured in `upload.file` in the file `config/admin.php`. If the directory does not exist, you need to create the directory and open the write permission.

```
$form->file('column', 'label');

// Modify the file upload path and file name
$form->file('column', 'label')->move($dir, $name);

// and set the upload file type
$form->file('column', 'label')->rules('mimes:doc,docx,xlsx');

// Add file delete button
$form->file('column', 'label')->removable();

// Keep files when deleting data
$form->file('column', 'label')->retainable();

// Add a download button, click to download
$form->file('column', 'label')->downloadable();

// Enable Media Selector (laveroadmin/media-manager must be installed)
$form->file('column', 'label')->pick(); // also sets retainable to true to prevent file deleteion
$form->file('column', 'label')->pick("/images"); // you can also supply to path to wich you want to navigate
$form->file('column', 'label')->pick()->disableUpload(); // to disable the file upload

// You can also set the setting by using the options function
$form->file('column', 'label')->options(["confirm_delete"=>false]);

// defaults are:
/*
"retainable"=>false,
"sortable"=> true,
"download" => true,
"delete" => true,
"confirm_delete" => true,
*/
```

## Multi-picture/file upload

```
// multiImage & multiFile share the same logic
$form->multipleImage('column', 'label');

// Add delete button
$form->multipleImage('column', 'label')->removable();

// multiple files
$form->multipleFile('column', 'label');

// Add delete button
$form->multipleFile('column', 'label')->removable();

// draggable sorting
$form->multipleImage('pictures')->sortable();

// Enable Media Selector (laveroadmin/media-manager must be installed)
$form->multipleImage('column', 'label')->pick(); // also sets retainable to true to prevent file deleteion
$form->multipleImage('column', 'label')->pick("/images"); // you can also supply to path to wich you want to navigate
$form->multipleImage('column', 'label')->pick()->disableUpload(); // to disable the file upload

// You can also set the setting by using the options function
$form->multipleImage('column', 'label')->options(["confirm_delete"=>false]);

// defaults are:
/*
"retainable"=>false,
"sortable"=> true,
"download" => true,
"delete" => true,
"confirm_delete" => true,
*/
```

The data submitted when uploading multiple pictures/files is an array of file paths, which is by default stored as a `JSON` array in the field of mysql. The field by default expects an array as field value, but it will try to json\_decode if a string value is given.

You can alter the storage mechanism of the Modelby:

```
public function setPicturesAttribute($pictures)
{
    if (is_array($pictures)) {
        $this->attributes['pictures'] = json_encode($pictures);
    }
}

public function getPicturesAttribute($pictures)
{
    return json_decode($pictures, true);
}
```

Of course, you can also specify any other format.

### One-to-many relationship processing

Assuming that each article in the articles table has multiple attachments, and the attachments are stored in the `path` field of the attachments table, then it can be handled as follows

```
articles
    id-integer
    title-string

attachments
    id-integer
    article_id-integer
    path-string
    order-integer
```

The model is:

```
class Article extends Model
{
    public function attachments()
    {
        return $this->hasMany(Attachment::class);
    }
}

class Attachment extends Model
{
}
```

Use in form:

```
$form->multipleFile('attachments','Attachments')->pathColumn('path')->sortColumn('order')->removable();
```

The first parameter of the `multipleFile` method uses the relationship name `attachments`, and the method `pathColumn` is used to specify that the file upload path is stored in the `path` field of the association table.

## Storage path/file name

```
// Modify the upload directory. Please note; this path is relative to the root of the used storage
$form->image('picture')->move('public/upload/image1/');

// Use a randomly generated file name (md5(uniqid()).extension)
$form->image('picture')->uniqueName();

// Custom file name
$form->image('picture')->name(function ($file) {
    return'test.'.$file->guessExtension();
});
```

## Upload configuration

Before using file upload, you must first complete the necessary configuration, configure local upload and cloud upload according to your situation

### Local Upload

First add storage configuration, `config/filesystems.php` add a `disk`:

```
'disks' => [
    ...,

    'admin' => [
        'driver' =>'local',
        'root' => public_path('uploads'),
        'visibility' =>'public',
        'url' => env('APP_URL').'/uploads',
    ],
],
```

Set the upload path to `public/uploads` (public\_path('uploads')).

Then select the uploaded `disk` and open `config/admin.php` to find:

```
'upload' => [

    'disk' =>'admin',

    'directory' => [
        'image' =>'images',
        'file' =>'files',
    ]
],
```

Set `disk` to the `admin` added above, `directory.image` and `directory.file` to use `$form->image($column)` and `$form->file($column)` Upload directory for uploaded pictures and files.

### Cloud upload (not tested with LaveroAdmin yet)

If you need to upload to cloud storage, you need to install the adapter corresponding to `laravel storage`, take Qi Niu cloud storage as an example

First install [zgldh/qiniu-laravel-storage] ([https://github.com/zgldh/qiniu-laravel-storage](https://github.com/zgldh/qiniu-laravel-storage "https://github.com/zgldh/qiniu-laravel-storage"))

Also configure the disk, add an item in `config/filesystems.php`:

```
'disks' => [
    ...,
    'qiniu' => [
        'driver' =>'qiniu',
        'domains' => [
            'default' =>'xxxxx.com1.z0.glb.clouddn.com', //your domain name
            'https' =>'dn-yourdomain.qbox.me', //your HTTPS domain name
            'custom' =>'static.abc.com', //your custom domain name
         ],
        'access_key'=>'', //AccessKey
        'secret_key'=>'', //SecretKey
        'bucket' =>'', //Bucket name
        'notify_url'=>'', //Persistent processing callback address
        'url' =>'http://of8kfibjo.bkt.clouddn.com/', // fill in the file to access the root url
    ],
],
```

Then modify the upload configuration of `LaveroAdmin`, open `config/admin.php` and find:

```
'upload' => [

    'disk' =>'qiniu',

    'directory' => [
        'image' =>'image',
        'file' =>'file',
    ],
],
```

`disk` select `qiniu` configured above.
