# Admin Table and Details Pages

You've created a list of items in the database, namely dragon names and colors, but you need to create a page to edit those. Not only that, but you want to see all the data in the db, page the data, search and filter it, and... well, you get the picture. Pretty complicated, isn't it?

Dragon Framework comes with controllers and views to handle the heavy lifting for you. The table page relies on [dataTables](https://datatables.net/) to handle the tabler operations, and Dragon Framework takes care of the details page portion.

Let's start setting things up. Shall we?

## First the Migration and Model

Let's create a migration and a model. First generate the migration.

!!! note
    Your database migration files are automatically migrated on plugin activation. You can use the Dragon App admin settings to tell the app to remove migrations on deactivation. Generate new migration files by running `wp acorn make:migration`. (You must have WP Cli installed and your plugin active in order to run this command!)

Your generated migration file should contain the following data.

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    /**
     * Run the migrations.
     */
    public function up(): void
    {
    	Schema::create('dragon_tests', function (Blueprint $table) {
    		$table->id();
    		$table->string('name');
    		$table->string('color');
    		$table->timestamps();
    	});
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
    	Schema::drop('dragon_tests');
    }
};
```

Now let's create a model for it at `/app/Models/DragonTest.php`, and add the following content.

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class DragonTest extends Model {
	protected $fillable = [
		'name',
		'color',
	];
}
```

## Create a Table to Display Database Data

Create a file at `/app/Http/Controllers/Admin/Table/DragonDataTable.php` and paste the following inside.

```
<?php

namespace App\Http\Controllers\Admin\Table;

use App\Models\DragonTest;
use Dragon\Http\Controllers\Admin\Table;

class DragonDataTable extends Table {
	protected static string $pageTitle = "Dragons";
	protected static string $menuText = "Dragons";
	protected static string $capability = "manage_options";
	protected static string $routeName = "admin-dragons";
	protected static string $slug = "admin-dragons";
	protected static string $parentSlug = "settings";
	
	protected bool $canCreate = true;
	protected bool $canRead = true;
	protected bool $canUpdate = true;
	protected bool $canDelete = true;
	
	protected string $detailsSlug = "admin-dragon-details";
	
	protected array $headers = [
		'name'			=> 'Name',
		'color'			=> 'Color',
		'created_at'	=> 'Creation Date',
	];
	
	protected function deleteRow(int $id) {
		DragonTest::where('id', $id)->delete();
	}
	
	protected function getRows() {
		return DragonTest::orderBy('created_at', 'DESC')->get();
	}
}
```

Notice that `getRows()` and `deleteRow()` use a model to work with the data? That's the model we created in the last step.

We'll wait until after the next step to add it to the admin menu so we can do it at the same time.

## Create the Details Page

Our table expects there to be a details page to handle any CRUD operations. It's the one specified with the `$detailsSlug` property above.

Create a file at `/app/Http/Controllers/Admin/Table/DragonDataDetails.php` and add the following contents.

```
<?php

namespace App\Http\Controllers\Admin\Table;

use App\Http\Requests\Admin\DragonDataDetailsRequest;
use Dragon\Http\Form\Textbox;
use Illuminate\Http\Request;
use App\Models\DragonTest;
use Dragon\Http\Controllers\Admin\Details;

class DragonDataDetails extends Details {
	protected static string $pageTitle = "Dragon Details";
	protected static string $menuText = "Dragon Details";
	protected static string $capability = "manage_options";
	protected static string $routeName = "admin-dragon-details";
	protected static string $slug = "admin-dragon-details";
	protected static string $parentSlug = "admin-dragons";
	protected static bool $readOnly = false;
	
	protected string $modelName = DragonTest::class;
	
	protected array $encryptedFields = [
		//
	];
	
	public function store(DragonDataDetailsRequest $request) {
		return $this->save($request);
	}
	
	protected function getFields(Request $request) {
		return [
			Textbox::make('name')
				->value($this->getValue($request->get('id'), 'name'))
				->label('What\'s the dragon\'s name?')
				->required(),
			Textbox::make('color')
				->value($this->getValue($request->get('id'), 'color'))
				->label('What color is the dragon?')
				->required(),
		];
	}
}

```

Now we have everything ready to add it to the admin menu. Open `/config/admin_menu.php` and add the following data. (For simplicity, we've left the SettingsController in place as the top level menu, but you could reconfigure your Table to be the top level menu if you'd rather it that way.

```
<?php

use App\Http\Controllers\Admin\SettingsController;
use App\Http\Controllers\Admin\Table\DragonDataTable;
use App\Http\Controllers\Admin\Table\DragonDataDetails;

return [
	'menu' => [
		SettingsController::class,
	],
	'submenu' => [
		DragonDataTable::class,
	],
	'hidden_menu' => [
		DragonDataDetails::class,
	],
];
```

Now check it out in WP admin! You should see a menu item named "Dragons" under your default Dragon App top level menu.
