---
layout: documentation.twig
title: Using ListModel

---

# Introduction

In Joomla legacy model, we writing query in `getListQuery()` and `getItems()` will help us fetch list from database.

In windwalker, we don't need to write whole `getListQuery()`, we can write what we actually want to do, and others will auto
done by model.

# Configure Tables

## Select & Join

Writing table config in `configureTables()`:

``` php
protected function configureTables()
{
    $queryHelper->addTable('sakura', '#__flower_sakuras')
        ->addTable('category',  '#__categories', 'sakura.catid      = category.id')
        ->addTable('user',      '#__users',      'sakura.created_by = user.id')
        ->addTable('viewlevel', '#__viewlevels', 'sakura.access     = viewlevel.id')
        ->addTable('lang',      '#__languages',  'sakura.language   = lang.lang_code');
}
```

We set all table information into `QueryHelper` and this object will help us generate queries.
`QueryHelper` also generate filter fields by table you set into it.

## The Selected Fields

`QueryHelper` will auto generate select fields:

```
`sakura`.`id` AS `id`,
`sakura`.`asset_id` AS `asset_id`,
`sakura`.`catid` AS `catid`,
`sakura`.`title` AS `title`,
`sakura`.`alias` AS `alias`,

# ...

`category`.`id` AS `category_id`,
`category`.`asset_id` AS `category_asset_id`,
`category`.`extension` AS `category_extension`,
`category`.`title` AS `category_title`,
`category`.`alias` AS `category_alias`,

# ...

`user`.`id` AS `user_id`,
`user`.`name` AS `user_name`,
`user`.`username` AS `user_username`,
`user`.`email` AS `user_email`,
`user`.`password` AS `user_password`,

# etc...
```

It looks terrible but useful, if you join multiple tables, the field name will not conflict and more semantic,
we can get main table field by `$item->title` or `$item->id`, and get joined fields by `$item->category_title` or `$item->user_id`, 
it is very clear and understandable.

If you want to override select fields, just set it in state:

``` php
// Set your own select fields then Windwalker will not auto generate fields.
$this->state->set('query.select', array('sakura.*', 'category.id AS cid'));
```

# Using Filters

## Filter Form XML

Open `model/form/sakuras/filter.xml`, we can write some filter fields here:

``` xml
<?xml version="1.0" encoding="utf-8"?>
<form>
	<fields name="search">
		<!-- Ignore -->
	</fields>

	<!-- Filter -->
	<fields name="filter">
		<!-- Ignore -->

		<field
            name="sakura.access"
            type="list"
            default=""
            label="Access"
            onchange="this.form.submit();"
        >
            <option></option>
            <option>JALL</option>
            <option value="1">Public</option>
            <option value="2">Register</option>
        </field>
	</fields>

	<fields name="list">
		<!-- Ignore -->
	</fields>
</form>
```

The `name` is same as what we want to filter in SQL column (eg: `sakura.state`), note you will need `onchange="this.form.submit();"` to submit form.

Back to admin, you'll see filter select appeared:

