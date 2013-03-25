.. _tutorials.request-life-in-mvc-application.rst:

Life of a request in a Zend Framework 2 MVC application
=======================================================

index.php initializes the MVC application.

the init method grabs the main service manager configuration from the main
application.config.php file.

the service manager then gets configured. a couple of default invokables
and factories are being set.

the zend\mvc\service\eventmanagerfactory creates an event manager and is set into it
a shared event manager.

the zend\mvc\service\modulemanagerfactory does a few things while creating the
zend\modulemanager\modulemanager object

Setting up the module manager
-----------------------------

modulemanagerfactory
^^^^^^^^^^^^^^^^^^^^

creates first a zend\modulemanager\servicelistener object that allows the developer
to configure different things from within the modules (either from module.php or the
module.config.php files). These things include, among other, setting router deffinitions,
configuring the service manager, controller plugins, view helpers, input filters and validators, etc.

different listeners are attached to the event manager:
    - moduleloaderlistener, that sets up module autoloading
    - moduleresolverlistener, that creates the Module objects when needed
and listeners that are triggered during module loading:
    - autoloaderlistener, that allows for configuring the autoloader by each module
    - ModuleDependencyCheckerListener, that allows for the developer to define dependency checks for their modules
    - inittrigger, that, if the method is defined, calls the init method of each module
    - onbootstraplistener, that will on bootstrap call the onBootstrap method of the module
    - locatorregistrationlistener, that allows to register the module class in the service manager so it can be
      retrieved later on during the application's lifetime, if needed
    - configlistener, that allows to configure the application's configuration from the modules

finally the module names set in the application's config file are set on the module manager, and all
these listeners are connected to the module manager.

loading modules
^^^^^^^^^^^^^^^

once the module manager is configured via its factory, it's loadModules method is called.

the module manager itself does little while loading the modules. for each module it triggers the
module resolve event, which returns the module object. after that the load module event is triggered
which in turn calls the listeners previously attached.

after all the modules are loaded, the service manager is configured with all the configuration files merged
together - this includes the module configuraiton files and the application configuration files.


Bootstrapping the application
-----------------------------
