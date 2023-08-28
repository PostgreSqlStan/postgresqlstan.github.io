---
# date: 2022-04-20
# last_modified_at: 2022-04-28
title: "Dig into zsh: How to Function (part 1)"
category: zsh
tags:
  - CLI
  - shell functions
excerpt: "Hands-on exploration zsh shell functions"
header:
  overlay_image: /assets/headers/digging.jpg
  overlay_filter: 0.3 # same as adding an opacity of 0.5 to a black background
  caption: "Photo credit: [Dog Digging in Planter](https://commons.wikimedia.org/wiki/File:Dog_Digging_in_Planter.jpg)"
  teaser: /assets/teasers/digging-teaser.jpg
toc: true
toc_sticky: true
sidebar:
  nav: dig-into-zsh
---

For my first dive into zsh shell functions, I decided to simplify access to a collection of notes I keep in `~/notes`.

<!-- For my first dive into zsh shell functions, I wanted to simplify access to a collection of command line notes. -->

```
% ls ~/notes
git       mm          pygments     sublime
less      postgres    shell        zsh
```

I want to create a function that, given the name of a file in the `~/notes` directory, displays the file. Called without arguments, it should list the contents of the directory.

To meet these modest requirements, I'll need to learn how to: 1) create a zsh function, 2) handle arguments, and 3) execute commands conditionally.

{% capture requirements %}**Requirements:** A terminal running zsh shell. Some prior experience with a shell is assumed but not required.

