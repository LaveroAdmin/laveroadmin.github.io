# Grid Header & Footer

You can add addional content to the header and footer of the grid.

```
$grid->header(function ($query) {
    return 'header';
});

$grid->footer(function ($query) {
    return 'footer';
});
```

The parameter `$query` of the closure function is an instance of `\Illuminate\Database\Eloquent\Builder`, which can be used to execute some custom queries to get data. The following are examples of the use of two different scenarios.

## Header

For example, to insert a pie chart in the table header of the user data module to display the status ratio of the page, you can follow the following method to achieve

First query the status ratio data:

```
$grid->header(function ($query) {
    $status = $query->select(DB::raw('count(status) as count, status'))
                ->groupBy('status')->get()->pluck('count', 'status')->toArray();
    $doughnut = view('admin.charts.status', compact('status'));
    return new Box('Status ratio', $doughnut);
});
```

To show charts you can use [chartjs](https://www.chartjs.org/ "https://www.chartjs.org/"), by adding chartjs in `app/Admin/bootstrap.php`

```
// the file chart.min.js is places in public/admin-assets/ folder.
//Note that using a folder names admin in public will break the default admin url
Admin::js('/admin-assets/chart.min.js');
```

To build the chart, the view file `resources/views/admin/chart/status.blade.php` is as follows

```
<canvas id="doughnut" width="200" height="200"></canvas>
<script>
    var config = {
        type: 'doughnut',
        data: {
            datasets: [{
                data: [
                    {{ $status['0'] }},
                    {{ $status['1'] }}
                ],
                backgroundColor: [
                    'rgb(54, 162, 235)',
                    'rgb(255, 99, 132)'
                ]
            }],
            labels: [
                'Online',
                'Offline'
            ]
        },
        options: {
            maintainAspectRatio: false
        }
    };

    var ctx = document.getElementById('doughnut').getContext('2d');
    new Chart(ctx, config);
</script>
```

The effect is as follows:

> **UI Preview:** Custom content (e.g. Chart.js statistics or metrics cards) rendered above the table.

## Footer

A more common scenario is to display statistical information at the foot of the data table. For example, to add revenue statistics to the footer of the order table, you can refer to the following code:

```
$grid->footer(function ($query) {

    // Query the total pages with status 1
    $data = $query->where('status', 1)->sum('status');

    return "<div style='padding: 10px;'>Total online ： $data";
});
```

> **UI Preview:** Custom footer markup rendered below the table.

If you have a more complex foot that needs to be displayed, you can use the view object as you did in the header example.
