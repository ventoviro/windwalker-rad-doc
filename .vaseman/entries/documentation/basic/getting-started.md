---
layout: documentation.twig
title: Getting Started

---

# Installation via Composer

We use composer to install Windwalker RAD.

``` bash
cd /your/joomla/dir
composer create-project windwalker/joomla-rad libraries/windwalker 2.*
```

# Setting PHP CLI

Windwalker generator need PHP CLI, that you can use command line to operate it. If you are in Windows, please make sure
 the Environment Variable of `php.exe` has set.

# Generator Commands

Now, chdir to your Joomla path, type:

``` bash
$ php bin/windwalker generator
```

And you will see:

```
Windwalker Console - version: 2.1
------------------------------------------------------------

[windwalker Help]


Usage:
  windwalker <command> [option]


Options:

  -h | --help       Display this help message.
  -q | --quiet      Do not output any message.
  -v | --verbose    Increase the verbosity of messages.
  --ansi            Set 'off' to suppress ANSI colors on unsupported terminals.

Commands:

  generator    Extension generator.
    init         Init a new extension.
    convert      Convert an extension back to a template.
    add          Add new controller view model system classes(only component).
    test         Generate test cases.

  build        Some useful tools for building system.
    gen-command  Generate a command class.


Welcome to Windwalker Console.
```

# Generate Extensions

Here is some example of how to generate extensions:

## Init Component

Create a component named `com_flower` and with two MVCs `sakura` and `sakuras` in both site and admin.

``` bash
$ php bin/windwalker generator init com_flower sakura.sakuras
```

Create a component in site or admin.

``` bash
$ php bin/windwalker generator init com_flower sakura.sakuras -c admin (site)
```

Create a component and use other sub template `foo`, default is `default`.

``` bash
$ php bin/windwalker generator init com_flower sakura.sakuras -t foo
```

## Add two MVC groups

Add a singular and a plural MVC group to a exists component.

``` bash
$ php bin/windwalker generator add subsystem com_flower rose.roses
```

## Module

Create a module named `mod_flower` in front end.

``` bash
$ php bin/windwalker generator init mod_flower -c site
```

## Plugin

Create a module named `plg_flower` in 'system' group.

``` bash
$ php bin/windwalker generator init plg_system_flower
```

