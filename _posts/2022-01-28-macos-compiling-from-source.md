---
classes: wide
title: "Compiling from source on macOS"
last_modified_at: "2022-01-29 04:33"
category: CLI
tags:
  - macOS
excerpt: "General directions for installing software from source on macOS."
header:
  teaser: /assets/teasers/compiling.jpg
---
*General directions for installing software from source on macOS.*

{% capture notice-1 %}

**Skills Required:** Intermediate terminal/shell knowledge. While compiling from source is not difficult, it's also not a beginner's task.

Along with basic commands you should also already know what shell you're using, how to configure it, how to use auto-completion while typing commands, and have a decent understanding of file permissions. If you haven't learned those things, you should probably begin your journey into the shell with something simpler.
{% endcapture %}<div class="notice--info">{{ notice-1 | markdownify }}</div>

### Why compile from source?

You probably shouldn't. You should use an installer, package manager, or a container like a normal, busy person who has better things to do with your time.

I compile from source because it's something I learned how to do before reliable package managers were available on macOS and I'm too ~~lazy~~ stubborn to learn another method if I don't have to.

Others reasons to compile source include the ability to configure software to your specific needs, installing multiple versions of a program, and having quicker access to updates.

Whatever your reasons, with some basic terminal skills and patience, it's *usually* not difficult, particularly when dealing with popular, open source software (OSS).

{% capture notice-2 %}

**Software Requirements:** You'll need Apple's Xcode Command Line Tools, which is not installed by default. Enter `gcc -v` in the terminal, which will initiate an installation dialog if the tools aren't already installed.
{% endcapture %}<div class="notice">{{ notice-2 | markdownify }}</div>


## Download and extract the source archive

The first step is locating a source archive of the program you want to install, typically from a project's web page or public repository. Since not many people install from source, the link to source code might not be obvious or easy to find.

There are often multiple archive formats (.zip, .gz, .bz), most of which can be handled with the builtin `tar` command. Locate and copy the link to an appropriate archive.

After you've copied the URL, open the Terminal, navigate to a working directory, and use the `curl` command to download the archive:

```shell
% cd ~/sandbox
% curl -OL http://foo.org/source/foo.tar.gz  # fictional "foo" archive
```

(The `-O` options tells curl to use the original file name for the download and the `-L` option tells curl to follow any redirects.)

Use `tar` to extract the archive:

```shell
% tar -xf foo.tar.gz
```

## Compiling and Installing

Navigate to the source directory and examine the contents. There should be a README and/or INSTALL file with installation directions with requirements, options, platforms details, caveats, and other details. (If not, that's not a good sign.)

The installation directions for Unix-compatible platforms like macOS typically follow this simple pattern:

```shell
% ./configure
% make
% sudo make install
```

First, `./configure` examines your system to find the dependencies the program needs to be built, saving the information and outputting a report. You'll probably see a lot of warnings. That's OK, as long as there are no errors.

{% capture notice-3 %}
Note, the "./" prefix is required because `configure` is a script in the source directory, not a builtin command. You can invoke `configure` with the `--help` flag to see a list of options and sometimes additional installation information, which can be  useful for troubleshooting.
{% endcapture %}<div class="notice">{{ notice-3 | markdownify }}</div>

The `make` command uses the saved information to build the program, displaying a lot of information while completing its sometimes lengthy task.

After the program is built, the `make install` command copies the program to the appropriate location(s), often requiring the `sudo` prefix to invoke the privileges needed to install in system directories like `/usr/local/`.

Note, sometimes there are multiple optional packages that can be installed. For example, you might need to use `make install-docs` to install documentation. Be sure to check the README or INSTALL file for full installation directions and options.

### Dealing with errors

Hopefully, the `configure` script and `make` command will run without error. Of course, that's not always the case. Fortunately, error messages are often very specific and helpful.

The most common error is missing dependencies. If the dependencies are popular packages, easy to locate, and come with clear installation directions, it's usually easy enough to install them myself.

Sometimes the error message contains a clear solution to the problem (a certain option or flag, file permission issue, installation location, etc) which is easy to understand. Even when I don't understand an error message, copying and searching the web for its cryptic text has helped me find a solution more often than not.

If the errors are unsolvable, I look for a binary installer or consider using different software.

## Cleaning Up

Once you've verified the software is correctly installed, the source files are no longer needed. You can delete them, but I usually wait a while in case I've overlooked any extras to install, like optional contrib packages or documentation.
