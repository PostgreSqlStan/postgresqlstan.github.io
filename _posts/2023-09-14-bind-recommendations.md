---
last_modified_at: 2023-09-17
title: "zsh : Dig into key bindings (macOS)"
category: zsh
excerpt: "After a couple years of using zsh and yet another thorough review of key bindings, some observations and recommendations."
header:
  overlay_image: /assets/headers/zsh-bg.jpg
  overlay_filter: 0.8
  teaser: /assets/teasers/bindings.jpg
toc: true
toc_sticky: true
toc_label: zsh key bindings (macos)
---

Last year, in a semi-useful exercise, I make a list of zsh [key bindings](/macos/zsh-keys/) enabled by default in the macOS Terminal.
Reviewing the list helped me learn a few additional shortcuts and add a couple new ones.

Now it's time to up my game. I've created a function, [bindlist](/zsh/bindlist/), which makes it easier than ever to display, filter, and reformat key bindings.

<figure style="width: 600px" class="align-center">
  <a href="/assets/ss/bindlist.jpg" title="screenshot - output of function: bindlist" alt="screenshot - output of function: bindlist">
  <img src="/assets/ss/bindlist.jpg" alt="screenshot -  output of function: bindlist"></a>
  <figcaption>screenshot -  output of function: bindlist</figcaption>
</figure>

Being able to view the bindings so easily is really useful, even more than I'd expected. I've already learned a couple things that had previously escaped my notice, hidden in a sea of special characters spanning several pages.

Based on using zsh for a couple years and the lastest careful review, these are my observations and recommendations.

## Limitations

{% capture n1 %}
Some limitations I've encountered:
- <kbd>ctrl</kbd> + number can't be bound to anything
- <kbd>ctrl</kbd><kbd>c</kbd> can't be bound to anything and isn't listed
<!-- - <kbd>ctrl</kbd><kbd>d</kbd> is a signal and (bound to) delete-char-or-list -->
<!-- - <kbd>ctrl</kbd><kbd>l</kbd> listed and works as clear screen -->
- <kbd>ctrl</kbd><kbd>q</kbd> can't be bound (listed as push-line)
- <kbd>ctrl</kbd><kbd>s</kbd> can't be bound (listed as history-incremental-search-forward)
- control characters are case insensitive, <kbd>ctrl</kbd><kbd>A</kbd> = <kbd>ctrl</kbd><kbd>a</kbd>, but options bindings *are* case sensitive
{% endcapture %}<div class="notice--info">{{ n1 | markdownify }}</div>

## Undefined keys

{% capture n2 %}
These keys are undefined by default and avaiable for use:

  - <kbd>⌥</kbd> - E/e, I/i, J/j, K/k, M/m, O/o, R/r, V/v, X , Y , Z
  - <kbd>⌥</kbd><kbd>ctrl</kbd> - Many `⌥^` bindings, which are case insensitive, are unused.
  - <kbd>ctrl</kbd><kbd>z</kbd> - still works as a signal after binding another command to it
{% endcapture %}<div class="notice--info">{{ n2 | markdownify }}</div>


## Recommended bindings

I prefer to make the best of default settings, only making a change when it clearly enhances my productivity.

So far, I've only added five bindings, removed two that don't actually work, and removed another six redundant bindings I'm unlikely to ever use.

I might update these recommendations at some point but don't expect them to change much, if at all.

### Additional bindings

I used this shortcut a lot while reviewing and verify bindings:

```zsh
bindkey "^[^K" describe-key-briefly
```

I'm never sure if I'll ever use to use yank/pop, or if I even want to. But I might give it a try, especially now I can easily refresh my memory with the `bindlist` function. That's why I added `kill-region`, which was oddly missing from the default bindings:

```zsh
bindkey "^[k" kill-region
```

The default `set-mark-command` binding is hard to remember and not so easy to use. I added opt-m for mark and opt-M for `exchange-point-and-mark`:

```zsh
bindkey "^[m" set-mark-command
bindkey "^[M" exchange-point-and-mark
```

I could never remember the various default bindings for undo.
After verifying using `^z` for undo doesn't break its function as a signal, I happily added this bind to my configuration:

```zsh
bindkey "^Z" undo
```

### Bindings to remove (optional)

This is entirely unnecessary, perhaps even a bad idea, but I also removed some default bindings, just to clean up the list a bit.

I removed a couple of bindings that are listed by `bindkey` but don't actually work (and have other keys bound to the same command):

```zsh
# remove redundant bindings that don't actually work
bindkey -r "^S"
bindkey -r "^Q"
```

Then I removed bindings that are redudant, either by default or because I've added alternative binding:

```zsh
# redundant bindings (by default)
bindkey -r "^[\$"       # spell-word
```

```zsh
# redundant bindings (replaced by my additions)
bindkey -r "^@"         # set-mark-command
bindkey -r "^X^X"       # exchange-point-and-mark
bindkey -r "^X^U"       # undo
bindkey -r "^Xu"        # undo
bindkey -r "^_"         # undo
```
