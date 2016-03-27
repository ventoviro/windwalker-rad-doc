---
layout: documentation.twig
title: Frontend

---

# Introduction

By default, Joomla use `JRoute::_()` to handle frontend routing, we must build a URL with queries to create a SEF route.

For example:

``` php
echo JRoute::_('index.php?option=com_flower&view=sakura&layout=item&id=25')
```

Will be

```
/{MENU}/sakura/25.html
```

In Windwalker, we provide a routing tool to help developer build URI by a set of custom rules. You can build a route by resource name:

``` php
echo \Windwalker\Router\RadRoute::_('com_flower@sakura', array('id' => 25, 'alias' => 'sakura-alias'));
```

The result will be:

```
/{MENU}/sakura/25/sakura-alias.html
```

# How It Works

Same as native Joomla routing, you must assign your component and view to core menu items first, now we create a menu with alias: `flower`.

![p-2016-03-26-003](https://cloud.githubusercontent.com/assets/1639206/14058599/bedf14e4-f360-11e5-9620-751d055d11c9.jpg)

![p-2016-03-26-006](https://cloud.githubusercontent.com/assets/1639206/14058619/2decaa0e-f361-11e5-8d84-c4c3aa0be7c6.jpg)

And create an item with alias: `example`, ID is `7`:

![p-2016-03-26-010](https://cloud.githubusercontent.com/assets/1639206/14058663/7d7f7f3c-f362-11e5-9257-5591d7b83188.jpg)

Open frontend component file `routing.yml`.

``` yaml
sakuras:
    pattern: sakuras/(id)
    view: sakuras

sakura:
    pattern: sakura/(id)/(alias)
    view: sakura
```

You can see the `sakura` item route rule is `/sakura/(id)/(alias)`, se we type `flower/sakura/7/example` to browser URL bar.

![p-2016-03-26-008](https://cloud.githubusercontent.com/assets/1639206/14058649/37a5be2c-f362-11e5-96c6-47cb1e064d29.jpg)

It is very easy to build route with this item, use `_()`:

``` php
\Windwalker\Router\RadRoute::_('com_flower@sakura', array('id' => 7, 'alias' => 'example'));
```

The return value will be:

```
/{YOUR_SITE_PATH}/flower/sakura/7/example
```

Or use shortcut by component built-in router, which supports ignoring the component option for route name:

``` php
// No longer need `com_flower@`
\Flower\Router\Route::_('sakura', array('id' => 7, 'alias' => 'example'));
```

But you can still build other components' routes by add option prefix:

``` php
// Build com_animal's route
\Flower\Router\Route::_('com_animal@bear', array('id' => 25, 'alias' => 'bear-alias'));
```

## Use Other Route Styles

### `/(id)-(alias)` Style

You can use `(id)-(alias)` style to make your URI. Follow this rules:

``` yaml
# ...

sakura:
    pattern: sakura/(id)-(alias)
    view: sakura
    requirements:
        id: \d+
```

The `requirements.id: \d+` use regex to limit id value must be an integer, so all string after `id` will be `alias`.

![p-2016-03-26-012](https://cloud.githubusercontent.com/assets/1639206/14058708/6a34523e-f364-11e5-8edf-56ac1c69da66.jpg)

You can get variables in controller:

``` php
$id    = $this->input->get('id');
$alias = $this->input->get('alias');
```

### Mutiple Level Routes

You can also add categories path for `sakuras` list page, for example:

``` yaml
sakura_category:
    pattern: /category/(*path)
    view: sakuras
```

``` php
Route::_('sakura_category', array('path' => $category->path));
```

Will be:

```
/{YOUR_SITE_PATH}/flower/category/first/second/third
```

In controller, you can get path as an array:

``` php
$this->input->getVar('path');

/*
Array
(
    [0] => first
    [1] => second
    [2] => third
)
*/
```

# Patterns

## Simple Params

Use parenthesis `()` to wrap param name.

``` http
    pattern: /flower/(id)/(alias)
```

For uri look like : `/flower/25/article-alias-name`, above pattern will be matched and there will be two input params.

``` html
[id] => 25
[alias] => article-alias-name
```

### Custom Input Variables

``` http
    pattern: /flower/(id)/(alias)
    variables:
        foo: bar
```

The attributes in `variables` will auto set to input if this route be matched.

### Limit By Requirement

Use Regular Expression to validate type of input. For example `\d+` indicates that only `Integer` will be accepted as `id` input.

``` http
    pattern: /flower/(id)/(alias)
    requirements:
        id: \d+
```

## Optional Params

### Single Optional Params

Use `(/{anyparam})` to wrap an Optional Param.

``` http
    pattern: flower(/id)
```

Below 2 uris will be matched simultaneously.

```
/flower
/flower/25
```

### Multiple Optional Params

``` http
    pattern: flower(/year,month,day)
```

All uris below will be matched.

```
/flower
/flower/2014
/flower/2014/10
/flower/2014/10/12
```

Matched variables:

```
Array
(
    [year] => 2014
    [month] => 10
    [day] => 12
)
```

## Wildcards

Use Wildcards to match all the successive params in uri.

``` http
    pattern: /king/(*tags)
```

Every param after `/king` will all be matched. For example: `/king/john/troilus/and/cressida`, will get these variables.

```
Array
(
    [tags] => Array
    (
        [0] => john
        [1] => troilus
        [2] => and
        [3] => cressida
    )
)
```

# Route Options

Windwalker router provides a set of options to make your rules flexible.

## `view`

``` yaml
sakura:
    pattern: sakura/(id)
    view: sakura
```

The `view: sakura` means to find `FlowerViewSakuraHtml`, it is as same as `&view=sakura` in HTTP query. 

## `task`

``` yaml
sakura:
    pattern: sakura/save(/id)
    task: sakura.edit.save
```

The `task: sakura.edit.save` means to find `FlowerControllerSakuraEditSave`, it is same as `&task=sakura.edit.save` in HTTP query.

> You can only contain ont of `task` or `view` in route setting.

## `layout`

``` yaml
sakura:
    pattern: sakura/(id)
    view: sakura
    layout: edit
```

The `layout: edit` means to use `tmpl/edit.php` in sakura view, it is as same as `&layout=edit` in HTTP query. 

## `format`

``` yaml
sakura:
    pattern: sakura/(id)
    view: sakura
    format: json
```

The `format: json` means to find `FlowerViewSakuraJson`, it is as same as `&format=json` in HTTP query. 

## HTTP Scheme

Only this HTTP setting will be matched.

``` yaml
sakura:
    pattern: sakura/(id)
    view: sakura
    
    # Only https
    scheme: https
    post: 80
    sslPort: 443
```

## Extra

Extra is some your custom values for this route.

``` yaml
sakura:
    pattern: sakura/(id)
    view: sakura
    extra:
        foo:
            bar: yoo
```

Use this way to get extra values from matched route.

``` php
\Windwalker\Router\RadRouter::getInstance('com_flower')->extra->get('foo.bar'); //yoo
```

## `buildHandler` And `parseHandler`

``` yaml
sakura:
    pattern: sakura/(id)
    view: sakura
    buildHandler: MyRouteHelper::buildSakura
    parseHandler: MyRouteHelper::parseSakura
```

The `*Handler` options means to add hook to build or parse action for this route.

### The `buildHandler`

``` php
class MyRouteHelper
{
    /**
     * Sakura build hook.
     *
     * @param array      $queries  The HTTP query get from RadRoute class.
     * @param boolean    $replace  Replace core build rules and only use the return segments from this method.
     * @param JMenuSite  $menu     The Joomla menu object to get menu items.
     *
     * @return  string|array|void  Replace, append segments or not, return value can be string or array.
     *                             Only works when $replace = true.
     */
    public static function buildSakura(&$queries, &$replace, $menu)
    {
        // Add custom query data
        $queries['my_data'] = 'foo';
        
        // Or return custom segments
        $replace = true;
        
        return 'my/custom/segments';
    }
}
```

Now if you build a route by `$route::_('sakura', ['id' => 25]);`, the generated URI will be:

```
/{YOUR_SITE}/flower/sakura/25?my_data=foo
```

Or return custom segments by setting `$replace` to `true`.

``` php
public static function buildSakura(&$queries, &$replace, $menu)
{
    $replace = true;
    
    unset($queries['id']);
    unset($queries['alias']);
    unset($queries['option']);
    
    return 'my/custom/segments';
}
```

Result:

```
/{YOUR_SITE}/flower/my/custom/segments
```

#### Use Menu Item Alias

You can replace your route by custom rules, for example, if a menu direct to an item, return menu alias to replace sakura route:

``` php
public static function buildSakura(&$queries, &$replace, JMenuSite $menu)
{
    // Get all com_flower menus
    $menuItems = $menu->getItems('component', 'com_flower');

    // Find matched menu item.
    foreach ($menuItems as $menuItem)
    {
        if (isset($menuItem->query['view']) && $menuItem->query['view'] == 'sakura' &&
            isset($menuItem->query['id']) && $menuItem->query['id'] == $queries['id'])
        {
            // Replace core route rule.
            $replace = true;

            // Only return menu Itemid then Joomla will convert to menu alias
            $queries = array('Itemid' => $menuItem->id);
        }
    }

    // No menu matched, follows default rule.
}
```

### The `parseHandler`

``` php
class MyRouteHelper
{    
    /**
	 * Sakura parse hook.
	 *
	 * @param   array  $variables  The variables parse from URI.
	 *
	 * @return  array  The final variables with your custom data.
	 */
	public static function parseSakura(array $variables)
	{
		if (isset($variables['task']) && $variables['task'] == 'sakura.edit.apply')
		{
			$variables['my_data'] = 'bar';
		}

		return $variables;
	}
}
```

ParseHandler is much simpler, just add or delete what you need of variables, and return it. Then you can get data by Input.
 
``` php
$this->input->get('my_data'); //bar
```



