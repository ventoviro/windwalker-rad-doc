---
layout: documentation.twig
title: Date Helper

---

# Using DateHelper

Use

``` php
$date = \Windwalker\Helper\DateHelper::getDate('now');
```

Same as

``` php
$dta = JFactory::getDate('now', JFactory::getConfig()->get('offset'));
```

To get the `JDate` object for local timezone.

# Convert Timezone

Convert a server time to local time (Useful when we get data from SQL and then show to frontend).

``` php
\Windwalker\Helper\DateHelper::toLocalTime($dateString, $format);
```

Convert a local time to server time (UTC) (Useful when we prepare store a data into SQL).

``` php
\Windwalker\Helper\DateHelper::toServerTime($dateString, $format);
```

# Quick Format

``` php
$date = new \JDate;

$date->format(DateHelper::FORMAT_STANDARD);
$date->format(DateHelper::FORMAT_YMD);
$date->format(DateHelper::FORMAT_YMD_HI);
$date->format(DateHelper::FORMAT_YMD_HIS);
$date->format(DateHelper::FORMAT_SQL); // Only support MySQL format
```

# Get Timezone

Quick get system timezone string from Joomla Config. 

``` php
$tz = \Windwalker\Helper\DateHelper::getTZOffset();
```