Most of the examples used here were lifted from the 2003 [A User's Guide to ZSH](https://zsh.sourceforge.io/Guide/) and still work, nearly two decades later, with zsh 5.8 on macOS. I assume they'll work on other platforms and future versions of zsh as well.
{% endcapture %}<div class="notice">{{ requirements | markdownify }}</div>

## Learning to function

I recommend opening a terminal window before reading any further. Go ahead. I'll wait.

<figure style="width: 600px" class="align-center">
 <a href="/assets/images/Edgar_Degas_-_Waiting_-_Google_Art_Project.jpg" title="Waiting" alt="Waiting"><img src="/assets/images/Edgar_Degas_-_Waiting_-_Google_Art_Project.jpg" alt=""></a>
 <figcaption>Degas, Edgar. <i>Waiting</i>. 1880-1882, pastel on paper (via <a href="https://commons.wikimedia.org/wiki/File:Edgar_Degas_-_Waiting_-_Google_Art_Project.jpg">Wikimedia Commons</a>)</figcaption>
 </figure>


### Defining a function

 You can define a function by entering this incantation:

```zsh
fn() {
    print I am a function
}
```

{% capture info1 %}After entering the first line, the shell recognizes a function has begun and changes the prompt. The prompt returns to normal after entering "}" on the last line.
 {% endcapture %}<div class="notice">{{ info1 | markdownify }}</div>


The parenthesis and curly brackets are required, but the spaces and new lines in this example are optional. I  wouldn't recommend it, but the same function can be written as concisely as this:

```zsh
fn(){print i am a function}
```

Enter a function's name to run it.

```
% fn
I am a function
```

Use the shell command `unfunction` to undefine a function:

```
% unfunction fn
% fn
zsh: command not found: fn
```

### Compound commands

The body of a function is typically a list of commands enclosed by curly brackets, known as a *compound-command*. As the name implies, it can contain multiple commands:

```zsh
fn() {
    print I am a function
    print I am powerful
}
```

The compound-command syntax is not limited to functions. The same method can be used to group and execute commands interactively in the shell.

```
% {
cursh> print I am a
cursh> print compound-command
cursh> }
i am a
compound-command
```

{% capture info2 %}After you enter `{`, the shell recognizes a compound-command has started and changes the prompt. After you enter the closing `}`, the normal prompt is restored and commands are executed.
 {% endcapture %}<div class="notice">{{ info2 | markdownify }}</div>

{% capture tip1 %}:bulb: **Tip – paste into braces.** Enter `{` before pasting into the shell to preview commands before executing them.
 {% endcapture %}<div class="notice--primary">{{ tip1 | markdownify }}</div>


A function created interactively will not exist in a newly launched shell or after you exit the current shell.

```
% fn() { print i am powerful }
% fn
i am powerful
% zsh -c 'fn'
zsh:1: command not found: fn
```

You'll learn how to save a function soon. Meanwhile, take advantage of your function's fleeting existence by experimenting with it for a while.

<figure style="width: 600px" class="align-center">
  <a href="/assets/images/1280px-Chodowiecki_Basedow_Tafel_12_d.jpg" title="Experiments" alt="Experiments">
  <img src="/assets/images/1280px-Chodowiecki_Basedow_Tafel_12_d.jpg" alt=""></a>
  <figcaption>Chodowiecki, Daniel. 1774, copper plates (via <a href="https://commons.wikimedia.org/wiki/File:Chodowiecki_Basedow_Tafel_12_d.jpg">Wikimedia Commons</a>)</figcaption>
</figure>

### Handling arguments

The shell uses special variables called *positional parameters* to pass arguments to a shell function, shell script, or the shell itself.

#### Positional parameters

For example, the positional parameter `$#` holds the number of arguments and `$*` is an array holding the arguments:

```zsh
args() {
  print $# $*
}
```

Try calling the above function with different arguments.

```
% args hi
1 hi
% args these are arguments
3 these are arguments
% args 'this is an argument'
1 this is an argument
```

#### Set parameters interactively

It's easy enough to create a function like `args` to experiment with positional parameters. A even easier way is to use `set` command to change the shell's positional parameters interactively:

```
% set a whole load of words
% print $*
a whole load of words
```

It's as if the shell had been called with the arguments you passed to the `set` command.

To set the shell's arguments to none, use `--` after the command.

```
% set --
% print $#
0
```

{% capture info-params %}For more information see [POSITIONAL PARAMETERS](https://zsh.sourceforge.io/Doc/Release/Parameters.html#Positional-Parameters) and [PARAMETERS SET BY THE SHELL](https://zsh.sourceforge.io/Doc/Release/Parameters.html#Parameters-Set-By-The-Shell) in the zshparam man pages.
 {% endcapture %}<div class="notice--info">{{ info-params | markdownify }}</div>


#### Watch out for options

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

<figure style="width: 600px" class="align-center">
 <a href="/assets/images/Art_Deco_Cubism_2022_The_Coming_Storm.jpg" title="study of a brewing argument" alt="study of a brewing argument">
 <img src="/assets/images/Art_Deco_Cubism_2022_The_Coming_Storm.jpg" alt=""></a>
 <figcaption>Soriano, David S. 2022, <i>study of a brewing argument</i> (via <a href="https://commons.wikimedia.org/wiki/File:Art_Deco_Cubism_2022_The_Coming_Storm.png">Wikimedia Commons</a>)</figcaption>
 </figure>

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

{% capture info-conditionals %}There are many tests you can perform. See [CONDITIONAL EXPRESSIONS](https://zsh.sourceforge.io/Doc/Release/Conditional-Expressions.html) in the zshmisc manual pages for more information.
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

{% capture see-connectors %}See [3.81.1: Logical command connectors](https://zsh.sourceforge.io/Guide/zshguide03.html#l67) in the User's Guide.
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

## The `note` function

Speaking of tests, here's one for you. By this point, you should be able to write the first version of this function. Give it a try before looking at my solution.

<figure style="width: 600px" class="align-center">
 <a href="/assets/images/test_station.jpeg" title="Test Station" alt="image of NES Test Station">
 <img src="/assets/images/test_station.jpeg" alt=""></a>
 <figcaption>Test Station (via <a href="https://commons.wikimedia.org/wiki/File:NES_Test_Station_%26_SNES_Counter_Tester_20140112.jpg">Wikimedia Commons</a>)</figcaption>
 </figure>



{% capture note-function %}
```zsh
note() {
  if [[ -n $* ]]; then
    less ~/notes/$*
  else
    ls ~/notes
  fi
}
```
{% endcapture %}

<details>
    <summary><b>note v0.1</b></summary>
<!-- empty line -->
{{ note-function | markdownify }}
</details>
<!-- empty line -->


## Saving a function

The easiest way to save a function is to add it to your zsh configuration, typically stored at `~/.zshrc`.

If you've already defined the function in the shell, you can append its definition to `~/.zshrc` by piping the output of the command `which note`
to the end of the file:

```zsh
print -- "\n\n$(which note)" >> ~/.zshrc
```

## Next: Part two

There's certainly room for improvement.

Most notably, our function doesn't pass multiple arguments to `less` correctly.

Feedback would be awesome.

[Next: How to Function (part 2)](zsh-functions-2.md){: .btn .btn--info}


---

{% capture style1 %}[Tested with zsh 5.8 on macOS 12.3.1]
 {% endcapture %}<div class="notice">{{ style1 | markdownify }}</div>

{% capture style2 %}:bulb: **Tip: paste into braces** - Enter `{` before pasting into the shell to preview commands before executing them.
 {% endcapture %}<div class="notice--primary">{{ style2 | markdownify }}</div>

{% capture style3 %}This is notice--info class.
 {% endcapture %}<div class="notice--info">{{ style3 | markdownify }}</div>

{% capture style4 %}This is notice--success class.
 {% endcapture %}<div class="notice--success">{{ style4 | markdownify }}</div>

---
{% capture info-params %}
For more information see [SHELL BUILTIN COMMANDS](https://zsh.sourceforge.io/Doc/Release/Shell-Builtin-Commands.html) in the zshbuiltins man pages (or enter `help set`).
{% endcapture %}<div class="notice--info">{{ info-params | markdownify }}</div>

