# Actions and Filters

WordPress has loads of actions and filters, and the Dragon Framework has a whole toolkit for setting those up for your plugin.

## Adding an Action or Filter

This is probably the shortest walkthrough you'll find on here because it's so easy.

Create your action or filter in `/app`, or a subdirectory. Let's call our file `/app/MyCallback.php`. We'll add the following contents to that file.

```
<?PHP

namespace App;

class MyCallback {
	public static function myAction() {
		// Do stuff
	}
	
	public static function myFilter(string $filterMe, array $args) {
		// Do stuff
		return $filteredData;
	}
}
```

Now lets hook it up in `config/hooks.php`. We can add it to the `global` array for simplicity. Make that array look like the one in the sample code below.

```
	'global' => [
		'actions' => [
			'an_action_hook_name' => [
				['callback' => [\App\MyCallback::class, 'myAction']],
			],
		],
		'filters' => [
			'a_filter_hook_name' => [
				[
					'callback'	=> [\App\MyCallback::class, 'myFilter'],
					'args'		=> 2,
				],
			],
		],
	],
```

Easy peasy. Want to learn more about the [configuration file settings for hooks](../../api-reference/config-files)?
