# Quick Start Guide

In this guide, we will...

- Create a Dragon Framework WordPress plugin
- Set a name for our plugin
- Visit the location of the editable code for your plugin, and review what's all there.

## Installation

To get started with Dragon Framework, you have two options. The first option is to clone the applications with some good starter code samples for you to look at and modify. However, if you're a veteran user, you can jump right to the blank app to start without the need to remove sample code from your codebase just to write a new plugin.

- First, open a terminal window, and change the directory to your WordPress installation's `wp-content/plugins` directory.
    - If you'd like the sample code, run `git clone git@github.com:red-scale-dragon/app.git dragonapp`.
    - If you prefer not to have the sample code, you'll want to instead run `git clone git@github.com:red-scale-dragon/app-blank.git dragonapp`.
    - Both of those commands will create a new plugin inside of the `dragonapp` directory. If you prefer to name your plugin something else, you'll want to change the directory name before proceeding to the next step.
- Change the directory to the `dragonapp` directory you just created, and run `composer update` to install all the necessary dependencies.
- From the same directory, run `mv .env.example .env` and then edit the resulting `.env` file to have your application's name. Activate the plugin in WordPress to allow you to run the `wp acorn key:generate` command, which will populate the `APP_KEY` variable in `.env` for you. Please note that the `wp` command will not find your plugin if you do not first activate your new plugin in WordPress before you use it.

!!! important
	If you plan to install two apps next to each other and you use the same version of Dragon Framework, you'll wind up with two identical composer autoloader names, thus not allowing you to activate the second plugin. To avoid this issue, it's best to set the `autoloader-suffix` in [composer.json](https://getcomposer.org/doc/06-config.md#autoloader-suffix) to be something other than an empty string. The composer project [reccomends](https://github.com/composer/composer/blob/71e8395ebdb68d9179fae53e7fa3a303d6e438f3/src/Composer/Autoload/AutoloadGenerator.php#L429) using the output of `bin2hex(random_bytes(16))` for your custom suffix. Once you chaange your suffix, be sure to run `composer upgrade` once more. Please note that you'll also need to decide on a sandboxing strategy, such as Strauss, PHP Scoper, or another way to sandbox your application. This is very involved, and unless there is significant interest in having this built into Dragon Dramework, it won't get added. If we made the framework install as its own plugin, then plugins for different versions of the framework would need backwards compatibility. So, there really isn't an easy solution to this short of PHP making autoloading able to be sandboxed, or WordPress having unified dependencies...

Congratulation! You now have a plugin ready for you to use. Of course your plugin doesn't do what you want until you build it out, but it is valid plugin code running on the Dragon Framework. Now, let's explore the codebase a bit.

!!! tip
    The following parts will assume that you have installed the app that includes the sample code, and that your plugin uses the `dragonapp` directory. If you chose the blank-app, then you might not see all the files mentioned, as those are sample code. Feel free to either clone the app with sample code, or follow along on the [code repository on GitHub](https://github.com/red-scale-dragon/app).

## Codebase Review

Let's explore the contents of `dragonapp`. First, take a look at the directory structure.

- `app` - This is where all of your application code lives.
- `config` - All of your configuration files are in this file. You can read about each of the [configuration files](../api-reference/config-files) on here.
- `database/migrations` - Your database migration files are automatically migrated on plugin activation. You can use the Dragon App admin settings to tell the app to remove migrations on deactivation. Generate new migration files by running `wp acorn make:migration`. (You must have WP Cli installed and your plugin active in order to run this command!)
- `public` - This directory is useful for anything you'd like to make publicly available. (Ex. PDF files)
- `resources/assets` - This contains directories for each type of asset. You're free to organize their contents any way you'd like.
- `resources/lang` - If you'd like to use Laravel's multilingual functionality, you may place language files in here as per the Laravel documentation.
- `resources/views` - All blade views for your app should be in this directory. They must all end with `.blade.php` for the Blade template engine to parse your variables. Organize this directory as you see fit.
- `routes` - There are typically only two files in this directory, `web.php` for your HTTP routes, and `api.php` for your Laravel API routes. (Not WP API)
- `storage` - This directory hosts your compiled views, cache data, session data, logs, and possibly more. Be sure to make your web server hide the contents of this directory. The included `.htaccess` file should stop Apache from displaying directory contents, but you'll need to verify that your server won't expose the data on this directory.
- `tests` - This is where your unit tests live. Learn how to [test your plugin](../how-to-articles/test-your-plugin).
- `vendor` - All 3rd-party code lives in this directory, including the Dragon Framework code, Acorn (by Roots), and Laravel. Never change this directory manually. Just use `composer` commands from your plugin directory.

**Suggested App Directory Structure**

While you could place most of the files in any structure here that you'd like, it's very highly recommended that you follow the typical Laravel layout for this directory, with a few small additions for the Dragon Framework.

- `Exceptions` - This must contain `Handler.php` which is your exceptions handler. It's a stub that extends the Dragon Framework's base exception. You're free to change it as you need, but the Dragon Framework does depend on this file being where it is.
- `Http/Ajax` - Your Ajax callbacks should appear in this directory.
- `Http/Controllers` - Any controllers should be here. `Admin`, `Api`, and `Shortcode` are good names for directories to organize the different parts of your codebase into different directories.
- `Http/Middleware` - Store your middleware here and reference it on your routes.
- `Http/Requests` - If you want to validate forms and the like, you can do that by extending the base `FormRequest` class. Feel free to organize this into subdirectories, then just type hint them on your controllers.
- `Models` - Store all of your database models for Eloquent Orm here. This way you can easily work with the tables you created in your migrations.
- `PostTypes` - If you've created custom post types, this directory is a great place to put them. Reference those in the `config/post_types.php` file for post types.
- `Providers` - Create your service providers in this directory, then load them up in `config/app.php`.
- `Taxonomy` - Create custom taxonomies in this directory, then load them up in `config/taxonomies.php`.

**Files Included for You**

There are a number of useful files included in the application starting code. Let's look at the list of the more important files.

- `App\Http\Controllers\Admin\AdminLogController` - Your application has a log file. It includes anything logged through the Laravel logger, and so your app comes with an admin page to display the log file for you.
- `App\Http\Controllers\Admin\SettingsController`- Pretty much all plugins need settings of some description. This controller provides a convenient way to add admin settings without having to design your own.
- `.env` - This is the Laravel environment file. Laravel expects it to be there. On first activation, your plugin will automatically generate the key for you. Feel free to set your application name as you'd like.
- `loader.php` - This is the file WordPress will use to load your plugin. Configure the meta data at the top to match your needs.
- `phpunit.xml` - This defines what tests will run when you run `phpunit`, and provides various settings it needs to run your tests. Feel free to change it to your needs.
