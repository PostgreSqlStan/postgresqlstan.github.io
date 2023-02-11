---
date: 2022-04-20
last_modified_at: 2022-04-28
title: "Dig into zsh: How to Function (part 1)"
category: zsh
tags:
  - CLI
  - shell functions
excerpt: "A hands-on exploration of writing a shell function in zsh."
header:
  overlay_image: /assets/headers/digging.jpg
  overlay_filter: 0.3 # same as adding an opacity of 0.5 to a black background
  caption: "Photo credit: [Dog Digging in Planter](https://commons.wikimedia.org/wiki/File:Dog_Digging_in_Planter.jpg)"
  teaser: /assets/teasers/digging-teaser.jpg
toc: true
toc_sticky: true
---

For my first dive into zsh shell functions, I want to create a function to simplify access to my collection of command line notes.

```
% ls ~/notes
git     mm         shell     zsh
less    postgres   sublime
```

Given the name of a note, the function should display it. Called without arguments, it should list the notes.

I'll call this function `note`.

## Learning to function

{% capture sources %}
The examples here are based on internet searches, manual pages, experimentation, plenty of mistakes, and a heavy reliance on [A User's Guide to ZSH](https://zsh.sourceforge.io/Guide/) (mainly Chapter 3, *Dealing with basic shell syntax*).

I'm using zsh 5.8 on macOS 12.3.1, but examples should work with most versions of zsh.
{% endcapture %}<div class="notice">{{ sources | markdownify }}</div>


This intended as a hands-on exploration. I recommend opening a terminal window before reading any further.

Go ahead. We'll wait.

<figure style="width: 600px" class="align-center">
  <a href="/assets/images/Edgar_Degas_-_Waiting_-_Google_Art_Project.jpg" title="Waiting, pastel on paper, 1880–1882" alt="Waiting, pastel on paper, 1880–1882">
  <img src="/assets/images/Edgar_Degas_-_Waiting_-_Google_Art_Project.jpg" alt=""></a>
  <figcaption>"Waiting" by Edgar Degas, from  <a href="https://commons.wikimedia.org/wiki/File:Edgar_Degas_-_Waiting_-_Google_Art_Project.jpg">Wikimedia Commons</a></figcaption>
</figure>


### Defining a function

You can define a function on the command line:

```zsh
fn() {
    print I am a function
}
```

{% capture tip-prompt %}
The shell understands you're entering a function and changes the prompt after the first line. After entering "}" on the last line, the prompt returns to normal.
{% endcapture %}<div class="notice">{{ tip-prompt | markdownify }}</div>

Enter the function's name to run it.

```
% fn
I am a function
```

The spacing and new lines in this example are optional. The same function can be written as concisely as:

```zsh
fn(){print i am a function}
```

Either way, the shell will display the function's definition in this form:

```
% which fn
fn () {
  print i am a function.
}
```

A function created in the shell only exists in the current shell. It won't exist in a newly launched shell.

```
% fn
i am a function
% zsh -c 'fn'
zsh:1: command not found: fn
```

It also won't exist when you close your current shell. So, feel free to experiment.

### Handling arguments

The shell uses special variables, known as *positional parameters*, to pass arguments to functions and scripts.

This function prints the number of arguments passed to it and the arguments:

```zsh
args() {
  print $# $*
}
```

The `$#` holds the number of arguments and `$*` is an array holding the arguments.

Try calling the function with different arguments.

```
% args
0
% args hi
1 hi
% args these are arguments
3 these are arguments
% args 'an argument'
1 an argument
```

{% capture info-params %}
For more information see [POSITIONAL PARAMETERS](https://zsh.sourceforge.io/Doc/Release/Parameters.html#Positional-Parameters) and [PARAMETERS SET BY THE SHELL](https://zsh.sourceforge.io/Doc/Release/Parameters.html#Parameters-Set-By-The-Shell) in the zshparam man pages.
{% endcapture %}<div class="notice--info">{{ info-params | markdownify }}</div>


### Setting arguments interactively

The `set` command sets the special parameter that gets passed as an argument to functions or scripts and is accessed by positional parameters.

```
% set a whole load of words
% print $#
5
% print $*
a whole load of words
```

It’s exactly as if you were in a function and had called the function with the arguments "a whole load of words".

To set the shell's arguments to none, use `--` after the command.

```
% set --
% print $#
0
```


{% capture info-params %}
For more information see [SHELL BUILTIN COMMANDS](https://zsh.sourceforge.io/Doc/Release/Shell-Builtin-Commands.html) in the zshbuiltins man pages (or enter `help set`).
{% endcapture %}<div class="notice--info">{{ info-params | markdownify }}</div>


### Watch out for options

The `set` command can also set options for the shell. While arguments don't effect the shell's behavior, options do.

```
% set -v
% print verbose
print verbose
verbose
```

(Use `set +v` to turn verbose mode back off.)

To prevent arguments from being interpreted as options, use the special option `--`, which signals there are no more options.

```
% set -- -v
% print -- $*
-v
```

Notice the same method is used with `print` to avoid `-v` being interpreted as an option when passed as a parameter.

Another example involves the positional parameter `$0`, which holds the name of the script or function being called.

```
% args() { print $0: $* }
% args hi
args: hi
```

But from the shell, the value of `$0` is "-zsh".

```
% set -- hi
% print $0: $*
print: bad option: -h
% print -- $0
-zsh
```

As you can see, using `--` with `set` and `print` is a good habit to get into.


### Testing things

This is a common shell syntax for testing things:

```zsh
if [[ -n $* ]]; then
  print yes
else
  print no
fi
```

A *conditional expression* is used with the `[[` compound command to test attributes of files and to compare strings. In this case, the -n tests whether the length of a string is non-zero.

{% capture info-conditionals %}
There are many tests you can perform. See [CONDITIONAL EXPRESSIONS](https://zsh.sourceforge.io/Doc/Release/Conditional-Expressions.html) in the zshmisc manual pages for more information.
{% endcapture %}<div class="notice--info">{{ info-conditionals | markdownify }}</div>

In the shell, new lines and semicolons mean the same thing and are interchangeable. So, the same test could be rewritten as:

```zsh
if [[ -n $* ]]; then; print yes; else; print no; fi
```

The *logical command connectors* && and \|\| provide an even shorter syntax for testing. The && executes the command that follows if the prior command succeeded, while \|\| executes the command that follows if the prior command failed.

```zsh
true  &&  print Previous command returned true
false  ||  print Previous command returned false
```

Using this form, our test can be rewritten as:

```zsh
[[ -n $* ]] && print yes || print no
```

{% capture see-connectors %}
See [3.81.1: Logical command connectors](https://zsh.sourceforge.io/Guide/zshguide03.html#l67) in the User's Guide.
{% endcapture %}<div class="notice--info">{{ see-connectors | markdownify }}</div>

With these methods, we can easily verify our test works in the shell before trying it as a function.

```
% set some arguments
% [[ -n $* ]] && print -- $* || print empty
some arguments
% set --
% [[ -n $* ]] && print -- $* || print empty
empty
```

## The function

Putting everything together, it's time to write my first function.

With notes stored at `~/notes`, when this function is called with the name of a note, the note is displayed in `less`. Called without arguments, all the notes are listed.

```zsh
note() {
  if [[ -n $* ]]; then
    less ~/notes/$*
  else
    ls ~/notes
  fi
}
```


### Saving user functions

The easiest way to save a function is to add it to your zsh customizations, typically stored at `~/.zshrc`.

You'll probably want to do that with a text editor, but if you've already entered the function into the shell, you can append its definition to `~/.zshrc` like this:

```zsh
print -- "\n\n$(which note)" >> ~/.zshrc
```


## Next steps

There's certainly room for improvement.

Most notably, the note function doesn't handle multiple arguments correctly. (Figuring out why is left as an exercise for the reader.) I plan to address that shortcoming, as well as other improvements and enhancements, in a future tutorial.

Meanwhile, please share any related thoughts, corrections, or questions on [Twitter](https://twitter.com/postgresqlstan).

---

