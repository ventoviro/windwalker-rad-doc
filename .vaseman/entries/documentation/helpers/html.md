---
layout: documentation.twig
title: Html Helper

---

# Repair Tags

We can using `repair()` method to repair unpaired tags by `php tidy`, if tidy extension not exists, will using simple tag close function to fix it.

``` php
$html = '<p>foo</i>';

$html = \Windwalker\Helper\HtmlHelper::repair($html);

echo $html; // <p>foo</p>
```
