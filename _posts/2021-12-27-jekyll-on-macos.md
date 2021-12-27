---
title: "Installing Jekyll on macOS Monterey"
categories:
  - macOS
tags:
  - Jekyll
---

Here are a couple of errors I encountered and managed to solve while trying to install Jekyll with the builtin version of ruby on macOS Montery.

Environment: Macbook Air (M1, 2020) running macOS Monterey (12.1), ruby 2.6.8p205
{: .notice}

## The Short Version

Here are the extra incantations I needed to get jekyll running on macOS:

```shell
gem update --user-install
gem install --user-install ffi -- --enable-libffi-alloc

```

The rest of this page explains why these commands were needed.


## The Full Story

### jekyll Does Not Install

Following the [installation directions for macOS](https://jekyllrb.com/docs/installation/macos/), after verifying the builtin version of ruby met the requirements, I tried to install bundler and jekyll locally:

```shell
gem install --user-install bundler jekyll
```

The subsequent error message included this information:

```
RDoc is not a full Ruby parser and will fail when fed invalid ruby programs.

The internal error was:

	(NoMethodError) undefined method `[]' for nil:NilClass

ERROR:  While executing gem ... (NoMethodError)
    undefined method `[]' for nil:NilClass
```

### The solution

The [troubleshooting](https://jekyllrb.com/docs/troubleshooting/) guide suggested RubyGems might need to be updated on macOS. To avoid installing anything in the system, I tried to update gem locally:

```shell
gem update --user-install
```

### jekyll Does Not Run


Following directions in the [tutorial](https://jekyllrb.com/docs/step-by-step/01-setup/), when I tried to run jekyll I got an error involving `library.rb:275`.

### The solution
Searching for `library.rb:275`, I managed to find a [page](https://github.com/ffi/ffi/issues/864) where several users reported success with a suggested fix for the same error:

```shell
gem install --user-install ffi -- --enable-libffi-alloc
```

I don't really know how that fixes anything, but jekyll ran without errors afterwards.



