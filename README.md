# Windwalker RAD Framework

![windwalker-logo](https://cloud.githubusercontent.com/assets/1639206/2814728/b8419496-ceae-11e3-911b-5743b474ed5d.png)

**Windwalker** is a RAD framework of Joomla! CMS.

We provides modern functions and interfaces help developer quickly create extensions.
Windwalker contains single action controller(Joomla new MVC), XUL engine, console interface, Dependency Injection,
DataMapper and code generator.

Hope our framework bring you a joy time :)

### Version 1 and 2

This is the version 2 repository, all folder with lower case is legacy code, all folders with capitals is for version 2 and follows PSR-0 naming standard.

The version 2 is still work in process.

## Installation Via Composer

``` bash
cd /your/joomla/dir
composer create-project windwalker/joomla-rad libraries/windwalker dev-staging -s dev
```

## Generate a new extension

``` bash
php bin/windwalker generator init com_flower sakura.sakuras -c admin
```

## About
Author
:   [Simon Asika](mailto://asika@asikart.com)

Joomla!CMS version
:   3.2 and newer

First release
:   2012-05-05

## All extensions created by Windwalker

- Asikart Quickicons: https://github.com/asikart/quickicons
- Asikart Quickcontent: https://github.com/asikart/quickcontent
- Remote and Local Image Manager: https://github.com/asikart/remoteimage
- ACE x Markdown Editor: https://github.com/asikart/ace-markdown-editor
- Asikart UserXTD: https://github.com/asikart/userxtd

