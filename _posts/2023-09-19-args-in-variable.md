---
title: "Dig in public (zsh) - storing and passing arguments with a variable"
toc_label: "Exploring zsh"
header:
  overlay_image: /assets/headers/dig-in-public.jpg
  overlay_filter: 0.65
  teaser: /assets/teasers/matrix.jpg
  caption: "Archaeology dig at Hierapolis<br>[Original photo by Chris_Parfitt from Morden, Surrey, England](https://commons.wikimedia.org/wiki/File:Archaeology_dig_at_Hierapolis_03.jpg). Licensed under CC BY 2.0, via Wikimedia Commons"
category: zsh
toc: true
toc_sticky: true
---

# Pass arguments with a variable

This post is a walk-through of methods I used to learn how to pass arguments stored in a variable to a command.

The focus here is on the process of learning through interactive experimentation, not any actual solutions, though some should be revealed along the way.
{: .notice--info}


I wanted to filter the output of `bindkey` by passing these search patterns to `grep`:

```
'\^\[\['      # match "^[["
'\^\[O[A-H]'  # match "^[OA"..."^[OH"
'^\"\\\M'     # match "\M-^@"-"\M-^?"
'\"-\"~\"'    # match " "-"~"
```

Let's pretend I actually did things in the following order instead of backtracking several times.
{: .notice }

## Setup

First, I'll set up a few things to make it easy to experiment.

### Assemble strings in an array

I like the convenience (and readability) of storing strings in an zsh array:
```zsh
patterns=(
  '\^\[\['      # match "^[["
  '\^\[O[A-H]'  # match "^[OA"..."^[OH"
  '^\"\\\M'     # match "\M-^@"-"\M-^?"
  '\"-\"~\"'    # match " "-"~"
  )
```

I'll also load up some test data into another array:

```zsh
data=(line1 '^[[' line3) # don't need much
```

The `print` command is my primary tool for both making sure things are as I imagined and, when results are unexpected, figuring out what's going on:

```
% print -rl -- $patterns
\^\[\[
\^\[O[A-H]
^\"\\\M
\"-\"~\"
% print -rl -- $data
line1
^[[
line3
```

### Testing with an alias

This alias, assigned to `d`, pipes lines from the array I stored test data in:

```zsh
alias d='print -rl -- $data |'
```

Now I can easily test commands against my massive, three line data set:

```
% d grep 'line'
line1
line3
% d grep '\^\[\['
^[[
```

The advantage of creating an alias on the fly, and letting it die with the terminal window, is I never have a chance to forget it.

:bulb: An alias won't exist in the next shell if it's not saved in a configuration file.<br>*This might seem too obvious to mention, but it took me a long time to appreciate how much freedom that gives me to mess up my current shell without worrying about long-term consequences.*
{: .notice}


## Experiment

Setup complete. Time to experiment.

### Pass argument with a variable

I'll start by trying to pass a single pattern stored in a variable to `grep`, using the alias `d` (created above) to pipe the test data to the command:

```
% pat=$patterns[1]  # use 1st pattern
% print -r $pat
\^\[\[
% d grep $pat
^[[
```

That works for a single pattern. But to pass multiple arguments to `grep`, I'll need to prefix each one with " -e ":

```
% estring=" -e $pat"
% print -r $estring
 -e \^\[\[
% d grep $estring
```

:x: No output. :neutral_face:

Maybe the search argument needs to be surrounded with quotes:

```
% estring=' -e '\'$pat\'
% print -r $estring
 -e '\^\[\['
% d grep $estring
```

:x: No output. :worried:

{% capture fig_img %}
[![“Waiting,” Sculpture by Stepan Taryan](/assets/images/woman-waiting.jpg)](/assets/images/woman-waiting.jpg)
{% endcapture %}
{% capture fig_caption %}
“Waiting,” Sculpture by Stepan Taryan.<br>Licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0), via [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Waiting_Sculpture_15.jpg)
{% endcapture %}
<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>{{ fig_caption | markdownify | remove: "<p>" | remove: "</p>" }}</figcaption>
</figure>

Skipping past several failed attempts, error messages, internet searches, and further experimentation…
{: .notice--info }

Despite numerous attempts, using various different quoting and escaping techniques, I haven't managed to successfully pass both an option and argument to `grep` in a variable.

According to answers to questions similar to my own, looks like I should try the `eval`  command:


```
% estring=' -e '\'$pat\'
% print -r $estring
 -e '\^\[\['
% d eval grep $estring
^[[
```

:heavy_check_mark: It worked. Finally! :tada:

:question: I'd still like to understand if `eval` is really the only solution and, if so, why that's the case.
{: .notice }

### Concatenate arguments

Now, instead of passing a single and argument, I'll try it with multiple arguments.

There are probably other, perhaps even easier, ways to extract and concatenate values from an array, but a simple `for` loop should suffice:

```zsh
for pat in $patterns
  do estring=$estring" -e '"$pat"'"
done
```

{% capture dig1 %}
:pick: **Quoting and  escaping**

You might have noticed different quoting methods being used. Both methods output the same result:

```
% print -r -- ' -e '\'$pat\'
 -e '\^\[\['
% print -r -- " -e '"$pat"'"
 -e '\^\[\['
 ```

The first method passes four arguments to `print`:
1) ` -e `
2) `\'` -  a single quote, escaped from the shell with a backslash
3)  `$pat` and
4) `\'` - the closing, escaped single quote.

The second method passes three arguments:
1) `" -e '"`
2) `$pat` and
3) `"'"`

{% endcapture %}<div class="notice">{{ dig1 | markdownify }}</div>

The result looks promising:

```
% print -r -- $estring
 -e '\^\[\[' -e '\^\[O[A-H]' -e '^\"\\\M' -e '\"-\"~\"'
```

### Pass multiple arguments

Now I'll try using the concatenated arguments with a command:

```
% d eval grep $estring
^[[
```

That works.

Finally, I'll test the command as I actually intend to use it, with the output of `bindkey`:

```zsh
bindkey | eval grep -v $estring
```

:heavy_check_mark: Success.

## A working example

The methods shown here are used with `grep` and `sed` in my [bindlist](/zsh/bindlist2) function.

