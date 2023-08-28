---
# date: 2022-04-28
# last_modified_at: 2023-01-06
title: "Dig into zsh: How to Function (part 3)"
category: zsh
# tags:
#   - CLI
#   - shell functions
# excerpt: "Learn how to configure zsh to load functions from a directory using `$fpath` and `autoload`."
header:
  overlay_image: /assets/headers/digging.jpg
  overlay_filter: 0.3
  caption: "Photo credit: [Dog Digging in Planter](https://commons.wikimedia.org/wiki/File:Dog_Digging_in_Planter.jpg)"
  teaser: /assets/teasers/digging-teaser.jpg
toc: true
sidebar:
  nav: dig-into-zsh
---


The focus of this series is to demonstrate methods for learning through experimentation, by trying things and observing the results. I hope the examples I provide are just a starting point for your exploration.

## Fix the `note` function

Returning to the `note` function, this is the first version:

```zsh
note () {
    if [[ -n $* ]]
    then
        less ~/notes/$*
    else
        ls ~/notes
    fi
}
```

When called with the name of a name, it displays the note. Called without arguments, it lists the notes.

However, there's a problem. When I call it with more than one argument, it only displays the first note. When I try to load the next file by pressing ":n", I get this error :

`less: No such file or directory  (press RETURN)`

Let's troubleshoot this error a bit by reproducing the behavior interactively. Using the `set` command (covered in Part 1), we can set the same arguments for the shell itself:

```zsh
set -- git mm
```

Now we can try the same command in the shell as in function, but instead of executing the command, first hit table to expand the `$*` parameter:

```
% less ~/notes/$*<tab>
```

After the parameter is expanded, the command looks like this:

```
% less /Users/stan/notes/git mm
```

See the problem?

The name of the first note gets added to the end of the full path to file, but the second argument is just the file name without the path.

The solution is to `cd` to the `~/notes` directory first, where the full path to the file isn't needed, just as I would from the shell.

```zsh
cd ~/notes
less git mm
```

To prevent from changing the shell's current directory, I can change directories in a subshell, which will not effect the parent shell.

```
% pwd
/Users/stan
% ( cd ~/notes; print hi from $(pwd) )
hi from /Users/stan/notes
% pwd
/Users/stan
```

Note version 2:

```zsh
note () {
    if [[ -n $* ]]
    then
        (cd ~/notes; less $*)
    else
        ls ~/notes
    fi
}
```

## Add an alias

One more thing, I want to add an alias, "n" to make the name even shorter. I'll take advantage of how functions get loaded and define the alias in the function's file:

```zsh
print "\n\n alias n=note" >> ~/functions/note
```

Initilaize the function (after loading) in .zshrc:

```zsh
autoload -Uz ${fpath[1]}/*(:t)
# initialize note
note
```
