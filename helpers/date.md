# DateHelper

Using

``` php
$date = \Windwalker\Helper\DateHelper::getDate('now');
```

Same as

``` php
$dta = JFactory::getDate('now', JFactory::getConfig()->get('offset'));
```

To get the `JDate` object for local timezone.
