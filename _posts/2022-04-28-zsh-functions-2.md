---
date: 2022-04-28
title: "Dig into zsh: How to Function (part 2)"
category: zsh
tags:
  - CLI
  - shell functions
excerpt: "Learn how to configure zsh to load functions from a directory using `$fpath` and `autoload`."
header:
  overlay_image: /assets/headers/digging.jpg
  overlay_filter: 0.3
  caption: "Photo credit: [Dog Digging in Planter](https://commons.wikimedia.org/wiki/File:Dog_Digging_in_Planter.jpg)"
  teaser: /assets/teasers/digging-teaser.jpg
---

As soon as I started trying to address shortcomings with the `note` function from [Part One](/zsh/zsh-functions/), I discovered a problem: I don't have the patience to edit functions stored in `~/.zshrc`.

So, this tutorial is about loading functions from a personal directory. I'll return to the `note` function in a future post.

If you're just looking an example of configuring zsh to load a directory of functions, jump to the [end](http://localhost:4000/zsh/zsh-functions-2/#configuring-zsh-to-load-functions). If you want to understand how the process works, continue reading.

## Loading functions from a directory

The method zsh provides for loading functions from a directory is simple. All you need to do is create a directory, add it to `$fpath`, and use `autoload` to load your functions.

I'm using `~/functions` as the directory for this demonstration, but you can do this in any location.

```zsh
mkdir ~/functions
cd ~/functions
```

Navigate to your functions directory and, to prevent effecting the current shell, start a new shell. Then `autoload` a function.

```zsh
zsh
autoload fn
```

The function doesn't exist yet, but that's not a problem.

When the function is called, the shell will check each directory listed in the special parameter `$fpath`, from left to right, looking for a file with the same name as the function.

```
% print -l $fpath
/usr/local/share/zsh/site-functions
/usr/share/zsh/site-functions
/usr/share/zsh/5.8/functions
```

To create a function definition the shell can find, first we need to add our directory to the `$fpath` array:

```zsh
fpath=(~/functions $fpath)
```

Next, create a file named "fn" containing the body of the function. Conveniently, the surrounding `functionname () { ... }` is not required for functions loaded this way:

```zsh
cat << EOF > fn
# example function
print i am the function called fn
EOF
```

See [Heredoc](https://linuxize.com/post/bash-heredoc/) if you're not familiar with the above syntax for creating a file.
{: .notice}

When function is called, the shell finds the definition, then loads and runs it.

```
% fn
i am the function called fn
```

After it's been loaded, changes to the function's definition will have no effect on the current shell. To refresh a function's definition, use `unfunction` before loading it again.

```
% cat << EOF > fn
> print i am fn
> EOF
% fn
i am the function called fn
% unfunction fn
% autoload fn
% fn
i am fn
```

### `autoload -Uz`

Although it's not required for our function, the common form for using `autoload` is:

```zsh
autoload -Uz fn
```

The `-U` option suppresses alias expansion when the function is loaded, which is recommended for the use of functions supplied with the zsh distribution. The `-z` option forces zsh-style loading.
{: .notice}

To get into the habit, further examples will follow this form.


### Loading multiple functions

The `autoload` command can accept multiple arguments. To demonstrate this, first create a second function in our directory:

```zsh
cat << EOF > fn2
print i am fn2
EOF
```

Then `autoload` both functions:

```
% autoload -Uz fn fn2
% fn
i am fn
% fn2
i am fn2
```

You can load an entire directory by using globbing to supply the names of all the functions:

```zsh
autoload -Uz ${fpath[1]}/*(:t)
```

The `${fpath[1]}/*` expands to all the files in the first directory listed in $fpath. The `(:t)` is a *globbing modifier* which takes the tail (basename) of all the files in the list.
<br><br>See [Modifiers](/https://zsh.sourceforge.io/Doc/Release/Expansion.html#Modifiers) in the zshexpn manual pages for more information.
{: .notice}


## Configuring zsh to load functions

To add a directory to `$fpath` and `autoload` functions individually, I can add this to `~/.zshrc`:

```zsh
typeset -U fpath
fpath=(~/functions $fpath)
autoload -Uz fn fn2  # load example functions
```

The `typeset -U fpath` command forces the shell to keep only the first occurrence of each duplicated value in the parameter `fpath`.
{: .notice}

---

I prefer to `autoload` the entire directory. To avoid loading the functions again if `~/.zshrc` is sourced again, I first perform a test to determine whether or not the directory is already in `$fpath`:

```zsh
typeset -U fpath
my_functions=$HOME/functions
if [[ -z ${fpath[(r)$my_functions]} ]] ; then
    fpath=($my_functions $fpath)
    autoload -Uz ${my_functions}/*(:t)
fi
```

The `(r)` *subscript flag* above returns an empty string if the expression that follows it (`my_functions`) doesn't match any element in the array. The '-z' test flag returns true if the string is empty.
<br><br>See [Subscript Flags](https://zsh.sourceforge.io/Doc/Release/Parameters.html#Subscript-Flags) in the zshparam manual pages for more information.
{: .notice}

---

**`export fpath`?**

I've found no suggestion in the zsh manual pages or [User's Guide](https://zsh.sourceforge.io/Guide/) that the `$fpath` parameter should be exported. I'm not aware of any reason it should be. So, contrary fairly common advice, I've chosen not to do so. I'll update this post if I come to regret the decision.

## Next steps

Now that I can save and edit functions in separate files, I'm ready to start improving the `note` function from [Part One](/zsh/zsh-functions), which will be the topic of the next post in this series.

As always, please share any related thoughts, corrections, or questions on [Twitter](https://twitter.com/postgresqlstan).
