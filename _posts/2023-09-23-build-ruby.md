---
title: "Compiling Ruby 3 on macOS Ventura"
category: macOS
tag: Ruby
header:
  overlay_image: /assets/headers/matrix.jpg
  overlay_filter: 0.4
  teaser: /assets/teasers/build-ruby.jpg
toc: true
toc_sticky: true
toc_label: "Building Ruby"
---

These are the hoops I jumped through to build Ruby on Ventura. There might be other solutions.

**Environment:** macOS 13.5.2 (Darwin 22.6.0 arm) , default PATH settings, no package manager
{: .notice }

- download source tarball (Ruby 3.2.2) from: [Ruby Downloads](https://www.ruby-lang.org/en/downloads/)
- follow [build instructions](https://docs.ruby-lang.org/en/master/contributing/building_ruby_md.html)
- resolve errors: [fix #1](#fix-1), [fix #2](#fix-2)
- repeat last step of build instructions: `make install`


## Errors

These are the errors I had to overcome.

{% capture c1 %}
The first error was this encounter with Apple's license agreement:

```
You have not agreed to the Xcode license agreements. Please run 'sudo xcodebuild -license' from within a Terminal window to review and agree to the Xcode and Apple SDKs license.
```

I actually had to type `agree` in the terminal. :japanese_goblin:
{% endcapture %}<div class="notice--primary">{{ c1 | markdownify }}</div>



When running `make install` (per the build instructions), I encountered an error related to the `pysch` gem. So, I attempted to install and/or update the `pysch` gem:

```zsh
gem install --user-install psych
```

This resulted in another error:

:x: error: `'ruby/config.h' file not found`


Without fully understanding the issue, this [stackexchange dicussion](https://stackoverflow.com/questions/53135863/macos-mojave-ruby-config-h-file-not-found/65481787#65481787) lead me to try adding a couple of soft links in Xcode frameworks. Adjusted for the appropriate versions, I ended up doing this:


### Fix #1 - add soft links

```zsh
cd /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.0.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/include/ruby-2.6.0/ruby
sudo ln -s /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.0.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.6/Headers/ruby/config.h
cd /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX14.0.sdk/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/include/ruby-2.6.0
sudo ln -sf universal-darwin23 universal-darwin22
```

Then, I got a new error. (Progress!)

:x: error: LibYAML required

### Fix #2 - install LibYAML

The new error was related to `LibYAML`, which was easy enough to download from a [repo](https://github.com/yaml/libyaml) and build with the typical steps:

```zsh
./configure
make
sudo make install
```

After installing `LibYAML`, I was able to install run `gem install --user-install psych` without error.

:heavy_check_mark: Finally, having installed `psych`, the final step of the build instructions, `make install`, ran without error.

```
% ~/.rubies/ruby-master/bin/ruby --version
ruby 3.2.2 (2023-03-30 revision e51014f9c0) [arm64-darwin22]
```


