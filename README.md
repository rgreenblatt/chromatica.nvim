# chromatica.nvim

Chromatica is an asynchronous syntax highlight engine for Neovim. It is a
python3 remote plugin. Currently, chromatica focuses on providing semantic
accurate syntax highlighting for C-family languages (using libclang).

Currently, the project is in alpha state.

## Features

* Asynchronous parsing and highlighting provides fast and responsive highlight
  as you update your code.
* Semantic-accurate highlighting for C-family languages.

### Example

<img src="https://raw.githubusercontent.com/arakashic/chromatica.nvim/master/figures/comparison_c.png" width="100%"/>

## Prerequites

* [Neovim][3]
* [Python3][4] and [Neovim python client][5]
* [libclang][6] (prefers 3.9.0, the latest HEAD version)

Tested on:

    * macOS 10.11.5, Neovim 0.1.4, Python 3.5.2, clang 3.9.0svn
    * Archlinux, Neovim 0.1.4, Python 3.5.1, clang 3.8.0

## Installation

### Install Prerequites

Install neovim python client and latest clang
```bash
pip3 install neovim
brew install llvm --HEAD --with-clang
```

### Install Chromatica

Use a plugin manager (for example, Neobundle)

```vim
NeoBundle 'arakashic/chromatica.nvim'
```

Or manually check out the repo and put the directory to your vim runtime path.


## Essential Settings

Like many other clang-based plugins, you need to set the path to your libclang.

```vim
let g:chromatica#libclang_path='/usr/local/opt/llvm/lib'
```

The path can be set to either the path of the libclang.dylib/libclang.so file,
or the directory that contains it.

## Compilation Flags

Chromatica already has flags for simple codes. To provide the most accurate
highlighting for complex projects, chromatica needs to know the correct
compilation flags such as include search path and macro definitions. There are
two ways to do that in chromatica.

1. A compilation database `compile_commands.json`.

    This is usually generated by CMake. If the project does not use CMake, you
    can generate it using [Bear][7].

2. A `.clang` file that has the compilation flags.

   The `.clang` file allows you to manually set the flags. For example:

   ```
   flags=-I/home/arakshic/.local/include -DNDEBUG
   flang=-I/../src
   ```

   Each `flags` option can have one or more compiler arguments. A `.clang` file
   can have multiple `flags` options. They will be concatenated in the order of
   their appearance.

When chromatica initializes, it search the current directory and the ancestor
directories for these two files. If both file are present, chromatica will
combine the flags in them.

## Highlight Feature Level

Chromatica provides different feature levels. Each level enables a different set
of highlight. This is controlled by `g:chromatica#highlight_feature_level`. 

The default level is 0, which provides basic semantic highlight
with default vim syntax.

A more advanced level is 1, which gets more detailed highlight from libclang
with a customized syntax file.

## Responsive Mode

By default, chromatica only updates highlight when returned to normal mode
after changing the buffer. This is quite awkward since you may have changed
thousands lines of codes, but the highlight will only be updated when you
finish those changes and return to normal mode.

Chromatica provides a responsive mode that reparses and updates the hightlight
as soon as you change the code (in insert mode). To use the responsive mode,
simply set

```vim
let g:chromatica#responsive_mode=1
```

in your vimrc.

Note that the responsive mode comes at the cost of frequent reparsing the
buffer. Even when the highlight is done asynchronously, frequent reparsing can
still cause performance (editor responsiveness) problem if you C++ code is
super complex (Yes, I haven't experienced this problem with C code). Chromatica
uses pre-compiled header to speed up the repasing and throttles the number of
reparse requests per seconds to avoid reparse flooding. You can increase
`g:chromatica#delay_ms` if you still experiencing performance issues.

## Acknowledgement

This project is largely inspired by [deoplete][1] and [color_coded][2].

[1]: https://github.com/Shougo/deoplete.nvim
[2]: https://github.com/jeaye/color_coded
[3]: https://neovim.io
[4]: https://www.python.org/download/releases/3.0
[5]: https://github.com/neovim/python-client
[6]: http://clang.llvm.org
[7]: https://github.com/rizsotto/Bear
