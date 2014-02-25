# max-generator
minimalistic, modular angular.js app generator

### Motivation

`mgen` is aimed at AngularJS only, so there's no need to compromise for generality, and it makes some choices for you:

* Your app will be composed of discrete CommonJS modules you install with [component.io](https://github.com/component) or that you scaffold using mgen

* Your app gets concatenated and served altogether from a single pair of .js and .css files

* You want to reuse as much code possible from others, share all you can code with others, and write the least amount of code possible to make something work

It's the rough equivalent to rails generators (far less sophisticated yet, but we'll get there eventually) that uses a package manager (component) similar to npm (in some ways), and lets you use a synchronous require call (thus no more ugly AMD/UMD definitions and such).

## Installation 
As a regular node cli tool, you can install this with `npm --global install mgen`. It requires `component` as a `peerDep`.

## Getting started

Let's make a sample app here:

```
$ mgen sampleApp
$ cd sampleApp
$ component install
```

Now you can start creating modules and after that, resources, like this:

```
$ mgen module max/login
$ mgen module --owner=max navbar comments
```

The `--owner` option is a shortcut so you don't have to add the prefix to every module when creating more than one.

And you already have 3 modules created for you. Scaffolding resources is just as easy:

```
$ mgen login script config
$ mgen login script routes
$ mgen login controller login logout
$ mgen login view login-form register-form

$ mgen navbar controller main
$ mgen navbar view navbar

$ mgen comments script config
$ mgen comments script routes
$ mgen comments controller list edit
$ mgen comments view list edit
$ mgen comments filter search
$ mgen comments provider comments
```

This will generate the following structure:

```
sampleApp
|-- component.json
|-- components
|-- app
   |-- index.html
   |-- comments
      |-- controllers
         |-- list.js
         |-- edit.js
      |-- views
         |-- list.html
         |-- edit.html
      |-- filters
         |-- search.js
      |-- provider
         |-- comments.js
      |-- component.json
      |-- index.js
      |-- config.js
      |-- routes.js
   |-- login
      |-- controllers
         |-- login.js
      |-- views
         |-- login-form.html
      |-- component.json
      |-- index.js
      |-- config.js
      |-- routes.js
   |-- navbar
      |-- controllers
         |-- navbar.js
      |-- views
         |-- navbar.html
      |-- component.json
      |-- index.js

```

Where each of the modules you have in your application (under the app branch in that tree) are perfectly reusable and shareable CommonJS, `component` compatible modules. Something as easy as 

```
$ cd app/comments
$ git init .
$ git remote add origin https://github.com/<your-username>/<repo-name>.git
$ git commit -am "Woo lets share!"
$ git push
```

(Providing your github repo exists) Will get you a module you can install directly with a simple

```
component install <your-username>/<repo-name>
```

in any other `component` project you have and since the repo is public, anyone can use it too! Isn't that neat?

Ok, proceeding. now that you have all this stuff, you can just build it doing

```
component build
```

If you get an error like this one:
> error : ENOENT, open '/Users/jhulick/repos/mgen/test/test_7/modules/contact/views/form.js'

that means you haven't compiled the html templates into javascript. You can do that by running

```
mgen html2js
```

Now a regular `component build` should do it's work in a couple tens of milliseconds and the app is ready to be served by your webserver of choice, or just locally by running `mgen server`.

Notice there's a lot of functionality bundled with `mgen`, thou it's not really tied to it. Read more about this in the [Plugins](#plugins) section.

## Usage
If you find yourself in trouble, running `mgen --help` is always useful.

```
○ mgen --help
log:  It worked if it ends with OK
log:  Usage:
log:
log:    mgen <app-name> [options]
log:
log:      Create a folder <app-name> and start a new application inside.
log:      If a <path> is specified then it will start it in such path
log:      and the application name will be the name of the last directory.
log:
log:
log:    mgen [options] module <module-name>
log:
log:      Scaffold a new module named <module-name>.
log:
log:
log:    mgen <module-name> [options] [generator] [params [, more params]]
log:
log:      It will use one of the following generators within the specified
log:      module named <module-name>.
log:
log:
log:  Generators:
log:      * controller
log:
log:  Plugins: (generators are plugins, too)
log:      * controller
log:      * module
log:      * server
log:
log:  For additional help on any plugin, use the --help flag
log:  like this: mgen <plugin-name> --help
log:
log:  OK

```

Regularly you would use this as described in the [Getting Started](#getting-started) section.

## Plugins
The plugin system is very simple, it looks for binaries named `mgen-` and allows you to run them thru `mgen <name>`. Some examples of this are:

* `mgen-server` – callable as `mgen server`.
* `mgen-scaffolder` – the default scaffolder.
* `mgen-module` – the module generator

As you can see, there is also a `mgen-controller` binary. This is because  I want `mgen` to be easily extendable and customizable. Any generator will override the default `scaffolder`. You can still access the `scaffolder` as `mgen scaffolder <template> [params]`. This way you can specify your very own `mgen controller` behavior but fallback to the original `scaffolder` if need be.

The current bundled plugins are listed in the `./bin` folder in this very repo. Eventually, if necessary, they will be taken out of `mgen` and required as `peerDeps` or simply external modules.

