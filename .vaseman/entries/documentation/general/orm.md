---
layout: documentation.twig
title: Simple ORM

---

# Introduction

Windwalker Simple ORM is a tool to configure relations between different tables, it helps `JTable` and `DataMapper` 
ables to operate other relative tables and data.    

# One to Many

If we have a table `brands` relation to `cars` and `bikes`, the `cars` and `bikes` both contains a column `brand_id`, 
this is `one-to-many` relation.

![diagram](https://cloud.githubusercontent.com/assets/1639206/14060861/ee5ebb70-f3ab-11e5-8f02-fcab25ff3dff.png)

We can configure this relation in Windwalker `Table` class:

``` php
<?php

use Windwalker\Relation\Action;
use Windwalker\Table\Table;

class VehicleTableBrand extends Table
{
    public function __construct()
    {
        parent::__construct('#__flower_brands');
    }

    protected function configure()
    {
        // Configure One to Many Relations
        $this->_relation->addOneToMany('cars') // Property to store relation data
        
            // Use another JTable to handle CRUD, and the foreign key mapping.
            ->targetTable(new VehicleTableCar, array('id' => 'brand_id'))
            ->onUpdate(Action::CASCADE) // On update
            ->onDelete(Action::CASCADE); // On delete
        
        $this->_relation->addOneToMany('roses')
            ->targetTable(new VehicleTableBike, array('id' => 'brand_id'))
            ->onUpdate(Action::CASCADE)
            ->onDelete(Action::CASCADE);
    }
}
```

You can just send the table name to `targetTable()`, the `Table` object will be auto created.

``` php
->targetTable('#__vehicle_cars', array('id' => 'brand_id'))
```

## CRUD

When `VehicleTableBrand::load()`, all relative `cars` and `bikes` will also loaded.
 
``` php
$brandTable = JTable::getInstance('Brand', 'VehicleTable');

$brandTable->load(3);

// \Windwalker\Data\DataSet
foreach ($brandTable->cars as $car)
{
    // \Windwalker\Data\Data
    echo $car->title;
}
```
 
If we call `VehicleTableBrand::store()`, all data in `cars` and `bikes` will also batch store back to database. 
The store action was handled by `VehicleTableCar` and `VehicleTableBike` objects which we set when the relation configuring.
  
``` php
$brandTable->store(); // All cars and bikes data will be updated.

$brandTable->cars[] = new Data(array('title' => 'A new car'));

$brandTable->store(); // The latest inserted data will be created. 
```

If we call `VehicleTableBrand::delete()`, all relative data will auto be deleted too.

``` php
$brandTable->load(3);
$brandTable->delete(); // All cars and bikes relative to this brand item will be delete.
```

# Actions

The relation handling above is `CASCADE`, which means sync all data with update and delete action.
We can set `onUpdate` and `onDelete` to other actions.
 
``` php
// In VehicleTableBrand class

use Windwalker\Relation\Action;

// ...

$this->_relation->addOneToMany('cars')
    ->targetTable(new VehicleTableCar, array('id', 'brand_id'))
    ->onUpdate(Action::SET_NULL)
    ->onDelete(Action::SET_NULL);
```

| Action | Description |
| ------ | ----------- |
| `Action::CASCADE` | When main record update or change the key value, all relative records' foreign keys will also change. If main record be deleted, all relative records will also be deleted too. |
| `Action::NO_ACTION` or `Action::RESTRICT` | If main record update, delete or change key value, all relative record will have no any actions. |
| `Action::SET_NULL` | If main record update, delete or change key value, all relative records' foreign key will be st to `NULL` |

See:

- [https://en.wikipedia.org/wiki/Foreign_key](https://en.wikipedia.org/wiki/Foreign_key)
- [http://stackoverflow.com/a/6720458](http://stackoverflow.com/a/6720458)
 
# One to One

If a table `members` and `member_profiles` is one-to-one relation, and the `member_profiles.member_id` matching the `members.id`. 

![diagram](https://cloud.githubusercontent.com/assets/1639206/14061115/ddd32f0a-f3b2-11e5-94f8-b4de40c2ffb2.png)

``` php
$this->_relation->addOneToOne('profile')
    ->targetTable(new Table('#__member_profiles'), array('id' => 'member_id'));
```

In this case, the loaded relative item will be a `Data` object not `DataSet`.

``` php
$memberTable->load(5);
$memberTable->profile; // \Windwalker\Data\Data
```

# Many to One

We can set `cars` belongs to `brands`, this case is very similar to `articles` and `category`, both are many-to-one relation.
 The key mapping must in turn.

``` php
// in VehicleTableCar class

$this->_relation->addManyToOne('brand')
    ->targetTable(new FlowerTableBrand, array('brand_id' => 'id'));
```

Now if we load a car record, we will get the matched brand item.

``` php
$carTable->load(4);
$carTable->brand; // \Windwalker\Data\Data
```

> Many to One relation only support `read` now, `delete` and `store` not works.

# Many to Many

Many to many relation need a middleware table to support multiple mapping. If we have a `members` and `groups` is many-to-many relation,
then we must have a `member_group_maps` table to handle this relation.

![mtm](https://cloud.githubusercontent.com/assets/1639206/14061202/75fd770c-f3b5-11e5-83ef-194589526fdd.png)

``` php
// In member table object 

$this->_relation->addManyToMany('groups')
    // We usually not create a JTable for mapping table, so just use table name at first argument.
    ->mappingTable('#__member_group_maps', array('id' => 'member_id'))
    ->targetTable(new FlowerTableGroup, array('group_id' => 'id'));
```

# Flush

If our relation update is to delete all and re-create all, we can set it is `flush`.

``` php
$this->_relation->addOneToMany('cars')
    ->targetTable(new VehicleTableCar, array('id', 'brand_id'))
    ->onUpdate(Action::SET_NULL)
    ->onDelete(Action::SET_NULL)
    ->flush(true); // Add this
```

Now if we call `store()`, all relation data will be deleted and re-insert all data.

# Relationship for DataMapper

If you hope the `DataMapper` can also handle relationship, you must extends to `AbstractObservableDataMapper`.

> NOTE: In DataMapper, the relationship handler still use `Table` object.

``` php
use Windwalker\DataMapper\Adapter\DatabaseAdapterInterface;
use Windwalker\Table\Table;

class VehicleMapperBrand extends \Windwalker\DataMapper\ObservableDataMapper
{
    public function __construct(DatabaseAdapterInterface $db = null)
    {
        parent::__construct('#__vehicle_brands', 'id', $db);
    }

    protected function initialise()
    {
        // Configure One to Many Relations
        $this->_relation->addOneToMany('cars')
            ->targetTable(new VehicleTableCar, array('id' => 'brand_id'))
            ->onUpdate(Action::CASCADE)
            ->onDelete(Action::CASCADE);
    }
}

$mapper = new VehicleMapperBrand;

$brands = $mapper->find(array('state' => 1));

foreach ($brands as $brand)
{
    foreach ($brand->cars as $car)
    {
        $car->title;
    }
}
```

# Global Configuring

Sometimes we will hope we can only configure once and work on both `Table` and `DataMapper`, or maybe we need configure 
relationship in different conditions, we can use `RelationContainer` to make global configuring. When `Table` and `DataMapper`
created, they will auto register these configurations.

``` php
// Set it in component class.

final class FlowerComponent extends \Flower\Component\FlowerComponent
{
    public function prepare()
    {
        parent::prepare();

        // Get RelationContainer and a Relation object
        $relation = $this->container->get('relation.container')->getRelation('#__flower_sakuras');

        $relation->addManyToOne('foo')
            ->targetTable(new FlowerTableFoo, array('foo_id' => 'id'));

        $relation->addManyToOne('category')
            ->targetTable(JTable::getInstance('Category'), array('catid' => 'id'));
    }
}
```

The `$relation` which get from `RelationContainer` is a singleton `Relation` object for `#__flower_sakuras` table. 
All configurations we set to this table will be keep in `RelationContainer` and latter if we create `Table` or `DataMapper`,
the relation configuration will auto register to these two objects.
