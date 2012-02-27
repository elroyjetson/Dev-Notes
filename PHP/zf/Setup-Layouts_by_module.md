# How to specify layouts by module in Zend Framework

### Assumptions

* Tested on Zend Framework 1.11.11
* Application has been setup to use modules

### General Steps

1. Set default layout in application.ini
2. Create controller plugin to become the layout picker


### Step 1: Set default layout in application.ini

Add the path to the layouts folder and make the default layout name (layout.phtml) match the default module name (default.phtml) which seems more sensible.

    resources.layout.layoutpath = APPLICATION_PATH "/layouts"
    resources.layout.layout = default

### Step 2:  Create controller plugin to become the layout picker

Setup autoloader for plugin in application.ini and add resource for frontController Plugin

    // ZC_ is the namespace for ZendCast where I found the instructions
    autoloaderNamespaces.zc = "ZC_"
    resources.frontController.plugins.LayoutPicker = "ZC_Controller_Plugin_LayoutPicker"

Create the LayoutPicker file in the library/ZC/Controller/Plugin/ directory with filename of LayoutPicker.php

    <?php
    class ZC_Controller_Plugin_LayoutPicker
        extends Zend_Controller_Plugin_Abstract
    {
        public function preDispatch(Zend_Controller_Request_Abstract $request)
        {
            Zend_Layout::getMvcInstance->setLayout($request->getModuleName());
        }
    }

Now if you have an admin module that needs a different layout from the default, create an module_name.phtml in the layouts folder with the new layout.

### Additional Resources

* [ZendCast:Moving Bootstrap Code to Configuration](http://www.zendcasts.com/moving-bootstrap-code-to-configuration/2009/11/)