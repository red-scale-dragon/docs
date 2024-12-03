# Creating API Endpoints

Let's face it. WordPress likes to make things overly complicated. That's why we need frameworks to do the heavy lifting of unmuddling their muddling. (Is that even a word?) AmIRight? Well, not exactly.

It's rather simple to add WordPress API endpoints, as all you do is add them with the `rest_api_init` action, which can be done through the Dragon Framework anyway. So, instead of reinventing the wheel, Dragon Framework reinvented the wheel!

Err... Wait. What?

That's right, sonny Jim! Now you have two bloody APIs to choose from! :D

Yes, you can now use both the WP native API if you'd like to add endpoints meant for other applications to use (Zapier, other plugins, etc.), or you can use the Laravel API to provide an API you can keep relatively black boxed for your own use.

## Creating a WordPress Native API Endpoint

I won't go into too much detail here, as WordPress already gives you a full article on [creating WordPress endpoints](https://developer.wordpress.org/rest-api/extending-the-rest-api/adding-custom-endpoints/), but here's how to do it in Dragon Framework.

Create a file at `/app/WordPressApi.php` and paste the following code inside.

```
<?php

namespace App;

use Dragon\Core\Config;

class WordPressApi {
	public static function register() {
		register_rest_route(Config::prefix() . '/v1', '/author/(?P<id>\d+)', [
			'methods' => 'GET',
			'callback' => [static::class, 'getAuthor'],
		]);
	}
	
	public static function getAuthor(\WP_REST_Request $request) {
		return new \WP_User($request->get_param('id'));
	}
}
```

Now register it as a hook in `/config/hooks.php` by adding the following to the `global` array for simplicity. This will make it available in the admin and front end areas.

```
	'global' => [
		'actions' => [
			'rest_api_init' => [
				['callback' => [\App\WordPressApi::class, 'register']],
			],
		],
		'filters' => [
			//
		],
	],
```

Now you can use it at a url like http://example.com/wp-json/dragonfw_[PLUGIN_DIR]/v1/author/[ID].

## Creating a Laravel API Endpoint

Want to keep your API private, but not to the point of having to use Ajax for everything? Laravel's API capabilities exist in Dragon Framework to do just that.

Create a file at `/app/Http/Controllers/Api/MyApiController.php` and paste the following code inside.

```
<?php

namespace App\Http\Controllers\Api;

use Illuminate\Routing\Controller;
use Illuminate\Http\Request;

class MyApiController extends Controller {
	public function show(Request $request) {
		return [
			'Black Drack',
			'Red Scale',
		];
	}
}
```

Now we need to route the call. So, open `/routes/api.php` and paste the following inside.

```
<?php

use Illuminate\Support\Facades\Route;
use Dragon\Core\Config;
use App\Http\Controllers\Api\MyApiController;

Route::middleware('api')->prefix(Config::prefix())->group(function () {
	Route::controller(MyApiController::class)->name('api-names')->group(function () {
		Route::get('names', 'show');
	});
});

```

Now you can access it at http://example.com/api/dragonfw_[PLUGIN_DIR]/names.
