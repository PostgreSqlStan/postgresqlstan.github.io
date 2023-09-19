---
title: "TIL (zsh) - clone a prompt theme"
category: zsh
header:
  overlay_image: /assets/headers/zsh-bg.jpg
  overlay_filter: 0.7
  teaser: /assets/teasers/clone-prompt-theme.jpg
excerpt: "Experimenting with a cloned `promptinit` theme."
toc: true
toc_sticky: true
toc_label: Today I learned…
---

*This part of my  attempt to share more TIL notes. Hopefully, with practice I'll get better at writing useful tips and less inclined to post rambling brain dumps.*

I regularly use the zsh promptinit module to display the minimal prompt (`%`) I use for sharing examples:

[![screenshot -  prompt off](/assets/ss/prompt-off.jpg)](/assets/ss/prompt-off.jpg)

To demonstrate a sequence of commands in two different shells, I wanted to add a timestamp to the prompt. Checking the builtin prompt themes, I discovered "bart", which adds an entire line of information about the last executed command. What's great about the "bart" prompt theme is that appends the extra line to any existing prompt:

[![screenshot -  prompt off; prompt bart](/assets/ss/prompt-off-bart.jpg)](/assets/ss/prompt-off-bart.jpg)

But the purple and red text is a bit hard on my old eyes. How hard could it be to change?

## Experiment with a copied theme

Functions for various zsh interface enhancements are stored in `/usr/share/zsh/5.9/functions`. It wasn't difficult to find the function for the "bart" prompt:

```
% ls /usr/share/zsh/5.9/functions/*bart*
/usr/share/zsh/5.9/functions/prompt_bart_setup
```

I created a directory and added it to `fpath`. Then I copied the "bart" function to it, renaming it `prompt_myprompt_setup`, and reinitialized `promptinit.` To my pleasant surprise, that was enough to make "myprompt" show up in the list of prompt themes:

```
% cd ~/sandbox/zsh
% mkdir themes
% cd !$
cd themes
% fpath=(. $fpath)
% cp /usr/share/zsh/5.9/functions/prompt_bart_setup \
> ./prompt_myprompt_setup
% autoload -Uz promptinit && promptinit
% prompt -l
Currently available prompt themes:
adam1 adam2 bart bigfade clint default elite2 elite
fade fire off oliver pws redhat restore suse walters
zefram myprompt
```

This method adds the cloned theme to the current shell. To add a `promptinit` theme to your configuration, it must be located to a directory you've also added to `$fpath` prior to loading `promptinit`.
{: .notice}

Changing the colors it uses has been a challenge for me. I've made some progress but still haven't accomplished exactly what I want. I'll return to that story when I have more progress (understanding) to report.

There are plenty of simpler prompt themes to dig into. Look for files in the zsh functions directory with "prompt" in the name.
{: .notice--info}

