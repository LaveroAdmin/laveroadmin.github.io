# AdminController hooks

### The Hooks

With the AdminController hooks you can easly alter the default behaviour of the AdminController and write reusable code.

There are 3 available hooks for the admin controller:

* alterGrid
* alterDetail
* alterForm

You can use them like this:

```
<?php

namespace App\Admin\Controllers;

use LaveroAdmin\Admin\Controllers\AdminController;
use LaveroAdmin\Admin\Form;
use LaveroAdmin\Admin\Grid;
use LaveroAdmin\Admin\Show;
use App\Models\Page;

class PageController extends AdminController
{
    /**
     * Title for current resource.
     *
     * @var string
     */
    protected $title = 'Page';

    public function __construct()
    {
        $this->hook("alterGrid", function ($scope, $grid) {
            // alter the grid here
            return $grid;
        });

        $this->hook("alterDetail", function ($scope, $detail) {
            // alter the grid here
            return $detail;
        });

        $this->hook("alterForm", function ($scope, $form) {
            // alter the grid here
            return $form;
        });
    }

    ...
```

### Trait example

The thrait below with add the duration of the form editing and the saved model to a log file. Now it becomes really easy to use functionality on other AdminControllers as we. But this functionality can me used for a wide varity of cases.

```
<?php

namespace App\Admin\Controllers;

use LaveroAdmin\Admin\Controllers\AdminController;
use LaveroAdmin\Admin\Form;
use LaveroAdmin\Admin\Grid;
use LaveroAdmin\Admin\Show;
use App\Models\Page;
use App\Admin\Traits\HookExample;

class PageController extends AdminController
{
    use HookExample;

    public function __construct()
    {
        $this->initHooks();
    }

    ...
```

App/Admin/Traits/HookExample.php

```
<?php

namespace App\Admin\Traits;

use Illuminate\Support\Facades\Log;
use LaveroAdmin\Admin\Facades\Admin;
use LaveroAdmin\Admin\Form;

trait HookExample
{
    public function initHooks()
    {
        $this->hook('alterForm', function ($scope, $form) {

            // maybe you want to alter the form looks
            //Admin::css('/vendor/some-css-file', false); // false mean the file is not minified, and is added when minifier is on

            // or add some custom js
            //Admin::js('/vendor/some-js-file', false);   // false mean the file is not minified, and is added when minifier is on

            $form = $this->addFormLogic($form);

            return $form;
        });
    }

    public function addFormLogic($form)
    {
        $form->hidden('time', 'time')->value(time());
        $form->ignore('time');

        $form->saved(function (Form $form) {
            $model = $form->model();
            $diff = time() - request()->time;
            Log::debug("Spend {$diff} seconds editing the form");
            Log::debug("The saved form data is:".json_encode($model));
        });

        return $form;
    }
}
```
