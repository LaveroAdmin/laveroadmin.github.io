# Content messages

## Notifications

Call the following method on the page, which will call the [toastify](https://github.com/apvarun/toastify-js "https://github.com/apvarun/toastify-js") component and add a floating notification in the top right corner of the page.

```
admin_toastr('Message...', 'success');

admin_toastr('Message...', 'info');

admin_toastr('Message...', 'warning');

admin_toastr('Message...', 'error');
```

You can also pass the third parameter and add the options to toastr. For more parameters, refer to [toastify](https://github.com/apvarun/toastify-js "https://github.com/apvarun/toastify-js")

```
admin_toastr('Message...', 'success', ['duration' => 5000]);
```

## Alerts

> **Display:** Renders standard Bootstrap alert banners (`success`, `info`, `warning`, `danger`) at the top of the content area with optional dismiss button.

```
<?php

namespace App\Admin\Controllers;

use LaveroAdmin\Admin\Layout\Content;
use App\Http\Controllers\Controller;

class PostController extends Controller
{
    public function index(Content $content)
    {
        return $content
            ->withError('Title', 'messages..');
    }
}
```

Add several other types of messages

```
$content->withWarning('Title', 'messages..');

$content->withInfo('Title', 'messages..');

$content->withSuccess('Title', 'messages..');
```

You can also use the following functions, the effect is the same

```
admin_success($title, $message);

admin_warning($title, $message);

admin_error($title, $message);

admin_info($title, $message);
```
