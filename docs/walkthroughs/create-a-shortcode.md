# Create a Shortcode

We're going to create a shortcode you can use on the front end of your website. Below are the steps required to get your shortcode functional.

1. Create the class that will handle the shortcode logic.
2. List the class in the configuration file to have it picked up by Dragon Framework.
3. Write the route definition that Dragon Framework relies on to virtualize your request.
4. Add a view.
5. Add some shortcode-specific assets.

## Create the Shortcode Class

A good place to place your new controller is at `app/Http/Controllers/Shortcode`, so create a file named `DragonNamesShortcode.php` in that directory. Open the file and paste the following code inside.

```
<?php

namespace App\Http\Controllers\Shortcode;

use Illuminate\Http\Request;
use Dragon\Http\Controllers\ShortcodeController;

class DragonNamesShortcode extends ShortcodeController {
	protected static string $shortcodeTag = "dragon_names";
	protected static string $routeName = "dragon-names-shortcode";
	
	public function show(Request $request) {
        return view('shortcode.dragon-names', [
        	'names' => ["Black Draak", "Red Scale", "Dragon Cloud", "Frank"],
        ]);
    }
}

```

The code above is what will respond when your shortcode runs on a given page.

!!! important
    Notice that like all shortcodes, your new `DragonNamesShortcode` is a subclass of `Dragon\Http\Controllers\ShortcodeController`. The parent class handles the route virtualization for you. So no need to manually do that on your own.

## List the Class in the Config File

Right now your shortcode class doesn't do anything. We need to handle a few things. Let's add your shortcode to the configuration file. Open `config/shortcodes.php` and add your controller's class name to the `controllers` array. The code below shows where the name goes.

```
<?php

use App\Http\Controllers\Shortcode\DragonNamesShortcode;

return [
	'controllers' => [
		DragonNamesShortcode::class,
	],
];

```

## Configure Your Virtual Route

Behind the scenes, Dragon Framework handles all route virtualization for you. However, it does need you to define the route that you wish to virtualize.

Open `routes/web.php` and add the following content.

```
<?php

use Illuminate\Support\Facades\Route;
use Dragon\Core\Config;
use App\Http\Controllers\Shortcode\DragonNamesShortcode;

Route::middleware(['web'])->prefix(Config::prefix())->group(function () {
	Route::controller(DragonNamesShortcode::class)->name('dragon-names-shortcode')->group(function () {
		Route::get('dragon-names-shortcode', 'show');
	});
});

```

That code loads the necessary `web` group of middleware from Acorn, attaches the prefix for your shortcode tag, names your `DragonNamesShortcode` as the controller on which to send requests, names the route group `dragon-names-shortcode`, and says that all `GET` requests to `dragon-names-shortcode` should go to the `DragonNamesShortcode::show()` method.

!!! important
    Notice that `name('dragon-names-shortcode')` matches the name of the route in your controller's `$routeName` property. Those must match to find your route.

## Add a View

To make your new `dragonfw_dragonapp_dragon_names` shortcode functional, you'll need to create a view for it to load. The controller says that view is `shortcode.dragon-names`, so add the file at `resources/views/shortcode/dragon-names.blade.php` and paste the following code inside.

```
<div id="dragon-test">
	<ul>
	@foreach($names as $name)
		<li>{{ $name }}</li>
	@endforeach
	</ul>
</div>
```

Now it's fully functional and ready for use, but if you happen to need shortcode-specific assets, such as Bootstrap, custom JS code, etc, keep reading.

## Shortcode-Specific Assets

Right now you have a shortcode that works, but you might need to load JS or CSS assets for your shortcode. This requires a small modification to your shortcode controller. So, open that, and paste the following code.

```
<?php

namespace App\Http\Controllers\Shortcode;

use Illuminate\Http\Request;
use Dragon\Http\Controllers\ShortcodeController;

class DragonNamesShortcode extends ShortcodeController {
	protected static string $shortcodeTag = "dragon_names";
	protected static string $routeName = "dragon-names-shortcode";
	
	protected static array $scripts = [
		'dragon-example' => [
			'script' => 'https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.3/js/bootstrap.min.js',
		],
	];
	
	protected static array $styles = [
		'mycss-example' => [
			'style' => 'bootstrap.min.css',
		],
	];
	
	public function show(Request $request) {
		static::loadPageAssets();
		
        return view('shortcode.dragon-names', [
        	'names' => ["Black Draak", "Red Scale", "Dragon Cloud", "Frank"],
        ]);
    }
}

```

The `$scripts` property holds your JS files or URLs, and the `$styles` property holds your CSS files or URLs. In `show()`, we also need to call `static::loadPageAssets()` to queue them for the page. Want to learn more? Check out the How To article for [adding page assets](../../how-to-articles/configuring-admin-menus#page-specific-assets). It talks about adding them to admin pages, but it's the same for shortcodes.
