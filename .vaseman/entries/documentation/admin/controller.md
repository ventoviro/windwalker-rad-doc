---
layout: documentation.twig
title: Controller

---

# Single Action

Windwalker controller is follow [Single Responsibility Principle](http://en.wikipedia.org/wiki/Single_responsibility_principle)
, every controller only have one action. For example:

``` php
<?php
// controller/sakura/display.php

use Windwalker\Controller\DisplayController;

class FlowerControllerSakuraDisplay extends DisplayController
{
    protected function doExecute()
    {
        $view = $this->getView();

        return $view->render();
    }
}
```

The `FlowerControllerSakuraDisplay` controller only do one thing, just render this page.
Note you have to return rendered string for component to echo it.

# Task Routing and Default Controller

Add this task in url to fetch controller what you want:

```
index.php?option=com_flower&task=sakuras.state.publish
```

This task will get `FlowerControllerSakurasStatePublish` controller in `controller/sakuras/state/publish.php`,
if this controller not exists, Windwalker will use default `Windwalker\Controller\State\PublishController` instead.

# Executed Hooks

Every Controller have two hooks to let you inject your logic, `prepareExecute()` and `postExecute()`.

See this example:

``` php
<?php
// controller/sakura/edit/save.php

use Windwalker\Controller\Edit\SaveController;

class FlowerControllerSakuraEditSave extends SaveController
{
    protected function prepareExecute()
    {
        // Set something.
        $this->data = $this->input->post;
    }

    // We don't need doExecute() because parent will do it.

    protected function postExecute($data = null)
    {
        // Do some stuff like redirect or session.
        $this->setRedirect('...');

        return $data;
    }
}
```

# Advanced Functions

## Redirect

### Basic Redirect

If you call `setRedirect(...)`, controller will store redirect URL and do redirect after execute completed. 
Or you can call `redirect()` to instantly redirect to the URL you stored into controller. 

``` php
$this->setRedirect($url, 'Mssage');

return true; // of false
```

If you call `redirect($url)` with a URL as first argument, Windwalker will instantly redirect to this URL.

### Redirect for success or fail

In some data handling controller (like `SaveController`), we can set success and fail redirect URL.

``` php
use Windwalker\Bootstrap\Message;

try
{
    // Do something
}
catch (\Exception $e)
{
    $this->setRedirect($this->getFailRedirect(), $e->getMessage(), Message::ERROR_RED);
}

$this->setRedirect($this->getSuccessRedirect(), 'Success', Message::MESSAGE_GREEN);
```

### Redirect to Item or List

You must extend `AbstractRedirectController` to use these features.

``` php
public function getSuccessRedirect()
{
    $this->input->set('layout', 'edit');

    return \JRoute::_($this->getRedirectListUrl(), false);
}
```

``` php
public function getFailRedirect()
{
    return \JRoute::_($this->getRedirectItemUrl($this->recordId, $this->urlVar), false);
}
```

You can use `getRedirectItemUrl()`, `getRedirectListUrl()`, `getRedirectItemAppend()` and `getRedirectListAppend()`
to set some redirect details.

## Setting Config

If you want to set some config to multiple controller, you have to use `Delegator`.

Every controllers group will have a delegator, for example, `sakuras` controllers will have a `delegator.php` in `controller/sakuras`.
You can set some config to or alias it:

``` php
<?php
// controller/sakuras/delegator.php

use Windwalker\Controller\Resolver\ControllerDelegator;

class FlowerControllerSakurasDelegator extends ControllerDelegator
{
	protected function registerAliases()
	{
	    // Set alias here using $this->addAlias($class, $alias);
	}

	protected function createController($class)
	{
	    $this->config['allow_redirect_params'] = array('...');

		return parent::createController($class);
	}
}

```

The alias will get other controller if we set it, and the config will push to every controllers of `sakuras`.

## HMVC

If you want to use other Controller to do something, please using `fetch()` in controller.

This will call save controller and return Boolean, SaveController will not redirect page because it is in HMVC mode.

``` php
$this->fetch('con_flower', 'sakura.edit.save', array('data' => $data));
```

And this can render other view for use:

``` php
$block = $this->fetch('con_flower', 'rose.display', array('layout' => 'foo'));

echo $block;
```

Do not show messages, add `quiet` params:

``` php
$this->fetch('con_flower', 'sakura.edit.save', array('quiet' => true, 'data' => $data));
```

## Return to Target URL

We can add a base64 encoded url in url query, the `redirect()` method will quto redirect to this url.

In View:

``` php
<?php
$return = 'http://google.com';
?>

<a href="index.php?option=com_flower&task=sakura.edit.save&return=<?php echo base64_encode($return);?>">
    Link
</a>
```

Now, when after controller executed, the `redirect()` method will take us to Google.
