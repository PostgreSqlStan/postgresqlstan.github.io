---
title: "zsh tip: describe-key-briefly"
category: zsh
excerpt: "The zsh 'describe-key-briefly' widget shows the binding of any key."
header:
  overlay_image: /assets/headers/zsh-bg.jpg
  overlay_filter: 0.8
  teaser: /assets/teasers/bindings.jpg
toc: true
toc_label: "Contents"
toc_sticky: true
---

Use the zsh widget, `describe-key-briefly`, to show the binding (definition) of any key or key combo.

## Using describe-key-briefly

By default, `describe-key-briefly` is bound to <kbd>opt</kbd><kbd>x</kbd>.

Press <kbd>opt</kbd><kbd>x</kbd>  and a new line will appear below the active command line:

```
execute: _
```

Enter "describe" and press <kbd>TAB</kbd> to autocomplete the command, which should expand to `describe-key-briefly`. Press enter to run the command and the line changes to:

```
Describe key briefly: _
```

Then press a key (or key combination) to see what, if anything, it's bound to. I entered <kbd>opt</kbd><kbd>ctrl</kbd><kbd>k</kbd> above to verify it wasn't in use. The line changed to:

```
"^[^K" is undefined-key
```

## Add a key binding

Since I found myself using this widget often while exploring key bindings, I added a binding for <kbd>opt</kbd><kbd>ctrl</kbd><kbd>k</kbd> to call `describe-key-briefly` directly:

```zsh
bindkey "^[^K" describe-key-briefly
```

To verify, press <kbd>opt</kbd><kbd>ctrl</kbd><kbd>k</kbd> twice. You should see:

```zsh
"^[^K" is describe-key-briefly
```

