# Relationship

## Introduction

The model form supports data processing related to multiple models such as one-to-one, one-to-many, and many-to-many. Please use lowercase relation functions in your models for example:

```
// Structure
pages
    id-integer
    name-string
    body-text

pages_info
    id-integer
    page_id-integer
    user_id-integer
    info-text

// Model
class Page extends Model
{
    public function pageinfo()
    {
        return $this->hasOne(PageInfo::class);
    }
}

// PageController.php
// Make use the relation function is lower case. CamelCase will converted to snake_case
$form->textarea('pageinfo.info', __('Page info'));
```

## One to one

The `users` table and the `profiles` table are associated one-to-one with the `profiles.user_id` field

```
users
    id-integer
    name-string
    email-string

profiles
    id-integer
    user_id-integer
    age-integer
    gender-string
```

The corresponding data model is:

```
class User extends Model
{
    public function profile()
    {
        return $this->hasOne(Profile::class);
    }
}

class Profile extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

### HasOne

The following code can directly edit the age and gender fields in the profile of the associated model in the form of the User model

```
$form = new Form(new User);

$form->text('name');
$form->text('email');

// Fields associated with model profile
$form->text('profile.age');
$form->text('profile.gender');
```

### BelongsTo

Similarly, the reverse relationship can also directly edit the data in the attribution model `User` in the form of the `Profile` model

```
$form = new Form(new Profile);

$form->text('age');
$form->text('gender');

// Fields associated with model profile
$form->text('user.name');
$form->text('user.email');
```

### MorphOne

The usage method of `MorphOne` association is the same as the above `HasOne` and `BelongsTo` association.

## One to many

### HasMany

Two of `painters` and `paintings` establish a one-to-many relationship through `painter_id`:

```
painters
  id-integer
  username-string
  bio-text

paintings
  id-integer
  painter_id-integer
  title-string
  body-text
  completed_at-timestamp
```

The model of the table is:

```
class Painter extends Model
{
    public function paintings()
    {
        return $this->hasMany(Painting::class,'painter_id');
    }
}

class Painting extends Model
{
    public function painter()
    {
        return $this->belongsTo(Painter::class,'painter_id');
    }
}
```

Use the following form to build the code to edit the main table and field fields:

```
// Subtable fields
$form->hasMany('paintings', function (Form\NestedForm $form) {
    $form->text('title');
    $form->image('body');
    $form->datetime('completed_at');
});

// You can use the second parameter to set the label
$form->hasMany('paintings','Painting', function (Form\NestedForm $form) {

});

// You display this relation in three different modes (default, tab & table)
$form->hasMany('paintings', function ($form) {
    ...
})->mode("table");
```

> Note: At present, HasMany forms are not friendly enough to support complex form components (rich text, etc.).

### MorphMany

The use of polymorphic relation is to re-use one table structure that hold relations to multible tables in our case:

`posts` and `pages` in relation to `comments`. So the comments table contains both comments for posts and pages. The data is for this is being stored in commentable\_id & commentable\_type fields automaticly.

```
posts
  id-integer
  content-text

pages
  id-integer
  content-text

comments
  id-integer
  body-integer
  commentable_id-integer
  commentable_type-string
```

The models are as following:

```
class Post extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment::class,'commentable');
    }
}

class Page extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment::class,'commentable');
    }
}

class Comment extends Model
{
    public function commentable()
    {
        return $this->morphTo();
    }
}
```

Use the following form to build the code to edit the field in either the PageController of PostController:

```
$form->morphMany('comments', function (Form\NestedForm $form) {
    $form->textarea('body');
});

// You can use the second parameter to set the label
$form->morphMany('comments','The Comments', function (Form\NestedForm $form) {

});
```

> In fact, the morphMany method is an alias of the hasMany method, and the distinction is made due to the different associated names. The two methods have the same options and functions

### HasMany With Resourceable

HasMany with Resourceable also know as Linked resources can be used to... ADD FURTHER INSTRUCTIONS

## Many to many

### BelongsToMany

The `users` table and the `roles` table are related through the middle table `role_user` many-to-many, a user can have multiple roles, and a role can also belong to multiple users

```
users
    id-integer
    name-string

