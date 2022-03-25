---
last_modified_at: 2021-03-25
title: "Tangled Up in Encoding"
category: CLI
tags:
  - encoding
  - PostgreSQL
excerpt: "An adventure with encoding errors."
classes: wide
header:
  overlay_image: /assets/images/tangled.jpeg
  overlay_filter: 0.5
  teaser: /assets/teasers/invalid-bytes.jpeg
---

*For the sake of brevity and coherence, various detours, experiments, and failures have been omitted from this story.*

## The Adventure Begins

Trying to load a collection of song lyrics into PostgreSQL with the `\COPY` command, I received this error message:

```
ERROR:  invalid byte sequence for encoding "UTF8": 0xa0
CONTEXT:  COPY _tmp, line 154:
```

Examining line 154, I saw "\xa0" in the text (in the midst of lyrics which will not be repeated here).

Trying to load a file into postgres is only way I know to determine if it contains invalid byte sequences. So I filter out the problematic lines and try to load the file again:

```
> \copy _tmp from program 'tail -n +2 lyrics.csv | grep -v ''\\xa0'' '
ERROR:  invalid byte sequence for encoding "UTF8": 0xad
CONTEXT:  COPY _tmp, line 834: "7IYU41rxDOremaBZi0vEMn  ['I flew to Hawaii recently to shoot a film, fresh on the heels of being labe..."
```

(The .csv suffix is a lie and `tail -n +2` is used to remove the header, something postgres refuses to do if it's not in csv format.)

I discover another invalid byte sequence to filter out: "0xad". Repeating the process, I keep filtering out problematic lines until the file loads with this incantation:

```
> \copy _tmp from program 'tail -n +2 lyrics.csv | grep -v -e ''\\xa0'' -e ''\\xad'' -e ''\\x92'' -e ''\\x9d'' -e ''\\x81'' '
COPY 20238
```

I've loaded 20238 of 20404 rows, leaving 166 problematic rows to examine.

### Examine the invalid data

Having identified 5 invalid byte sequences (0xa0, 0xad, 0x92, 0x9d, 0x81), I'll extract the problematic lines to review:

```
% mkdir invalid
% grep '\\xa0' lyrics.csv> ./invalid/a0
% grep '\\xad' lyrics.csv> ./invalid/ad
% grep '\\x92' lyrics.csv> ./invalid/92
% grep '\\x9d' lyrics.csv> ./invalid/9d
% grep '\\x81' lyrics.csv> ./invalid/81
% cd invalid
% wc -l * | sort -k1 -nr
     168 total
     147 a0
       7 ad
       7 9d
       4 92
       3 81
```

Based on visual examination and internet searches for the specific bytes in question, I eventually concluded:

* \xa0 - replace: Should be a non-breaking space.
* \xad - remove: Looks like random noise. Might be a soft hyphen, but not useful here.
* \x9d - remove: Even if it's intended to be a character, it appears in the middle of gibberish. It might a character in windows-1250, but wouldn't be useful here.
* \x92 - replace: Obviously should be an apostrophe, which is what U+0092 is.
* \x81 - remove: Even if this byte is intended to be a valid character, it appears in the midst of gibberish or other invalid bytes. Converting it to a "correct" character could not improve the accuracy of the lyrics.

<figure style="width: 800px" class="align-center">
  <a href="/assets/images/tangled.jpeg" title="Tangled" alt="painting of several cursive characters twisted into one">
  <img src="/assets/images/tangled.jpeg" alt="painting"></a>
  <figcaption>Invalid Bytes (renamed painting by the author's wife)</figcaption>
</figure>

### sed to the Rescue

When I tried to use sed to remove the `\xa0` bytes I got an "illegal byte sequence" error:

```
% sed 's/\xa0//g' a0
sed: 1: "s/\xa0//g": RE error: illegal byte sequence
```
A quick internet search found this solution:

```
% LC_TYPE=C sed 's/\\xa0//g' a0
```

Inspecting the results, the `\xa0` has been removed. It works.

{% capture requirements %}
**Note:** After setting LC_TYPE once, I seem to have no more "illegal byte sequence" errors from sed, even after restarting my computer. This is slightly concerning, but hasn't seemed to cause any problem. Searching the web, I haven't found reports of this behavior. I'm choosing to ignore the issue for as long as possible.
{% endcapture %}<div class="notice--primary">{{ requirements | markdownify }}</div>

Next I try replacing `\xa0` with the correct code for a non-breaking space, `\xc2\xa0`, and examine the results.

```
% sed 's/\\xa0/\xc2\xa0/g' a0 | less
```

Reviewing the results, the `\xa0` bytes have clearly been replaced with spaces.

Similarly, I used sed to replace invalid bytes in other files with an empty string. For example, for `\xad`:

```
% sed 's/\\xad//g' ad
```

That left `\x92` to deal with, which should be some type of apostrophe. Based on common advice on the internet, I tried converting the character from windows-1252 to utf-8:

```
% echo '\x92' | iconv -f windows-1252 -t utf-8
’
```

Finally, I copied the above output into the following sed pattern to fix the invalid bytes in `92`:

```
% sed 's/\\x92/’/g' 92
```

### Apply the fixes

Applying all the fixes, I again tried to load the file into postgres:

```
=> \copy _tmp from program 'tail -n +2 lyrics.csv | sed -e ''s/\\xa0/\xc2\xa0/g'' -e ''s/\\xad//g'' -e ''s/\\x92/’/g'' -e ''s/\\x9d//g'' -e ''s/\\x81//g'' '
ERROR:  invalid byte sequence for encoding "UTF8": 0x8f
CONTEXT:  COPY _tmp, line 13420: "4uM5yY5f3430f8Q27ijmLd  "['[Nappy Intro]\n:\x8f\x8f\x8f\x8f\x8f\x8f\x8f^|\n\n[Klonopin Intro]\nOh God..."
```

Another invalid byte!

After examining the line and searching the web for obvious character substitutions, I decide to remove `0x8f` as well.

```
> \copy _tmp from program 'tail -n +2 lyrics.csv | sed -e ''s/\\xa0/\xc2\xa0/g'' -e ''s/\\xad//g'' -e ''s/\\x92/’/g'' -e ''s/\\x9d//g'' -e ''s/\\x81//g'' -e ''s/\\x8f//g'' '
COPY 20404
```

Finally. That was exhausting. Time for a nap.

<figure style="width: 800px" class="align-center">
  <a href="/assets/images/nap-time.jpeg" title="Nap Time" alt="napping cat">
  <img src="/assets/images/nap-time.jpeg" alt="napping cat"></a>
  <figcaption>Napping cat</figcaption>
</figure>
