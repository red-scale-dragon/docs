# Blade Directives

Blade directives extend the functionality of the Blade Template Engine. Below is a list of items added to Blade by the Dragon Framework.

`@image()`
----------

`@image(string $filename)`

Displays the URL for the image specified in `$filename` located in the resources/assets/img directory, either in your app or if that doesn't exist, then the one in the framework.

```
<p><img src="@image('dragon.png')" /></p>
```

Example Output:

```
<p><img src="/wp-content/plugins/dragonapp/resources/assets/img/dragon.png" /></p>
```

`@namespaced()`
----------

`@namespaced(string $key)`

Displays the namespaced version of `$key` using the namespace from `config('app.namespace')`.

```
<p>@namespace('test-key')</p>
```

Example Output:

```
<p>dragonfw_dragonapp_test-key</p>
```

`@nonce`
----------

`@nonce`

Displays the WordPress-generated nonce in a hidden form field.

```
<p>@nonce</p>
```

Example Output:

```
<p><input name="dragonfw_dragonapp_nonce" value="d7cccc2187" type="hidden" /></p>
```

`@admin`
----------

`@admin`

Custom `if` statement that displays enclosed content to admin users.

```
@admin
  <p>I'm an admin.</p>
@else
  <p>Nope! Not an admin...</p>
@endadmin
```

Example Output (admin):

```
<p>I'm an admin.</p>
```
