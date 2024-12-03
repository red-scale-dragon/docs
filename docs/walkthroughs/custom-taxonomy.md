# Create a Custom Taxonomy

In WordPress, a taxonomy is a way to categorize and group content (posts, pages, custom post types) based on shared characteristics or relationships. Taxonomies provide a mechanism for organizing and structuring content, making it easier for users to navigate and find related information on your website.

## Creating Your Taxonomy

There are only two pieces of information you need to create a taxonomy, a tag name, and a display name.

Create a file at `/app/Taxonomy/MyTaxonomy.php` and add the following content.

```
<?php

namespace App\Taxonomy;

use Dragon\Posts\Taxonomy;

class MyTaxonomy extends Taxonomy {
	protected static string $tag = "my-tax";
	protected static string $name = "My Tax";
}

```

No, you're not paying your taxes with this class, but instead categorizing... posts? Yep. Taxonomies categorize posts in various ways. (WordPress Categories are a type of taxonomy, for instance.)

!!! tip
    You can customize them even more by overriding the `init()` method or by setting the `$options` property, but for now, let's keep things simple.

**Making the taxonomy show in wp-admin**

Let's add our taxonomy to the `config/taxonomies.php` file. Paste this sample code into the file, and then view the admin area to see your new taxonomy.

```
<?php

use App\Taxonomy\MyTaxonomy;

return [
	'tax' => [
		MyTaxonomy::class,
	],
];

```
