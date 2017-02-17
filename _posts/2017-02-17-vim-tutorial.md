---
layout: post
title: Getting the most out of Vim
---

This is a brief about my Vim setup and how can one start using the awesome features of Vim.
*Not an actual tutorial. Just insights about what Vim is capable of and showcase of my setup*.

For beginner tutorial, visit [openvim](http://www.openvim.com/). Moreover, Youtube has a ton of screencasts about it.

### Keyboard shortcuts
This makes Vim require some learning curve, but is a necessity. Learn some of them, and try to use them regularly.
[Vim Wikia](http://vim.wikia.com/) is a great article about [naviation shortcuts](http://vim.wikia.com/wiki/All_the_right_moves).

Vim has a special key, called the `leader` key. This can be any key on your keyboard. I've chosen `,` as my leader key for it causes least strain on hands, and is not frequently used. You can use combinations of keys with leader key to create many custom shorcuts. Find more at my [vimrc](https://raw.githubusercontent.com/himanshub16/MyScripts/master/vimrc).

### .vimrc
This is the configuration file for Vim, located in your home directory. Here, you can place your customisations like keyboard shortcuts, color schemes, plugins, etc.
This is the first file processed after Vim loads.

### Vim plugins
Vim can be interfaced with Plugins which can add features like file browser, syntax checking, git, and many more.
[Vim Awesome](http://vimawesome.com) is a great place to find plugins.

To use plugins, we will need a plugin manager, which can add/remove/update plugins for us. Vim has plugins like [Vundle](https://github.com/VundleVim/Vundle.vim), [VimPlug](https://github.com/junegunn/vim-plug), [pathogen](https://github.com/tpope/vim-pathogen).
I use VimPlug (recommened). However, all of them work fine.

To install any plugin, click the link and follow the instructions on their README.

### MVP (Most valuable plugins) (for me :P)
* **[Nerdtree](https://github.com/scrooloose/nerdtree)**: Provides a directory tree on the left.
* **[Nerdcommenter](https://github.com/scrooloose/nerdcommenter)**: Comment and uncomment multiple lines at once.
* **[Autopairs](https://github.com/jiangmiao/auto-pairs)**: Automatically pairs brackets, quotest, etc.
* **[Syntastic](https://github.com/vim-syntastic/syntastic)**: Real-time syntax checker. Highlights possible errors in code, like variable type, return value etc.
* **[Neocomplete](https://github.com/Shougo/neocomplete.vim)**: Auto-completion based on open files.
* **[YouCompleteMe](https://github.com/Valloric/YouCompleteMe)**: Autocompletion for almost any language, even libraries.
* **[Vim Airline](https://github.com/vim-airline/vim-airline)**: Status line at the bottom of your window, with short details about your environment.

Here's a view of my plugins in action:

![Screenshot]({{ site.url }}/assets/powering-up-vim/screenshot.png)


### ;TLDR;
# Want to try my vimrc?
* Download Vim. On Debian/Ubuntu, `sudo apt install vim vim-nox`
* Download it [here](https://raw.githubusercontent.com/himanshub16/MyScripts/master/vimrc). You can refer to comments in the file for references.
* Install [Vim Plug](https://github.com/junegunn/vim-plug).
* Open Vim, and then type `:PlugInstall`.
* Wait for things to set up.

*YouCompleteMe can take considerable time to build after install. However, that is one-time. You can comment the lines regarding youcompleteme to make things fast while setup.*

*Don't freak out at the lines at the end. I've too used the snippets available on the README pages of the respective plugins.*
