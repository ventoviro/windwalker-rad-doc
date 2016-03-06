---
layout: documentation.twig
title: Create Component

---

# Using Generator

Now we'll use a component named `com_flower` with `sakura` and `sakuras` controllers to tutorial.

Please type:

``` bash
$ php bin/windwalker generator init com_flower sakura.sakuras
```

Here is the success message.

![p-2016-03-06-006](https://cloud.githubusercontent.com/assets/1639206/13554462/646f10f4-e3e3-11e5-96a0-90f65c38882e.jpg)

> If CLI return ` Could not connect to MySQL.` message, check your mysql account in configuration.php

> The Mac environment may caused by `mysql.sock` issue, see [This solution](https://gist.github.com/asika32764/6760580).

# Discover Component

Go to Joomla admin and discover this component.

![image](https://cloud.githubusercontent.com/assets/1639206/13554468/76a29386-e3e3-11e5-8a00-ac878e9c6a3c.png)

Install it.

![image](https://cloud.githubusercontent.com/assets/1639206/13554471/7fcdc0b6-e3e3-11e5-8138-b727ac6f4b6b.png)

Then, click image into component view.

![image](https://cloud.githubusercontent.com/assets/1639206/13554474/8f953c36-e3e3-11e5-8a28-5f1997014b1a.png)

The database has auto created:

![image](https://cloud.githubusercontent.com/assets/1639206/13554477/9b41d346-e3e3-11e5-8335-c3c1fb0c59b3.png)

# Add Subsystem

This command can add two controllers, item edit and list:

``` bash
$ php bin/windwalker add subsystem com_flower rose.roses
```

We call these two MVC groups an subsystem.

# Folder Structure of Component

## Admin

![image](https://cloud.githubusercontent.com/assets/1639206/13554478/a9c91b2c-e3e3-11e5-95cd-f0cfd9a65dfd.png)

| Folder | Desc   |
|--------|--------|
| asset  | CSS & JS etc. |
| etc    | Config files  |
| controller             | Controllers |
| **helper** / helpers   | Helper classes. Plural folder is for legacy use. |
| images        | Images |
| language      | Languages, site language is in admin together. |
| model         | Models |
| sql           | The SQL needed when component install |
| src           | Some useful classes, using PSR-0 autoloading here. |
| table         | Tables, Active Record. |
| view          | Views |

## Site

| Folder | Desc   |
|--------|--------|
| asset        | CSS & JS files |
| controller   | Controllers |
| helper       | Helpers. If you want to use some classes both in site and admin, put it in `src` |
| images       | Images      |
| model        | Models      |
| sql          | The SQL needed when component install |
| view         | Views       |

# Include Component Library in Other Extensions

There is an important file in admin `src/init.php`.

If you include this file, it will load component required classes and windwalker.
