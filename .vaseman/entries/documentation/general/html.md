---
layout: documentation.twig
title: Html Builder

---

# Html & Dom Builder

This is a convenience class to create XML and HTML element in an OO way.

## DomElement

DomElement and DomElements is use to create XML elements.

``` php
use Windwalker\Dom\DomElement;

$attrs = array('id' => 'foo', 'class' => 'bar');

echo $dom = (string) new DomElement('field', 'Content', $attrs);
```

Output:

``` xml
<field id="foo" class="bar">Content</field>
```

Add Children

``` php
use Windwalker\Dom\DomElement;

$attrs = array('id' => 'foo', 'class' => 'bar');

$content = array(
    new DomElement('option', 'Yes', array('value' => 1)),
    new DomElement('option', 'No', array('value' => 0))
);

echo $dom = (string) new DomElement('field', $content, $attrs);
```

The output will be:

``` xml
<field id="foo" class="bar">
    <option value="1">Yes</option>
    <option value="0">No</option>
</field>
```

## HtmlElement

HtmlElement is use to create HTML elements, some specific tags will force to unpaired.

``` php
use Windwalker\Dom\HtmlElement;

$attrs = array(
    'class' => 'btn btn-mini',
    'onclick' => 'return false;'
);

$html = (string) new HtmlElement('button', 'Click', $attrs);
```

Then we will get this HTML:

``` html
<button class="btn btn-mini" onclick="return false;">Click</button>
```

### Get Attributes by Array Access

``` php
$class = $html['class'];
```

## DomElements & HtmlElements

It is a collection of HtmlElement set.

``` php
$html = new HtmlElements(
    array(
        new HtmlElement('p', $content, $attrs),
        new HtmlElement('div', $content, $attrs),
        new HtmlElement('a', $content, $attrs)
    )
);

echo $html;
```

OR we can iterate it:

``` php
foreach ($html as $element)
{
    echo $element;
}
```

## Attributes

``` php
$html = new HtmlElement('input', array(
    'data-string' => 'string',
    'data-empty' => '',
    'data-true'  => true,
    'data-false' => false,
    'data-null'  => null,

    // Special attributes
    'checked'    => 'checked',
    'disabled'   => true,
    'readonly'   => false
));

echo $html;
```

Result

``` html
<input data-string="string" data-empty="" data-true checked="checked" disabled="disabled">
```

# Formatter

`DomFormatter` and `HtmlFormatter` will help us format `XML` / `HTML` string.

``` php
$xml = '<field id="foo" class="bar"><option value="1">Yes</option><option value="0">No</option></field>';

DomFormatter::format($xml);
```

Result

``` xml
<field id="foo" class="bar">
    <option value="1">Yes</option>
    <option value="0">No</option>
</field>
```

`HtmlFormatter` will convert some tags to unpaired element, e.g. `<img>`.
