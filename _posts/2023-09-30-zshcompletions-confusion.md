---
title: "zsh: confounded by zshcompletions"
category: zsh
header:
  overlay_image: /assets/headers/zsh-bg.jpg
  overlay_filter: 0.8
  teaser: /assets/teasers/matrix.jpg
classes: wide
excerpt: "Seems like this should be easy."
---

This command in my `.zshrc` works, providing the desired file completions for the `note` function:

```zsh
compdef '_path_files -W "${NOTE_HOME:-${HOME}/notes/}" -g "^.*"' note
```

From what I can understand—which is very little—from  docs and other sources, is to have completion definitions loaded automagically, there should be a file named `_note` in a directory included in `$fpath`.

OK. But, aside from the file starting with `#compdef`, I have no idea what I'm supposed to put in it.

This doesn't work:

```zsh
#compdef '_path_files -W "${NOTE_HOME:-${HOME}/notes/}" -g "^.*"' note
```

I tried removing the single quotes:

```zsh
#compdef _path_files -W "${NOTE_HOME:-${HOME}/notes/}" -g "^.*" note
```

I tried running the `_path_files` function after the first line declaration:

```zsh
#compdef note
_path_files -W "${NOTE_HOME:-${HOME}/notes/}" -g "^.*"
```

I even tried just copying the same command to the `_note` file:

```zsh
#compdef note
compdef '_path_files -W "${NOTE_HOME:-${HOME}/notes/}" -g "^.*"' note
```

Nor does this variation work:

```zsh
#compdef note
compdef '_path_files -W "${NOTE_HOME:-${HOME}/notes/}" -g "^.*"'
```

None of the completions defined in functions starting with `_` in `/usr/local/zsh/5.9/functions` seem to offer a simple example I could mimic. Even searching the internet for `_compdef _path_files` doesn't yield much aside from copies of things I've already read without finding an answer to my question.

This should be easy, right?

