# Contributing

Are you looking to get involved in improving the Dragon Framework? Awesome! We'd love to have you. The Dragon Framework is open source, and below are some ways you can contribute, as well as some guidelines.

## Where to Contribute

Here are the main places to contribute.

  - [Dragon Framework](https://github.com/red-scale-dragon/framework) - Contribute your code (Pull Request) to the Dragon Framework repository or report any issues here.
  - [Dragon App](https://github.com/red-scale-dragon/app) - This repository hosts the starter application code and samples. It is also how we run our tests for both the framework and the sample application. Contribute your code or issues for this project on its repository.
  - [Dragon Blank App](https://github.com/red-scale-dragon/blank-app) - This repository hosts an empty Dragon Framework app without any starter code.
  - [Dragon Framework Docs](https://github.com/red-scale-dragon/docs) - Did you add code to the framework and wish to document it? Maybe you found something complicated, and want to create a tutorial to walk someone through the process with ease? We'd love to have you add to the documentation.

## Guidelines and Rules for Developers

Guidelines are items not set in stone, but should be followed when possible. Certain things, such as making sure your code functions, are not guidelines. Your code must pass all its unit tests to be included in our codebase so it doesn't break things for other people.

**Guidelines**

  - KISS - When writing your code, you should keep it as simple as possible. Try to write as little code to make your code as efficient as possible.
  - YAGNI - You Aren't Gonna Need It! Don't write things that no one will use. This is a framework, and that's why this is a guideline, so if you're writing a utility method not used by any code in the codebase, but provides value to people using the framework, that's cool. However, if you write a private method that's not used by anything, or it has one line of code and it's used in one place, that's an example of something that needs refactoring.

**Rules**

  - Your code must work. Write your unit tests in PHPUnit and make sure it passes.
  - Keep to the same structure and code formatting as you see in front of you. (Ex. Directory structure, casing, etc.)
  - SOLID - Follow [Robert C. Martin's](https://en.wikipedia.org/wiki/Robert_C._Martin) Clean Code Principles.
    - **Single Responsibility Principle** - A method should do one thing, and do it well. Make sure each unique thing is in its own method.
    - **Open / Closed Principle** - Code should be open for extension, but closed for modification. That means when you write a class, it shouldn't be marked `final`. Methods should be marked `public` only when outside code needs access, `protected` when only subclasses need access or need to override the code, and `private` when it's just a method used by your class exclusively.
    - **Liskov Substitution Principle** - If S is a subtype of T, then objects of type T may be replaced with objects of type S without altering any of the desirable properties of the program (correctness, etc.). In other words, if `MyControllerRequest extends Request` then `MyControllerRequest` may be passed to a method accepting a `Request` object.
    - **Interface Segregation Principle** - When your code must implement certain methods, but none of those methods do not need a base implementation, then use an interface instead of an abstract class or trait. This prevents having to override all kind of unused methods, such as a file system or database implementation. These would expect certain methods, but there shouldn't be any methods to override.
    - **Dependency Inversion Principle** - Your code should depend on a "type" of something, not the concrete implementation. This keeps your code modular. For example, you're building a method that relies on a database model. That model in turn needs to talk to the database. Your model should depend on an interface that defines how to talk to the database, and your method can pass the selected database driver to the model. A bit contrived, but you get the point.

## Guidelines and Rules for Documentors

**Guidelines**

  - Follow proper grammar. If you're a non-native speaker of the language you're writing in, then just do the best you can and someone will polish it up for you. However, this does take an extra effort to rewrite things, so the more you can do on your own, the better. (Ex. ask ChatGPT to proofread your text)

**Rules**

  - We follow the [Diataxis](https://diataxis.fr/) principles for our documentation. This helps keep things clean. For instance, the text on this page is considered an "Explanation", not a "How To" or the like like. Please read the documentation from Diataxis and make sure your documentation matches the format.
  - ReadTheDocs will parse our documentation using `mkdocs`, and therefore, all documentation must pass the compiler to become part of the documentation on our website. This is just simple Markdown in a specific directory structure. That'll create the menus and provide quality documentation. Please review both the [ReadTheDocs](https://docs.readthedocs.io/) and [MkDocs](https://www.mkdocs.org/) documentation to make sure your documentation will pass.
