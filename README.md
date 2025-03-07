[![](https://badges.gitter.im/emacs-ng/emacs-ng.svg)](https://gitter.im/emacsng/community)
[![](https://github.com/emacs-ng/emacs-ng/workflows/CI/badge.svg)](https://github.com/emacs-ng/emacs-ng/actions?query=workflow%3ACI)
[![](https://img.shields.io/reddit/subreddit-subscribers/emacsng?label=Join%20r%2Femacsng&style=social)](https://www.reddit.com/r/emacsng/)

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [emacs-ng](#emacs-ng)
    - [Motivation](#motivation)
- [Why Emacsng](#why-emacsng)
    - [New User Guides](#new-user-guides)
    - [Why JavaScript](#why-javascript)
        - [Performance](#performance)
- [Getting Started](#getting-started)
    - [Requirements](#requirements)
    - [Building emacsng](#building-emacsng)
    - [Running emacsng](#running-emacsng)
- [Features](#features)
    - [Javascript](#javascript)
        - [Typescript](#typescript)
        - [Interacting with buffers and symbols](#interacting-with-buffers-and-symbols)
        - [WebWorkers and Parallel Scripting](#webworkers-and-parallel-scripting)
        - [Using Async I/O](#using-async-io)
        - [Adding Hooks](#adding-hooks)

<!-- markdown-toc end -->

# emacs-ng

## Motivation

The goal of this fork is to explore new development approaches.

# Why Emacsng

Emacs-ng is an additive native layer over emacs, bringing features like Deno's Javascript and Async I/O environment, Mozilla's Webrender (experimental opt-in feature), and other features in development. emacs-ng's approach is to utilize multiple new development approaches and tools to bring Emacs to the next level. emacs-ng is maintained by a team that loves Emacs and everything it stands for - being totally introspectable, with a fully customizable and free development environment. We want Emacs to be a editor 40+ years from now that has the flexibility and design to keep up with progressive technology.

## New User Guides

If you would like to see a breakdown of what JavaScript can do, feel free to jump into our User Guides: [Getting Started](https://github.com/emacs-ng/emacs-ng/blob/master/getting-started.md), [Using Deno](https://github.com/emacs-ng/emacs-ng/blob/master/using-deno.md), and [Advanced Features](https://github.com/emacs-ng/emacs-ng/blob/master/adv-features.md). Otherwise, you can continue reading for a project outline and build instructions.

## Why JavaScript

One of emacs-ng's primary features is integrating the [Deno Runtime](https://deno.land/), which allows execution of JavaScript and Typescript within Emacs. The details of that feature are listed below, however many users would ask themselves **WHY JAVASCRIPT?** JavaScript is an extremely dynamic language that allows for a user to inspect and control their scripting environment. The key to note is that bringing in Deno isn't JUST JavaScript - it's an ecosystem of powerful tools and approaches that Emacs just doesn't have currently.

* TypeScript offers an extremely flexible typing system, that allows to user to have compile time control of their scripting, with the flexibility of types "getting out of the way" when not needed.
* Deno uses Google's v8 JavaScript engine, which features an extremely powerful JIT and world-class garbage collector.
* Usage of modern Async I/O utilizing Rust's Tokio library.
* Emacs-ng has WebWorker support, meaning that multiple JavaScript engines can be running in parallel within the editor. The only restriction is that only the 'main' JS Engine can directly call lisp functions.
* Emacs-ng also has WebAssembly support - compile your C module as WebAsm and distribute it to the world. Don't worry about packaging shared libraries or changing module interfaces, everything can be handled and customized by you the user, at the scripting layer. No need to be dependent on native implementation details.

### Performance

v8's world-class JIT offers the potential for massive performance gains. For a simple benchmark (fibonacci), using the following implementations:

``` lisp
(defun fibonacci(n)
  (if (<= n 1)
      n
    (+ (fibonacci (- n 1)) (fibonacci (- n 2)))))
```

``` js
const fib = (n) => {
    if (n <= 1) {
        return n;
    }

    return fib(n - 1) + fib(n - 2);
};
```

emacs-ng's JS implementation clocks in over **50 times** faster than emacs 28 without native-comp for calculating fib(40). With native-comp at level 3, JS clocks in over **15** times faster. This, along with Async I/O from Deno, WebWorkers, and WebAsm, gives you the tools to make Emacs a smoother and faster experience without having to install additional tools to launch as background processes or worry about shared library versions - **full performance with EVERYTHING in the scripting layer**.

# Getting Started

## Requirements

1. You will need
   [Rust installed](https://www.rust-lang.org/en-US/install.html).
   The file `rust-toolchain` indicates the version that gets installed.
   This happens automatically, so don't override the toolchain manually.
   IMPORTANT: Whenever the toolchain updates, you have to reinstall
   rustfmt manually.

2. You will need a C compiler and toolchain. On Linux, you can do
   something like:

        apt install build-essential automake clang libclang-dev

   On macOS, you'll need Xcode.

3. Linux:

        apt install texinfo libjpeg-dev libtiff-dev \
          libgif-dev libxpm-dev libgtk-3-dev gnutls-dev \
          libncurses5-dev libxml2-dev libxt-dev

   macOS:

        brew install gnutls texinfo autoconf

    To use the installed version of `makeinfo` instead of the built-in
    (`/usr/bin/makeinfo`) one, you'll need to make sure `/usr/local/opt/texinfo/bin`
    is before `/usr/bin` in `PATH`.
    Mojave install libxml2 headers with: `open /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg`


If you want to run doomemacs, you will need to compile with `./configure --with-nativecomp`. nativecomp will also require `zlib1g-dev libgccjit-8-dev`

## Building emacsng

```
$ ./autogen.sh
$ ./configure --enable-rust-debug
$ make -j 8 # or your number of cores
```

For a release build, don't pass `--enable-rust-debug`.

The Makefile obeys cargo's RUSTFLAGS variable and additional options
can be passed to cargo with CARGO_FLAGS.

For example:

``` bash
$ make CARGO_FLAGS="-vv" RUSTFLAGS="-Zunstable-options --cfg MARKER_DEBUG"
```

## Running emacsng

You can now run your build:

```bash
./src/emacs
```

If you want to install it, just use

```bash
make install
```

You may need to run sudo make install depending on your system configuration.

## Contributing

Contributions are welcome. We try to maintain a list of "new contributor" friendly issues tagged with "good first issue".

# Features

## Javascript
This code is a strictly additive layer, it changes no elisp functionality, and should be able to merge upstream patches cleanly. JS tests can be run by building the editor and executing `cd test && ../src/emacs --batch -l js/bootstrap.el`.

To learn more about JavaScript, it is recommended you check out [Getting Started](https://github.com/emacs-ng/emacs-ng/blob/master/getting-started.md), [Using Deno](https://github.com/emacs-ng/emacs-ng/blob/master/using-deno.md), and [Advanced Features](https://github.com/emacs-ng/emacs-ng/blob/master/adv-features.md)

### Typescript
emacs-ng supports native typescript. If your typescript fails to compile, those functions will throw elisp errors that you can address within your program.

The high level concept here is to allow full control of emacs-ng via the javascript layer. In order to avoid conflict between the elisp and javascript layers, only one scripting engine will be running at a time. elisp is the "authoritative" layer, in that the javascript will invoke elisp functions to interact with the editor. This is done via a special object in javascript called `lisp`. An example of it's usage would be:

### Interacting with buffers and symbols

``` js
let buffer = lisp.get_buffer_create("mybuf");
let qcname = lisp.keywords.name; // Will return an object representing :name
let myList = lisp.list(qcname, 3);
console.log(myList); // prints { nativeProxy : true }
console.log(myList.json()); // prints { name: 3 }, defaulting to the assumption this list is a plist
```

*Almost* any function callable from lisp can be invoked in JavaScript via the lisp object. So `(function-name)` becomes `lisp.function_name`. If you find a lisp function that cannot be called from JS, that is a bug.

### Using Async I/O

We expose the async IO functionality included with deno. Users can fetch data async from their local file system, or  the network. They can use that data to interact with the editor. An example would be:

``` js
const json = fetch("https://api.github.com/users/denoland")
.then((response) => { return response.json(); });

const txt = Deno.readTextFile("./test.json");

Promise.all([json, text])
    .then((data) => {
        let buffer = lisp.get_buffer_create('hello');
        const current = lisp.current_buffer();
        lisp.set_buffer(buffer);
        lisp.insert(JSON.stringify(data[0]));
        lisp.insert(data[1]);
        console.log(lisp.buffer_string());
        lisp.set_buffer(current);
    });
```
This example assumes you have a json file named `test.json` in your current directory.

### WebWorkers and Parallel Scripting

We also support WebWorkers, meaning that you can run javascript in seperate threads. Note that WebWorkers cannot interact with the lisp VM, however they can use Deno for async I/O. See [Advanced Features](https://github.com/emacs-ng/emacs-ng/blob/master/adv-features.md)

Web Assembly allows you to perform things normally handled by native libraries with easy distribution. Want to manipulate sqlite3? Use the [deno sqlite wasm package](https://deno.land/x/sqlite@v2.3.2/mod.ts)

``` js
import { DB } from "https://deno.land/x/sqlite@v2.3.2/mod.ts";

const db = new DB("test.db");
db.query("CREATE TABLE IF NOT EXISTS people (id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT)");

const name = "David";
db.query("INSERT INTO people (name) VALUES (?)", [name]);
for (const [name] of db.query("SELECT name FROM people")) {
    console.log(name);
}

db.close();
```

### Adding Hooks

Lambdas are converted automatically between the two languages via proxying. You can use  this to set timers, or to add hooks. This example features the lisp.symbols object, which returns a proxy to the quoted key. lisp.setq functions similarly to `(setq ....)`

``` js
lisp.add_hook(lisp.symbols.text_mode_hook, () => {
    lisp.setq(lisp.symbols.foo, "3");
});

// Or set timer with arguments...
lisp.run_with_timer(lisp.symbols.t, 1, (a, b) => {
    console.log('hello ' + a);
    lisp.print(b);
    return 3;
}, 3, lisp.make.alist({x: 3}));
```

The user can also define functions via `defun` that will call back into javascript. Like in normal defun, DOCSTRING and the INTERACTIVE decl are optional. This example features both docstring and interactive

``` js
lisp.defun({
    name: "my-funcntion",
    docString: "This is my docstring",
    interactive: true,
    args: "P\nbbuffer:",
    func: (a, b) => console.log('hello buffer!')
});
```
