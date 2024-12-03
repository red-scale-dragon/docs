# Creating Custom User Profile Fields

Want to add admin notes to a user profile? Maybe you'd like them to set the name of their dragon, or perhaps its color? Look no further! Now you can follow the instructions on this page and out pops a shiney new option or two for the wp-admin profile for that user.

## Creating the Handler

Create a file at `/app/UserProfile.php` and paste the following inside.

```
<?php

namespace App;

use Dragon\Support\User;

class UserProfile {
	public static function render(\WP_User $user) {
		$data = [
			'name_of_dragon' => User::getMeta('name_of_dragon', null, $user->ID),
		];
		
		if (User::isAdmin()) {
			$data['admin_notes'] = User::getMeta('admin_notes', null, $user->ID);
		}
		
		echo view('admin.profile', $data);
	}
	
	public static function update(int $userId) {
		if (empty(request('_wpnonce')) || !wp_verify_nonce(request('_wpnonce'), 'update-user_' . $userId)) {
			return;
		}
		
		if (!current_user_can('edit_user', $userId)) {
			return;
		}
		
		User::setMeta('name_of_dragon', request('name_of_dragon'), $userId);
		
		if (User::isAdmin()) {
			User::setMeta('admin_notes', request('admin_notes'), $userId);
		}
	}
}
```

The `render()` method says we need a view at `admin.profile`. Create a file at `/resources/views/admin/profile.blade.php` and paste the following inside.

```
<div id="dragon-profile" class="card card-full-width">
	<h3>Extra profile information</h3>

    <table class="form-table">
    <tr>
        <th><label for="name_of_dragon">Name of Dragon</label></th>
        <td>
            <input type="text" name="name_of_dragon" id="name_of_dragon" value="{{ $name_of_dragon }}" class="regular-text" /><br />
            <span class="description">Name your dragon.</span>
        </td>
    </tr>
        @admin
        <tr>
        	<th><label for="admin_notes">Admin Notes</label></th>
        	<td>
        		<textarea name="admin_notes" id="admin_notes" class="regular-text">{{ $admin_notes }}</textarea>
	        </td>
	    </tr>
        @endadmin
    </table>
</div>
```

Now we need to hook up the plumbing and get it running. Open `/config/hooks.php` and add the following to the `admin` array.

```
		'actions' => [
			'show_user_profile' => [
				['callback' => [\App\UserProfile::class, 'render']],
			],
			'edit_user_profile' => [
				['callback' => [\App\UserProfile::class, 'render']],
			],
			'personal_options_update' => [
				['callback' => [\App\UserProfile::class, 'update']],
			],
			'edit_user_profile_update' => [
				['callback' => [\App\UserProfile::class, 'update']],
			],
		],
```

Now visit your own profile (or someone elses) on WP admin. Scroll to the bottom to see your new options.
