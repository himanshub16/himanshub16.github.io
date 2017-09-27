---
layout: post
title: How to use GCC and Vim on Windows
categories: [vim, gcc, windows, mingw]
medium_link: none
---

Most of the GNU tools are available for not just Linux, but other platforms as well.
Windows users now have the option of using Ubuntu via [WSL](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).
However, many times, you just want to use GCC (C/C++) and (sometimes vim) for programming and don't want the entire Linux environment.

#### What you need to install?
MinGW for the compiler, and GVim for ViM.

### Installing ViM
* Pretty easy. [Download from here](https://vim.sourceforge.io/download.php) the Windows build of ViM.
* Start the installation and make sure to check all the checkboxes in installation menus.
* After installation completes, you can find shortcuts on your desktop and also access from the Command Line.

### Installing MinGW
* Download the latest version of [MinGW](https://sourceforge.net/projects/mingw/files/latest/download?source=typ_redirect).
* Run the downloaded file as administrator.
* Select the options as shown in the screenshot below and __*Apply chanages*__ under Installation menu. The installation will proceed.

![Select mingw32-base and mingw32-gcc-g++]({{ site.url }}/assets/vim-on-windows/mingw.PNG)


The installation is almost done. You just need to add it to PATH.


* On Desktop, right click on *This PC or My Computer* icon and select **Properties**.
  Alternatively, you can also search for *Environment variables* in Start menu.
  
* In the window that appears, click on **Advanced System Settings** in the left column.

![Thic PC properties]({{ site.url }}/assets/vim-on-windows/thispc.PNG)

* Select **Environment variables** in the Window that appears.

![environment variables]({{ site.url }}/assets/vim-on-windows/env_var.PNG)

* Highlight the **Path** variable in the *System variables* section and click on *Edit* button. 

![environment variables]({{ site.url }}/assets/vim-on-windows/mod_env.PNG)

* Add the required path to end of paths in dialog, placing a semicolon at the end as shown in the screenshot.
  Make sure the path "C:\MinGW\bin" exists on the system. In any case, you have to enter the correct path.

![environment variables]({{ site.url }}/assets/vim-on-windows/final.PNG)

* Fire up the command prompt to check if things are set up and working.

![vim running]({{ site.url }}/assets/vim-on-windows/vim_running.PNG)

![vim start]({{ site.url }}/assets/vim-on-windows/vim_start.PNG)

![live]({{ site.url }}/assets/vim-on-windows/live.PNG)
### Bravo! You are done.
