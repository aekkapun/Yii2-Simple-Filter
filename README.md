# Simple-Yii2-Checkbox-Filter-Module
Simple Yii2 Checkbox Filter Module

Yii2 filter module v0.02

Video: http://www.youtube.com/watch?v=Vah2j5WzXIs

Installation:
Put `sanex` folder to vendor folder, then add to 
`vendor/yiisoft/extensions.php`
following code:
```
'sanex/yii-filter-module' => 
array (
    'name' => 'sanex/yii-filter-module',
    'version' => '9999999-dev',
    'alias' => 
    array (
      '@sanex/filter' => $vendorDir . '/sanex/yii-filter-module',
    ),
    'bootstrap' => 'sanex\\filter\\Bootstrap',
),
```
..and register module in config file:
```
'modules' => [
    'filter' => [
        'class' => 'sanex\filter\SanexFilter',
    ],
],
```
How to use?
In controller, which has view (main view), where you want show data with filter, set 3 parameters:
```
use sanex\filter\Module;

...

$model = new Catalog; // - model, which data you want to filter
$viewFile = '@sanex/catalog/views/catalog/catalog-ajax'; // - alias (or path) to ajax view, where you want to show data
$filter = Yii::$app->getModule('filter'); // - filter object
```

`$viewFile` - ajax view (not main view!!!). You must create that view file before continue.

As default, result query with filter looks like `SELECT COUNT(*) FROM 'catalog' WHERE 'color' IN ('Green', 'Red')`
If you want create custom query with filter you must call setQuery() method in controller with `\yii\db\ActiveQuery` object as method parameter, that contain query parameters.

```
$query = new \yii\db\ActiveQuery($model);
$query->select(['id', 'name', 'size', 'price', 'country'])->where(['country' => 'Canada'])->orderBy(['price' => SORT_ASC]); 
$filter->setQuery($query);
```

In main view, you must call `setFilter()` method contain array with filter parameters:

```
$filter->setFilter([
    [
        'property' => 'color',
        'caption' => 'Color',
        'values' => [
            'Red',
            'Green',
            'Blue',
            'Black'
        ]
    ],
    [
        'property' => 'size',
        'caption' => 'Size',
        'values' => [
            '45x45',
            '50x50',
            '60x60'
        ]
    ]
]);
```
Then, where you want to render ajax view with filtered data, call `renderDataView()`:
```
$filter->renderDataView($viewFile, $model, 1, ['testParam' => $testParam]);
```
`renderDataView($viewFile, $model, $setDataProvider = false, $viewParams = [])`

`$viewFile` - ajax view file;

`$model` - model;

`(bool)$setDataProvider` - if true - return data as dataProvider, if false or not set - return data as model;

`(array)$viewParams` - parameters, that will be send to ajax view.

In ajax view, you can get filtered data (model or dataProvider) through `$sanexFilterData` variable.
#####Note! Module pass to ajax view only data! You must create in that ajax view `<table></table>` or use GridView widget to show data, same way as with all other Yii2 models!
