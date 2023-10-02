---
title: "zsh: learning notes (pbnote function)"
category: zsh
tags:
  - note to self
header:
  overlay_image: /assets/headers/zsh-bg.jpg
  overlay_filter: 0.7
  teaser: /assets/teasers/digging-teaser.jpg
classes: wide
---

*Learning zsh in public, `pbnote` function.*

## pbnote function - dev notes

`pbnote` - insert (macos) clipboard into note

### note entry template (array or text):

If I use a text template for a note entry, lines with empty content end up becoming extra empty lines.

E.g.:

```zsh
local template=$(cat <<- "EOF"
    ✏️  [$(date)] ${title}
    ${input}
    ${pasted}
    EOF
    )
```

The output is:

```
✏️  [Thu Sep 28 13:15:24 CDT 2023]

good morning, geek
```

I think I’ll try an array (again), using  the `eval` method , a variation of this:

```zsh
eval "print \"${template}\""
```

:heavy_check_mark: Solved!

```zsh
# assemble lines to be inserted
local output
local item_sep='' # no separator before 1st line
for item in $note_format
do
    line=$(eval "print -- \"${item}\"")
    [[ -n "$line" ]] \
        && output="${output}"${item_sep}$(eval "print -- \"${line}\"")
    item_sep='\n'
done
print $output >> ${note_home}/${note_name}
```

### error - `command not found: compdef`

The source file for `note` and `pbnote`, `${ZDOTDIR}/functions/note`, is sourced by the pbnote command in `~/.local/bin`. Called from `less` (which is why I set it up that way), it works but generates a couple of errors:

```
!pbnote
/Users/heath/.zsh.d/functions/note:75: command not found: compdef
/Users/heath/.zsh.d/functions/note:112: command not found: compdef
!done  (press RETURN)
```

:heavy_check_mark: Incomplete solution

The temporary solution is to declare completions for `note`/`pbnote` in `.zshrc`, e.g.:

```zsh
note                               # init note function
compdef '_path_files -W "${NOTE_HOME:-${HOME}/notes/}" -g "^.*"' note
```

**next step** :arrow_down:

The real solution is to learn more about the zsh completions system, starting here:
- [zsh completions how-to (gist)](https://github.com/zsh-users/zsh-completions/blob/master/zsh-completions-howto.org
).

### next considerations :thought_balloon:

- considering changing the default behavior to not use the clipboard. Perhaps a `-v` switch to paste.
- perhaps divide the note template (poorly named `name_format` for now) into sections so the HEADER section can be suppressed.
- ultimately, it would be nice to be able to, via an option, append a note with as few newlines as possible, e.g. `{thing} {thing} {thing}`
- sorta want some header icon options too.
