---
title: "Reconsidering bang history"
category: zsh
header:
  overlay_color: "#333"
  overlay_filter: 0.5
  teaser: /assets/teasers/matrix.jpg
---

As much as I like the idea of bang history expansion in the shell, I probably end up escaping “!” much more than I use it.

The three bang history features I ever used enough to care about are easily replaced:

1. Editing the prior command line is just as easy as entering `!!`.

2. The keybinding opt-. (default with zsh on macos) also inserts the last argument of the prior command line, which is easier to type and more convenient.

3. The `r` command, which I recently discovered, is just as easy to use as `^^`:
```
% print yes
yes
% r yes=no
print no
no
```

All the other bang history features take me longer to remember and carefully enter than just editing the command line in question.

So, I'm going test drive this option for a while:

```zsh
setopt nobanghist
```