![image](https://cloud.githubusercontent.com/assets/1639206/13554449/f87b44bc-e3e2-11e5-9093-5fc7d1fabc23.png)

## Custom Filter Query

Sometimes we will need to filter a period, so we can't always use `=` to filter fields. So we can extend the filter rules.

First, add this field in filter XML:

``` xml
<field
    name="a.date"
    type="list"
    default=""
    onchange="this.form.submit();"
    class=""
>
    <option></option>
    <option>- Choose Time Period -</option>
    <option value="year">In 1 Year</option>
    <option value="month">In 1 Month</option>
    <option value="week">In 1 Week</option>
</field>
```

Write our logic in `configureFilters()`:

``` php
protected function configureFilters($filterHelper)
{
    $filterHelper->setHandler(
        'sakura.date',
        function($query, $field, $value)
        {
            if ('' !== (string) $value)
            {
                $now = new DateTime;
                
                $now->modify('-1 ' . $value);
    
                $query->where($field . ' > ' . $now->format('Y-m-d'));
            }
        }
    );
}
```

OK, we can filter time period in our ist.

# Search

## Fulltext Search

Search function is very similar to filter, open `model/form/sakuras/filter.xml`, you will see:

``` xml
<fields name="search">
    <field name="field"
        type="hidden"
        default="*"
        label="JSEARCH_FILTER_LABEL"
        labelclass="pull-left"
        class="input-small"
        >
        <option value="*">JALL</option>
        <option value="sakura.title">JGLOBAL_TITLE</option>
        <option value="category.title">JCATEGORY</option>
    </field>

    <field
        name="index"
        type="text"
        label="JSEARCH_FILTER_LABEL"
        hint="JSEARCH_FILTER"
        />
</fields>
```

The `name="field"` field is for setting our search fields, every option means one field, let us add a new field:

``` xml
<field name="field"
    type="hidden"
    default="*"
    label="JSEARCH_FILTER_LABEL"
    labelclass="pull-left"
    class="input-small"
    >
    <option value="*">JALL</option>
    <option value="sakura.title">JGLOBAL_TITLE</option>
    <option value="category.title">JCATEGORY</option>

    <option value="lang.title">Language</option>
</field>
```

OK, we search english, the language title can be searched:

![image](https://cloud.githubusercontent.com/assets/1639206/13554438/d01a6228-e3e2-11e5-8f73-8e66407845cb.png)

# Single Field Search

Change `field` field type to `list`, then you will able to choose field to search single column.

``` xml
<field name="field"
    type="list"
    default="*"
    label="JSEARCH_FILTER_LABEL"
    labelclass="pull-left"
    class="input-small"
    >
    <option value="*">JALL</option>
    <option value="sakura.title">JGLOBAL_TITLE</option>
    <option value="category.title">JCATEGORY</option>

    <option value="lang.title">Language</option>
</field>
```

![joomla323b_-_administration_-_sakura_list](https://cloud.githubusercontent.com/assets/1639206/2565963/c62520b2-b8bc-11e3-96ae-0087bb376abc.png)

# Multiple Search

Add a fieldset `multisearch`.

``` xml
<fields name="search">
    <field name="field"
        type="list"
        default="*"
        label="JSEARCH_FILTER_LABEL"
        labelclass="pull-left"
        class="input-small"
        >
        <option value="*">JALL</option>
        <option value="sakura.title">JGLOBAL_TITLE</option>
        <option value="category.title">JCATEGORY</option>

        <option value="lang.title">Language</option>
    </field>

    <field
        name="index"
        type="text"
        label="JSEARCH_FILTER_LABEL"
        hint="JSEARCH_FILTER"
        />

    <!-- For multiple search -->
    <fieldset name="multisearch">
        <field
            name="sakura.title"
            type="text"
            label="Title"
            hint="JSEARCH_FILTER"
            />

        <field
            name="category.title"
            type="text"
            label="Category"
            hint="JSEARCH_FILTER"
            />
    </fieldset>

</fields>
```

![140331-0016](https://cloud.githubusercontent.com/assets/1639206/2565802/ae00fb98-b8ba-11e3-9981-a69c203bcc29.jpg)

# Custom Search Query

We also use `SearchHelper` to set handler, same as `FilterHelper`:

``` php
/**
 * configureSearches
 *
 * @param \Windwalker\Model\Filter\SearchHelper $searchHelper
 *
 * @return  void
 */
protected function configureSearches($searchHelper)
{
    $searchHelper->setHandler(
        'sakura.title',
        function ($query, $filed, $value)
        {
            // Custom search query...
        }
    );
}
```

# Allow Fields

`$this->allowFields` is a white list to allow limited fields into filter and search. By default, Windwalker will auto generate all fields
in tables as allow fields. If we send a field named `sakura.foo` but this field not in our table, not thing will happen. 
This function protect our model from invalid user input, without this list, everyone can set a URL query `&filter[sakura.foo]=1`
to request then your application will return DB error.

![p-2016-03-06-008](https://cloud.githubusercontent.com/assets/1639206/13554655/8c6e97d2-e3e8-11e5-95f4-53c8783c4633.jpg)

## Custom Allow Fields

Add some custom fields on `allowFields` property, ListModel will merge them into auto generated list.
 
``` php
class FlowerModelSakuras extends ListModel
{
	protected $allowFields = array(
		'my.custom_field',
		'other.field'
	);
	
	// ...
```

## Field Mapping

If some user input are different from table field, we can add alias to mapping them.

``` php
class FlowerModelSakuras extends ListModel
{
    // If user input named created_name, we allow this name first.
	protected $allowFields = array(
		'created_date'
	);

    // Set created_date map to sakura.created field
	protected $fieldMapping = array(
	    'created_date' => 'sakura.created'
	);
	
	// ...
```

## Work with Filter & Search Helper

If we need a special filter with multiple user inputs, we can make allow fields work with filter helper.
For example, we have two filter named `start_date` and `end_date`, both relative to `sakura.created` field.
We can use this code to filter a date range between `start_date` and `end_date`.
  
``` php
class FlowerModelSakuras extends ListModel
{
	protected $allowFields = array(
		'start_date',
		'end_date'
	);
	
	protected function configureFilters($filterHelper)
    {
        $filterHelper->setHandler(
            'start_date',
            function($query, $field, $value)
            {
                if ('' !== (string) $value)
                {
                    $query->where('sakura.created >= ' . $query->quote($value));
                }
            }
        );
        
        $filterHelper->setHandler(
            'end_date',
            function($query, $field, $value)
            {
                if ('' !== (string) $value)
                {
                    $query->where('sakura.created <= ' . $query->quote($value));
                }
            }
        );
    }
```

