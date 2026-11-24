# Model Grid Edit Relationships

### Selectable

To manage relationships directly in the grid view you first need to make a `Selectable`.

```
<?php

namespace App\Admin\Selectable;

use App\Models\User;
use LaveroAdmin\Admin\Grid\Filter;
use LaveroAdmin\Admin\Grid\Selectable;

class UsersSelect extends Selectable
{
    public $model = User::class;
    public static $display_field = "tag"; // display field when using in grid
    public static $labelClass = "badge bg-success";
    public static $seperator = " ";

    public function make()
    {
        $this->column('id');
        $this->column('name');
        $this->column('email');
        $this->column('avatar','Avatar')->image();
        $this->column('created_at');

        $this->filter(function (Filter $filter) {
            $filter->like('name');
        });
    }

    public static function display()
    {
        return function ($value) {

            // For belongs to many relationships
            if (is_array($value)) {
                return implode(self::$seperator, array_map(function ($tag) {
                    return "<span data-key=\"{$tag['id']}\" class='".(self::$labelClass)."'>{$tag['tag']}</span>";
                }, $value));
            }

            // For belongsTo relationship
            return "<span data-key=\"".$value."\" class='".(self::$labelClass)."'>".optional($this->user)->name."</span>";

            //Note: the data-key needs double quotes for javascript selector

        };
    }
}
```

#### make()

The `make` method is used to build the list, please refer to [model-grid documentation](README.md)

#### display()

The `display` method allows you to alter how the list value are displayed

#### $model settings

In the list selection class, the `$model` attribute is used to specify the model of the list. The default data of the list is `10`. You can use the attribute `protected $perPage = 5;` to set the number of each page.

### BelongsTo

Here is how to use use this Selectable in combination with the belongsTo method:

```
use App\Admin\Selectable\UsersSelect;

$grid->column('user_id','Project Owner')
    ->belongsTo(UsersSelect::class);
```

> **Preview:** *Grid belongsto*

### BelongsToMany

Use `belongsToMany` method instead of `multipleSelect` to select many-to-many relationship

Take the following project table and user table as examples, each project has a belongs to multiple relation with users (collaborators)

```
projects
    id-integer
    title-string
    body-text
    user_id-integer

users
    id-integer
    name-string
    email-string
    avatar-string

project_user
    user_id-integer
    project_id-integer
```

model:

```
class Project extends Model
{
    public function collaborators()
    {
        return $this->belongsToMany(Users::class);
    }
}

class User extends Model
{
}
```

Grid part:

```
use App\Admin\Selectable\UsersSelect;

$grid->column('collaborators','Collaborators')
    ->belongsToMany(UsersSelect::class);
```

> **Preview:** *Grid belongstomany*

## Unsupported relationship

The following types of relationship do not supported

`HasOneThrough`, `HasManyThrough`, `MorphTo`, `MorphToMany`
