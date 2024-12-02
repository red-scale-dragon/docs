# Config Files

There are a number of configuration settings and files that Dragon Framework adds to your app directory. Below is an outline of their settings with pages in alphabetal order, and options listed in the order in which they appear in the config file.

## admin_menu.php

The Dragon Framework configuration file for creating admin menus. This is read by `Dragon\Hooks\AdminPluginHooks::buildAdminMenu()`, and executes automatically during boot up.

The `menu` Array
-----------------

Possible values: Any controller class name that extends `Dragon\Http\Controllers\AdminPageController`

List your top level admin menu controllers in this array.

!!! note
    Configuration for page-specific JS/CSS assets, icons, menu name, and more are found in your controller. Be sure to check out the section on [how to configure an admin menu page](../how-to-articles/configuring-admin-menus).

The `submenu` Array
--------------------

Possible values: Any controller class name that extends `Dragon\Http\Controllers\AdminPageController`

List your submenu controllers in this array. They still extend the same controller, but they are configured differently. See the note above on where to find the documentation on configuring them.

The `hidden_menu` Array
------------------------

Possible values: Any controller class name that extends `Dragon\Http\Controllers\AdminPageController`

Hidden menus are submenus that don't appear in the sidebar links. These are great for pages that require extra data sent to them to load correctly, and get linked to by your other pages.

## app.php

The following are new options added to the app.php configuration file.

The `namespace` String
-----------------------

Possible values: Any string you'd like to use as a namespace prefix

Options in the DB, shortcodes, and many more things use namespaces to keep your code from affecting other plugins installed on the same site. Set this to a unique string and behind the scenes Dragon Framework will add and remove the namespace for you so you can work with any key names you'd like.

The `providers` array
-----------------------

Possible values: List of all service providers

This this of providers contains service providers contains all base service providers from Laravel and Acorn, but also includes the following list of Dragon Framework service providers.

