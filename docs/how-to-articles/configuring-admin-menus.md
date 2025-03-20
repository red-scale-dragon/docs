# Configuring Admin Menus

To configure admin menus, you'll need to create a controller that extends `Dragon\Http\Controllers\AdminPageController`, and add it to your `config/admin_menu.php` configuration file. Once that's done, follow the information on this page to properly configure your controller to do what you'd like.

## Menu Data

The first thing you'll want to do is choose your menu text, slug, icon, and other specificities. Take a look at these properties copied from the admin `SettingsController`.

```
	protected static string $pageTitle = "Admin Settings";
	protected static string $menuText = "Dragon Settings";
	protected static string $capability = "manage_options";
	protected static string $routeName = "admin-settings";
	protected static string $slug = "settings";
```

By default, your admin settings page is installed as a top level menu. So, lets unpack what we can use for our top level menu display.

**Properties for all menus**

- `$pageTitle` - This is the title for your page, not your menu. If you choose to use that on your page view, the parent controller (`Dragon\Http\Controllers\AdminPageController`) will give you access to it as `{{ $title }}` if you get your page data through `$this->makePageData()` on your controller.
- `$menuText` - This is the text that will display for your menu item in the sidebar. Be sure to choose an appropriate name.
- `$capability` - Select a capability that should be allowed to access your menu item. (Ex. `manage_options` or `read`) You usually can select a role here as well if you'd prefer, but that limits things to that role only.
- `$routeName` - Dragon Framework virtualizes routes so you can use them as admin pages. You'll need to configure your route in `routes/web.php`. Be sure to check out the examples in there already. Set the name (`name()` in the `routes/web.php` file) of the route here.
- `$slug` - This is the slug you'd like to set for your admin page on WordPress. Choose anything. It's namespaced for you so that it won't conflict with other plugins. If you must not namespace your page, set the `$shouldNamespace` property to `false` for your controller.

Phew! That's a lot of stuff, but what about icons?

```
protected static string $icon = "dashicons-admin-home";
```

Whatever you set here is passed to `add_menu_page()`, so feel free to use dashicons from WordPress, a URL, or a relative path to your icon file.

Need to change the position of where your menu item is in the stack of menus?

```
protected static ?int $menuPosition = null;
```

That also gets passed directly to `add_menu_page()`, so feel free to choose an appropriate value.

## Sub Menus

There are two types of submenus, and with the exception of where your controller is placed in `config/admin_menu.php` and not having an icon like a top level menu, there is one other property you'll want to set on your controller.

```
protected static string $parentSlug = "";
```

Set the slug name here, that you set on your parent controller. So, if you're looking to have your submenu under `settings`, then put `settings` here as the parent slug.

## What about hidden menus?

Yes, this is the second type of submenu. A hidden menu doesn't appear on the sidebar, and is just there for you to link to from your other admin pages. For this type of menu, you do NOT set a `$parentSlug`. Just configure it like a top level menu (no icon), but list the controller under `hidden_menu` on `config/admin_menu.php`. It'll get added as a submenu, but without a parent, and thus it'll be a hidden page.

## Page-Specific Assets

Page-specific assets prevent unwanted code loading on every single page in your admin area, and so they're configured on the controller using the following two properties.

```
protected static array $scripts = [];
protected static array $styles = [];
```

As you probably guessed, `$scripts` holds your JS code, and `$styles` holds your CSS code. The following example shows the correct structure for these two properties.

```
    protected static array $scripts = [
		'example' => [
			'script' => 'https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.3/js/bootstrap.min.js',
		],
	];
	
	protected static array $styles = [
		'example' => [
			'style' => 'bootstrap.min.css',
		],
	];
```

Both can handle URLs or files locally hosted. `example` is the tag name for the scripts and styles listed above, but they can both have different tag names. Also, take note of the fact that you can add multiple files, each with different tag names.

If your JS or CSS depends on other CSS or JS files, be sure to add a `dependencies` key as a sibling of `script`/`style` and set it to an array of tag names you wish to use as dependencies.

Also, if you need your JS to load in the footer, add an `in_footer` key as a sibling of `script` and set that to `true`.
