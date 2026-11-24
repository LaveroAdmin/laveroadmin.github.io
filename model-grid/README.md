# Model-grid

Class `LaveroAdmin\Admin\Grid` is used to generate tables based on the data model,for example,we have a table `movies` in database:

```
movies
    id          - integer
    title       - string
    director    - integer
    describe    - string
    rate        - tinyint
    released    - enum(0, 1)
    release_at  - timestamp
    created_at  - timestamp
    updated_at  - timestamp
```

And the model of this table is `App\Models\Movie`,The following code can generate the data grid for table `movies`:

```
use App\Models\Movie;
use LaveroAdmin\Admin\Grid;
use LaveroAdmin\Admin\Facades\Admin;

$grid = new Grid(new Movie);

// The first column displays the id field and sets the column as a sortable column
$grid->id('ID')->sortable();

// The second column shows the title field, because the title field name and the Grid object's title method conflict, so use Grid's column () method instead, also column has an additional mehtod Label
// new version automaticly use column function instead of name function, this is the prefered method
$grid->column('title',__('Title'));

// The third column shows the director field, which is set by the display($callback) method to display the corresponding user name in the users table
$grid->column('director')->display(function($userId) {
    return User::find($userId)->name;
});

// The fourth column appears as the describe field
$grid->column('describe');

// The fifth column is displayed as the rate field
$grid->column('rate');

// The sixth column shows the released field, formatting the display output through the display($callback) method
$grid->released('Release?')->display(function ($released) {
    return $released ? 'yes' : 'no';
});

// The following shows the columns for the three time fields
$grid->release_at();
$grid->created_at();
$grid->updated_at();

// The filter($callback) method is used to set up a simple search box for the table
$grid->filter(function ($filter) {

    // Sets the range query for the created_at field
    $filter->between('created_at', 'Created Time')->datetime();
});
```

## Basic Usage

#### Add a column

```
// Add the column directly through the field name `username`
$grid->username('Username');

// The effect is the same as above
$grid->column('username', 'Username');

// Display JSON inline fields
$grid->column('profile->mobile', 'Mobile');

// Add multiple columns
$grid->columns('email', 'username' ...);
```

#### Modify the source data

```
$grid->model()->where('id', '>', 100);

$grid->model()->whereIn('id', [1, 2, 3]);

$grid->model()->whereBetween('votes', [1, 100]);

$grid->model()->whereColumn('updated_at', '>', 'created_at');

$grid->model()->orderBy('id', 'desc');

$grid->model()->take(100);
```

#### Sets the number of lines displayed per page

```
// The default is 20 per page
$grid->paginate(15);
```

#### Modify the display output of column

```
$grid->text()->display(function($text) {
    return str_limit($text, 30, '...');
});

$grid->name()->display(function ($name) {
    return "<span class='label'>$name</span>";
});

$grid->email()->display(function ($email) {
    return "mailto:$email";
});

// column not in table
$grid->column('column_not_in_table')->display(function () {
    return 'blablabla....';
});
```

The closure passed to method `display()` is bind to row data object, you can use other column data in current row.

```
$grid->column('first_name');
$grid->column('last_name');

// column not in table
$grid->column('full_name')->display(function () {
    return $this->first_name.' '.$this->last_name;
});
```

#### Enable double click to edit

```
$grid->enableDblClick();
```

#### Fixed table header at the top of the screen

```
$grid->fixHeader();
```

#### Disable the create button

```
$grid->disableCreateButton();
```

#### Disable Pagination

```
$grid->disablePagination();
```

#### Disable data filter

```
$grid->disableFilter();
```

#### Disable the export button

```
$grid->disableExport();
```

#### Disable row selector

```
$grid->disableRowSelector();
```

#### Disable row actions

```
$grid->disableActions();
```

#### Disable column selector

```
$grid->disableColumnSelector();
```

#### Set options for perPage selector

```
$grid->perPages([10, 20, 30, 40, 50]);
```

## Relation

### One to one

The `users` table and the `profiles` table are generated one-to-one relation through the `profiles.user_id` field.

```
uers
    id      - integer
    name    - string
    email   - string

profiles
    id      - integer
    user_id - integer
    age     - string
    gender  - string
```

The corresponding data model are:

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

You can associate them in a grid with the following code:

```
$grid = new Grid(new User);

$grid->id('ID')->sortable();

$grid->name();
$grid->email();

$grid->column('profile.age');
$grid->column('profile.gender');

//or
$grid->profile()->age();
$grid->profile()->gender();

$grid->created_at();
$grid->updated_at();
```

### One to many

The `posts` and `comments` tables generate a one-to-many association via the `comments.post_id` field

```
posts
    id      - integer
    title   - string
    content - text

comments
    id      - integer
    post_id - integer
    content - string
```

The corresponding data model are:

```
class Post extends Model
{
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
}

class Comment extends Model
{
    public function post()
    {
        return $this->belongsTo(Post::class);
    }
}
```

You can associate them in a grid with the following code:

```
$grid = new Grid(new Post);

$grid->column('id', 'id')->sortable();
$grid->column('title');
$grid->column('content');

$grid->column('comments', 'Comments count')->display(function ($comments) {
    $count = count($comments);
    return "<span class='label label-warning'>{$count}</span>";
});

return $grid;
```

```
$grid = new Grid(new Comment);
$grid->column('id');
$grid->column('post.title');
$grid->column('content');

return $grid;
```

### Many to many

The `users` and`roles` tables produce a many-to-many relationship through the pivot table `role_user`

```
users
    id       - integer
    username - string
    password - string
    name     - string

roles
    id      - integer
    name    - string
    slug    - string

role_users
    role_id - integer
    user_id - integer
```

The corresponding data model are:

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
    public function users()
    {
        return $this->belongsToMany(User::class);
    }
}
```

You can associate them in a grid with the following code:

```
$grid = new Grid(new User);

$grid->id('ID')->sortable();
$grid->username();
$grid->name();

$grid->roles()->display(function ($roles) {

    $roles = array_map(function ($role) {
        return "<span class='label label-success'>{$role['name']}</span>";
    }, $roles);

    return join('&nbsp;', $roles);
});

$grid->created_at();
$grid->updated_at();
```
