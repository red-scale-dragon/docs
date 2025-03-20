# Test Your Plugin

If you don't test your plugin thoroughly, you're only guessing that it works. This article will show you how to test your plugin using the included toolkit.

## Setting Up

All of your tests run on PHPUnit. The base test case extends the `\WP_UnitTestCase` so you have access to both WordPress testing commands, as well as PHPUnit commands. However, due to PHP not having the concept of multiple inheritance, unfortunately that means we are unable to extend the Laravel base test case. Laravel and WordPress do not extend each other's test case either. So, since we're targeting WordPress by building a plugin for it, it makes more sense for the Dragon Framework to extend the WordPress test case instead of the one from Laravel. This means things like `$this->get('/')` will not work in your tests.

Bootstrapping is done on the `tests/bootstrap.php` file, and the `/phpunit.xml` file already tells PHPUnit to load that file for you. So, if you need some special treatment for your tests, you could add to the bootstrap file, but typically that's not necessary.

There are two types of tests, unit tests and feature tests. Unit tests test your methods and functions to make sure they work they way you want, and feature tests test complex workflows, such as running a route and seeing if the response is the way you expect.

## Creating a Test Case

To stay well organized, consider that you'd like to build a unit test for an admin search feature. You could put your test case in `tests/Unit/Admin` and name the file `SearchTest.php`. This file will contain the following code.

```
<?php

namespace Tests\Unit;

use Tests\TestCase;

class SearchTest extends TestCase {
	public function testReturnsTrue() {
		$this->assertTrue(true);
	}
}

```

Notice that we're extending `TestCase` located at `tests/TestCase.php`. That's the Dragon Framework-supplied base test case which extends the WordPress base test case. It's responsible for running your migrations in the database provided in `phpunit.xml`, and masking errors and warnings that spaghetti code WordPress spews out when you use a more modern `phpunit` command line tool.

## Running Unit Tests

To run all the unit tests you have listed as suites in `phpunit.xml`, install PHPUnit 10.5. (Don't use other versions, as that will conflict with WordPress's spaghetti code.) The following text expects you to have installed it to a place listed in your `$PATH` environment variable. (Ex. `/usr/bin/phpunit` is often a good choice.)

On a command line, change the directory to the base directory for your plugin, then run `./tests/bin/install-wp-tests.sh`. (You'll need to install Subversion (svn) first if you haven't.) This should be done once per session, as it saves wordpress and the testing library to `/tmp` on your machine. That will get purged over time or with a reboot, so if you get errors when running the next command, then rerun this installer command.

Any time you want to run your tests, run the `phpunit` command.
