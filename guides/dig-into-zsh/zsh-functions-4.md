---
# date: 2022-04-28
# last_modified_at: 2023-01-06
title: "Dig into zsh: How to Function (part 4)"
category: zsh
# tags:
#   - CLI
#   - shell functions
excerpt: "Parse options with zparseopts"
header:
  overlay_image: /assets/headers/digging.jpg
  overlay_filter: 0.3
  caption: "Photo credit: [Dog Digging in Planter](https://commons.wikimedia.org/wiki/File:Dog_Digging_in_Planter.jpg)"
  teaser: /assets/teasers/digging-teaser.jpg
toc: true
sidebar:
  nav: dig-into-zsh
---

## Parsing Options - notes

Learned how to use `zparseopts` entirely from this gist:
https://gist.github.com/mattmc3/804a8111c4feba7d95b6d7b984f12a53



```zsh
note () {
  local flag_help
  local arg_pyg
  local usage=(
    "Usage:"
    "  p [-h|--help]"
    "  p [-p|--pygemtize] lexer"
  )
  zmodload zsh/zutil
  zparseopts -D -F -- {h,-help}=flag_help {p,-pygmentize}:=arg_pyg || return 1
  [[ -z "$flag_help" ]] || { print -l $usage && return }

  if [[ -n $* ]]; then
    (
      cd ~/notes
      if (( $#arg_pyg )) && (( $+commands[pygmentize] )); then
        LESSOPEN="|pygmentize -l ${arg_pyg[-1]} %s" less -RXF $*
      else
        less -FX $*
      fi
    )
  else
    ls ~/notes
  fi
}

compdef '_path_files -W "$HOME/notes/" -g "^.*"' note
alias n=note
```

