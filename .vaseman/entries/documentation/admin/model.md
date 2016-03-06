---
layout: documentation.twig
title: Model

---

# Windwalker Models

Windwalker has many Model classes and each one do different things.

![p-2016-03-06-004](https://cloud.githubusercontent.com/assets/1639206/13554394/981fd2be-e3e1-11e5-8ce5-203274c7aa03.jpg)

# Model

The basic model, it can fetch Table and provides a Registry interface to setting state, just same as Joomla Model.

# ListModel

Using for list page, you can write a query in it then this model will helper you get list from database and handle
 search, filter, sorting and pagination etc. See [ListModel Section](model-list.html)

# ItemModel

A simple Model to get one item record.

``` php
$model->getItem();
```

# CrudModel & AdminModel

These two models use on singular item page. CrudModel provides some methods help you do CRUD to a table. The AdminModel is extends CrudModel, provides some advanced
 functions when you do CRUD in admin.

``` php
$form = $model->getForm();

$model->save($data);
```