roles
    id-integer
    name-integer

role_user
    user_id-integer
    role_id-integer
```

The corresponding data model is:

```
class User extends Model
{
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
}

class Role extends Model
{
    public function user()
    {
        return $this->belongsToMany(User::class);
    }
}
```

Then there are two components `multipleSelect` and `checkbox` which support the selection of `many to many association relationship`

For example, select the role in the user's form:

```
$form = new Form(new User);

$form->multipleSelect('roles','Role')->options(Role::all()->pluck('name','id'));
```

Or select the user in the role form:

```
$form = new Form(new Role);

$form->multipleSelect('users','User')->options(User::all()->pluck('name','id'));
```

The same usage of `checkbox`:

```
$form = new Form(new User);

$form->checkbox('roles','role')->options(Role::all()->pluck('name','id'));
```

## BelongsTo selection

The attribution relationship selection lets easely select related elements.

Take the following article table and user table as examples, each article belongs to a user (author)

```
page
    id-integer
    user_id-integer
    title-string
    body-text

users
    id-integer
    name-string
    email-string
    avatar-string
```

model:

```
class Article extends Model
{
    public function author()
    {
        return $this->belongsTo(User::class);
    }
}

class User extends Model
{
}
```

If you want to select the user in the form of the page, you can use the `select` component

```
$form->select('author_id')->options(User::all()->pluck('name','id'));
```

There are two obvious problems with the `select` component. First, if there are too many users, the selected option will be too long. Second, there is no way to display more user information in the selected option, such as `email`, The two fields "avatar".

The following two methods `BelongsTo` and `BelongsToMany` solve this problem very well, using the form of `popup list` to select the attribution object.

### BelongsTo

Also use the table structure and model in the above example, using the following method

First define the list selection class:

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
}
```

In the list selection class, the `$model` attribute is used to specify the model of the list. The default data of the list is `10`. You can use the attribute `protected $perPage = 5;` to set the number of each page.

The `make` method is used to build the list, please refer to [model-grid documentation](../model-grid/README.md)

Here is how to use it in the form:

```
use App\Admin\Selectable\UsersSelect;

$form->belongsTo('user_id', UsersSelect::class,'Author');
```

The effect is as follows:

> **Preview:** *Belongsto*

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

Here is how to use it in the form:

```
// Select the class using the list defined in the example above
use App\Admin\Selectable\UsersSelect;

$form->belongsToMany('collaborators', UsersSelect::class, __('Collaborators'));
```

The effect is as follows:

> **Preview:** *Belongstomany*

### Use in the list page

As long as the attribution selector is used in the form, it can also be used in the list page to achieve in-line editing

```
// Select the class using the list defined in the example above
use App\Admin\Selectable\UsersSelect;

$grid->column('user_id','Project Owner')
    ->belongsTo(UsersSelect::class);

$grid->column('collaborators','Collaborators')
    ->belongsToMany(UsersSelect::class);
```

Note that when used in a list, **its recommanded** to add a `display` method to the list selection class `App\Admin\Selectable\Users` to define the display of these two columns in the list:

```
class UsersSelect extends Selectable
{
    ...

    public static function display()
    {
        return function ($value) {

            // For belongs to many relationships
            if (is_array($value)) {
                return implode(', ', array_map(function ($collaborator) {
                    return "<span class='_{$collaborator['id']}'>{$collaborator['name']}</span>";
                }, $value));
            }

            // For belongsTo relationship
            return "<span class='_".$value."'>".optional($this->user)->name."</span>";

        };
    }
}
```

BelongsToMany / multiple example

> **Preview:** *Grid belongstomany*

BelongsTo / single relation example:

> **Preview:** *Grid belongsto*

## Unsupported relationship

The following types of relationship do not supported

`HasOneThrough`, `HasManyThrough`, `MorphTo`, `MorphToMany`