- `AppServiceProvider` - Stub provider for your own application code
- `BladeDirectiveServiceProvider` - Provides blade directives and custom Blade if statements ([Which ones?](blade-directives))
- `ShortcodeServiceProvider` - Loads all the shortcodes you have registered in [`config('shortcodes.controllers')`](#shortcodes-php)
- `TaxonomyServiceProvider` - Loads all the custom taxonomies you have listed in [`config('taxonomies.tax')`](#taxonomies-php)
- `PostTypeServiceProvider` - Loads all the custom post types you have listed in [`config('post_types.post_types')`](#the-post_types-array)
- `AssetsServiceProvider` - Loads JS and CSS assets you have configured globally for the admin area, front end, or both, as outlined in [`config('assets)`](#assets-php)
- `FormRequestServiceProvider` - Currently a Laravel-specific service provider
- `RoleServiceProvider` - Adds all the roles you have configured in [`config('roles.roles')`](#roles-php)
- `AjaxServiceProvider` - Loads all the Ajax hooks you've configured in [`config('hooks.ajax')`](#the-ajax-array)
- `CronServiceProvider` - Loads the cron job data configured in [`config('cron')`](#cron-php)

## assets.php

The following are new options added to the assets.php configuration file.

The `admin` Array
------------------

Possible values: An array with `js` and `css` keys set to [appropriate values](#values-for-js-and-css)

These JS and CSS files are loaded for the entire admin area. ([How do I add page-specific assets?](../how-to-articles/configuring-admin-menus#page-specific-assets))

The `frontend` Array
------------------

Possible values: An array with `js` and `css` keys set to [appropriate values](#values-for-js-and-css), and `enable_ajax` set to `true` or `false`

These JS and CSS files are loaded for the front end of your website. ([How do I add page-specific assets?](../how-to-articles/configuring-admin-menus#page-specific-assets)) If `enable_ajax` is set to true, the `admin_url` variable will be available on the front end for ajax requests.

The `global` Array
------------------

Possible values: An array with `js` and `css` keys set to [appropriate values](#values-for-js-and-css)

These JS and CSS files are loaded for the entire admin area and the entire front end of your website. ([How do I add page-specific assets?](../how-to-articles/configuring-admin-menus#page-specific-assets))

Values for `js` and `css`
--------------------------

Possible values: A tag name set to an array as shown in the example below

The preceeding three keys for `admin`, `frontend`, and `global` all mention needing "appropriate values" for `js` and `css`. `js` and `css` are both arrays with their keys set to the tag name of the asset. (Ex. `my-table-script` or the like. Choose what you'd like.) The arrays set to those tags include:

**`css`**
- `style` - Set to the URL or filename (in resources/assets/css) that you wish to load
- `dependencies` - Set to an array of tag names your style depends on before yours is loaded

**`js`**
- `script` - Set to the URL or filename (in resources/assets/js) that you wish to load
- `dependencies` - Set to an array of tag names your script depends on before yours is loaded

Example config:

```
'frontend' => [
		'enable_ajax' => true,
		'css' => [
			'app' => [
				'style' => 'global/frontend.css',
			],
		],
		'js' => [
			'app' => [
				'script' => 'global/app.js',
				'dependencies' => ['jquery'],
			],
		],
	],
```

## cron.php

This file is where you configure your cron job options and schedules.

The `schedules` Array
------------------

Possible values: Set the name of the schedule to an array containing `interval` in seconds between runs, and `display` for the name to appear in WordPress when viewing the list of cron jobs.

Schedules tell WordPress when to run any cron job attached to them.

!!! note
    The following are scheduled already defined in WordPress, and you can use these instead if you don't need a custom schedule.
    - `hourly`
    - `twicedaily`
    - `daily`
    - `weekly`
    
Example config:

```
    'schedules' => [
		'dragon_every_minute' => [
			'interval' => 60,
			'display' => 'Once Every Minute',
		],
	],
```

The `actions` Array
------------------

Possible values: Set the name of cron job to an array containing a `callback` and `schedule` identifier

This is where you bind the code you wish to run for your cron job, to the [schedule](#the-schedules-array) for when WordPress should run it.

Example config:

```
	'actions' => [
		'dragon_spew_fire' => [
			'callback' => [Cron::class, 'spewFire'],
			'schedule' => 'dragon_every_minute',
		],
	],
```

## database.php

There are new options in this default database configuration file.

The `migrations` String
------------------

Possible values: Name the table that you'd like to create to create

The default value changed to `dragonfw_migrations` to aid in compatability with other plugins that might use the Acorn default of `migrations`.

## errors.php

Error configuration file new in the Dragon Framework.

The `ignore_messages` Array
------------------

Possible values: Any error messages you wish to ignore in testing

Spatie Ignition takes over the screen to display exceptions, warnings, and errors and the like when you're testing your application. However, sometimes third-party plugins won't properly handle error messages, and this prevents Dragon Framework from displaying the code you're working on. To prevent Ignition from blocking you, you can use the message text that Spatie gives you, to set a new item in this array. This setting doesn't alter anything in production, nor does it stop error logging in any environment.

Filtering takes place in the exception handler extended by `App\Exceptions\Handler`.

## hooks.php

Dragon Framework configuration file for WordPress actions, filters, and ajax hooks.

The `admin` Array
------------------

Possible values: An array containing `actions` and `filters` arrays with [valid values](#values-for-actions-and-filters)

This is where you configure all WordPress actions and filters you wish to use in the admin area. `Dragon\Hooks\AdminPluginHooks::init()` adds these hooks at startup. You do not need to call it manually.

The `frontend` Array
------------------

Possible values: An array containing `actions` and `filters` arrays with [valid values](#values-for-actions-and-filters)

This is where you configure all WordPress actions and filters you wish to use on the front end of your website. `Dragon\Hooks\FrontendPluginHooks::init()` adds these hooks at startup. You do not need to call it manually.

The `global` Array
------------------

Possible values: An array containing `actions` and `filters` arrays with [valid values](#values-for-actions-and-filters)

If you wish to run the same actions and hooks on both the admin area as well as the front end, you can configure them one globally here. `Dragon\Hooks\PluginHooksAbstract::init()` adds these hooks at startup. You do not need to call it manually.

Values for `actions` and `filters`
------------------------------------

The previous three sections say to use "valid values" for `actions` and `filter`. Both hold arrays where the key name is the name of the hook, and it holds an array of the following items. (Thus allowing you to add multiple callbacks per hook.)

- `callback` (Required) - The callable to run when the hook is called
- `priority` (Optional - default: 10) - The priority on when your hook should run, with a lower number coming sooner than a higher number
- `args` (Optional - default: 1) - The number of arguments WordPress will be allowed to pass to your callable

Example config:

```
       'actions' => [
			'show_user_profile' => [
				['callback' => [UserProfile::class, 'render']],
				['callback' => [Another::class, 'init']],
			],
			'edit_user_profile' => [
				['callback' => [UserProfile::class, 'render']],
				[
				  'callback' => [Another::class, 'edit'],
				  'args' => 2,
				  'priority' => 20,
				],
			],
		],
```

The `ajax` Array
------------------

Possible values: An array containing a hook name assigned to an array of callbacks

This is where you set your callbacks for ajax requests. They're registered through the service provider, and will load automatically for you. The folowing items may be set in the configuration for each callback configuration.

- `frontend` (Optional - default: false) - If set to `true`, the service provider will register a `wp_ajax_nopriv_xxx` hook callback, so non-privilidged users (logged out) can use the endpoint.
- `callback` (Required) - Set this to a callable that will run on ajax requests for your endpoint.

Example config:

```
	'ajax' => [
		'get_dragon_names' => [
			[
				'frontend' => true,
				'callback' => [DragonAjax::class, 'getNames'],
			],
		],
	],
```

## post_types.php

Dragon Framework configuration file for WordPress custom post types.

The `post_types` Array
------------------

Possible values: An array containing class names for those classes that extend `Dragon\Posts\PostType`

Set the class names for each custom post type you wish to register.

## roles.php

Dragon Framework configuration file for WordPress roles.

The `roles` Array
------------------

Possible values: An array where key names are the names of the roles, and the value is an array as outlined below

This is where you configure any custom WordPress roles. Below is a list of data you'll need to define for each role name. If you'd like to assign any new capabilities to the administrator user, just add only new capabilities to the administrator user. That role is given special treatment, so you don't need to define every administrator capability that already exists.

**New Role Config**
- `name` (Required) - The display name for the user (Ex. Dragon Master)
- `capabilities` (Required) - An array containing all of the capabilities for the new role (custom capabilities welcome)

Example config:

```
	'roles' => [
		'dragon_master' => [
			'name' => 'Dragon Master',
			'capabilities' => [
				'read'				=> true,
				'edit_user'			=> true,
				'authorize_dragons'	=> true,
			],
		],
		'administrator' => [
			'capabilities' => [
				'authorize_dragons' => true,
			],
		],
	],
```

## shortcodes.php

Dragon Framework configuration file for WordPress shortcodes.

The `controllers` Array
-------------------------

Possible values: An array of class names that extend `Dragon\Http\Controllers\ShortcodeController`

Dragon Framework virtualizes routes specified in `routes/web.php` to be used as shortcodes, admin pages, and others. In this case, extend the base controller and list the names of all shortcode controllers you create. The service provider will pick up on this array and make them available for use on the front end.

## taxonomies.php

Dragon Framework configuration file for WordPress custom taxonomies.

The `tax` Array
-------------------------

Possible values: An array of class names that extend `Dragon\Posts\Taxonomy`

List all class names that extend the base class for taxonomies, and they will automatically get added by the service provider.
