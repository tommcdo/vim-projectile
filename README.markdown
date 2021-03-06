# projectile.vim

Projectile is a general purpose plugin based on "projections" from
[rails.vim][].  What are projections?  Let's start with an example.

## Example

A while back I went and made a bunch of plugins for working with [rbenv][].
Here's what a couple of them look like:

    ~/.rbenv/plugins $ tree
    .
    ├── rbenv-ctags
    │   ├── bin
    │   │   └── rbenv-ctags
    │   └── etc
    │       └── rbenv.d
    │           └── install
    │               └── ctags.bash
    └── rbenv-sentience
        └── etc
            └── rbenv.d
                └── install
                    └── sentience.bash

As you can see, rbenv plugins have hooks in `etc/rbenv.d/` and commands in
`bin/` matching `rbenv-*`.  Here's a projectile configuration for that setup:

    let g:projectiles = {
          \   "etc/rbenv.d/|bin/rbenv-*": {
          \     "bin/rbenv-*": {
          \        "command": "command",
          \        "template": ["#!/usr/bin/env bash"],
          \     },
          \     "etc/rbenv.d/*.bash": {"command": "hook"}
          \   }
          \ }

The key in the outermost dictionary says to activate for any directory
containing a subdirectory `etc/rbenv.d/` *or* files matching `bin/rbenv-*`.
The corresponding value contains projection definitions.  Here, two
projections are defined.  The first creates an `:Ecommand` navigation command
and provides boilerplate to pre-populate new files with, and the second
creates an `:Ehook` command.

[rails.vim]: https://github.com/tpope/vim-rails
[rbenv]: https://github.com/sstephenson/rbenv

## Features

See `:help projectile` for the authoritative documentation.  Here are some
highlights.

### Global and per project projection definitions

In the above example, we used the global `g:projectiles` to declare
projections based on requirements in the root directory.  If that's not
flexible enough, you can use the autocommand based API, or create a
`.projections.json` in the root of the project.

### Navigation commands

Navigation commands encapsulate editing filenames matching certain patterns.
Here are some examples for this very project:

    {
      "plugin/*.vim": {"command": "plugin"},
      "autoload/*.vim": {"command": "autoload"},
      "doc/*.txt": {"command": "doc"},
      "README.markdown": {"command": "doc"}
    }

With these in place, you could use `:Eplugin projectile` to edit
`plugin/projectile.vim` and `:Edoc projectile` to edit `doc/projectile.txt`.
For `README.markdown`, since there's no glob, it becomes the default
destination for `:Edoc` if no argument is given.

The `E` stands for `edit`.  You also get `S`, `V`, and `T` variants that
`split`, `vsplit`, and `tabedit`.

Tab complete is smart.  Not quite "fuzzy finder" smart but smart nonetheless.
(On that note, fuzzy finders are great, but I prefer the navigation command
approach when there are multiple categories of similarly named files.)

### Alternate files

Projectile provides `:A`, `:AS`, `:AV`, and `:AT` to jump to an "alternate"
file, based on ye olde convention originally established in [a.vim][].  Here's
an example configuration for Maven that allows you to jump between the
implementation and test:

    {
      "src/main/java/*.java": {"alternate": "src/test/java/%s.java"},
      "src/test/java/*.java": {"alternate": "src/main/java/%s.java"}
    }

Bonus feature: `:A {filename}` edits a file relative to the root of the
project.

[a.vim]: http://www.vim.org/scripts/script.php?script_id=31

### Buffer configuration

Check out these examples for a Ruby project:

    {
      "*": {"compiler": "rake"},
      "*.rb": {"indent": 2},
      "spec/*_spec.rb": {"dispatch": "rspec %"}
    }

That last one sets the default for [dispatch.vim][].  The possibilities are
*endless*.

[dispatch.vim]: https://github.com/tpope/vim-dispatch

## Installation

If you don't have a preferred installation method, I recommend
installing [pathogen.vim](https://github.com/tpope/vim-pathogen), and
then simply copy and paste:

    cd ~/.vim/bundle
    git clone git://github.com/tpope/vim-projectile.git

## FAQ

> Why not a clearer filename like `.vim_projections.json`?

I don't see any reason to tie the concept of projections to Vim in particular.
While some features (in particular navigation commands) are idiosyncratic to
the way Vim works, others could make sense in a wide variety of editors.

## License

Copyright © Tim Pope.  Distributed under the same terms as Vim itself.
See `:help license`.
