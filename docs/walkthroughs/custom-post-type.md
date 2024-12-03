# Create a Custom Post Type

Custom Post Types have all sorts of purposes. Are you trying to make a real estate listing page? Maybe you're looking to create a product for your ecommerce solution? Here's how to make your dreams come true.

## Creating Your Post Type

There are only two pieces of information you need to create a custom post type, a tag name, and a display name.

Create a file at `/app/PostTypes/MyPostType.php` and add the following content.

```
<?php

namespace App\PostTypes;

use Dragon\Posts\PostType;

class MyPostType extends PostType  {
	protected static string $tag = "my-post-type";
	protected static string $name = "My Post Type";
}


```

!!! tip
    You can customize them even more by overriding the `init()` method or by setting the `$options` property, but for now, let's keep things simple.

**Making the post type show in wp-admin**

Let's add our post type to the `config/post_types.php` file. Paste this sample code into the file, and then view the admin area to see your new post type.

```
<?php

use App\PostTypes\MyPostType;

return [
	'post_types' => [
		MyPostType::class,
	],
];


```
