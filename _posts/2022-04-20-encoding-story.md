---

classes: wide
last_modified_at: 2023-09-21
title: "Tangled Up in Encoding"
category: CLI
tags:
  - encoding
  - PostgreSQL
excerpt: "Fixing invalid byte sequences in UTF8 text with sed."
header:
  overlay_image: /assets/images/tangled.jpeg
  overlay_filter: 0.3
  teaser: /assets/teasers/invalid-bytes.jpeg
---

*For the sake of brevity and coherence, various detours, experiments, and missteps have been omitted from this story.*

{% capture env %}
Environment: macOS 12.2.1, zsh 5.8, PostgreSQL 14.1
{% endcapture %}<div class="notice">{{ env | markdownify }}</div>

## Invalid byte sequences

While trying to load a collection of song lyrics into PostgreSQL, I encountered this error message:

```
ERROR:  invalid byte sequence for encoding "UTF8": 0xa0
CONTEXT:  COPY _tmp, line 154:
```

Examining line 154, I saw "\xa0" in the text. (The lyrics which will not be repeated here).

Various desktop applications, including my preferred text editor, opened this file without errors or warnings, simply displaying the invalid bytes in a different style. In a file this size, I doubt I would have noticed the problem without postgres reporting an error.

To be clear, strict data validation is one of the many reasons I use postgres. This problem involves postgres, but it's not a problem with postgres.


### The Adventure Begins

{% capture info-1 %}
*Let's pretend that rather down chasing down a solution to the first error I encountered, I did the sensible thing described here: I stopped to determine the full scope of the problem before seeking solutions.*
{% endcapture %}<div class="notice">{{ info-1 | markdownify }}</div>


To check for other invalid byte sequences, I decided to filter out the lines containing "\xa0" and try to load the file again.

Instead of editing the source file, I use the `program` option of the postgres `\copy` command to perform whatever filtering is needed. In this case, because the file is tab-delimited (despite the .csv suffix) and postgres only allows the `header` option with the csv format, I was already removing the first line of the file with the `tail` program:

```
> \copy _tmp from program 'tail -n +2 lyrics.csv'
```

To remove the lines containing "\xa0", also I pipe the file through grep, using the -v option to remove matching lines:

```
> \copy _tmp from program 'tail -n +2 lyrics.csv | grep -v ''\\xa0'' '
ERROR:  invalid byte sequence for encoding "UTF8": 0xad
CONTEXT:  COPY _tmp, line 834: "7IYU41rxDOremaBZi0vEMn  ['I flew to Hawaii recently to shoot a film, fresh on the heels of being labe..."
```

Trying to load the file again, I find another invalid byte sequence to filter out.

Repeating this process a few times, I continue filtering out problematic lines until the file finally loads with this rather lengthy incantation:

```
> \copy _tmp from program 'tail -n +2 lyrics.csv | grep -v -e ''\\xa0'' -e ''\\xad'' -e ''\\x92'' -e ''\\x9d'' -e ''\\x81'' '
COPY 20238
```

I've loaded 20238 lines out of a total of:

```
% wc -l lyrics.csv
   20405 lyrics.csv
```

That leaves 166 lines that were excluded. Time to try figure out why.

### Examining the invalid data

Having identified 5 invalid byte sequences, I extract the problematic lines for a closer look:

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

Based on visual examination and internet searches for the specific bytes in question, I eventually conclude:

* \xa0 - replace: Should be a non-breaking space.
* \xad - remove: Looks like random noise. Might be a soft hyphen, but not useful here.
* \x9d - remove: Even if it's intended to be a character, it appears in the middle of gibberish. It might a character in windows-1250, but wouldn't be useful here.
* \x92 - replace: Obviously should be an apostrophe, which is what U+0092 is.
* \x81 - remove: Even if this byte is intended to be a valid character, it appears in the midst of gibberish or other invalid bytes. Converting it to a "correct" character could not improve the accuracy of the lyrics.

<figure style="width: 600px" class="align-center">
  <a href="/assets/images/tangled.jpeg" title="Invalid Bytes" alt="painting of several calligraphic numbers and characters twisted into one">
  <img src="/assets/images/tangled.jpeg" alt="painting"></a>
  <figcaption>"Invalid Bytes" (renamed painting by the author's wife)</figcaption>
</figure>

### sed to the Rescue

Can `sed` remove or replace invalid bytes sequences from utf-8 text? Time to find out.

The first time I tried to use sed to remove the `\xa0` bytes, I got an "illegal byte sequence" error:

```
% sed 's/\xa0//g' a0
sed: 1: "s/\xa0//g": RE error: illegal byte sequence
```

Searching the internet, I learn this is, apparently, a fairly common problem. Instead of utf-8, I needed to run `sed` with the locale set to `LC_CTYPE=C`:

```
% LC_TYPE=C sed 's/\\xa0//g' a0
```

Examining the results, I verified this works. The `\xa0` has been removed.

{% capture note %}
**Note:** After setting LC_TYPE once, I seem to have no more "illegal byte sequence" errors from sed, even after restarting my computer. This is slightly concerning, but hasn't seemed to cause any problem. Searching the web, I haven't found reports of this behavior. I'm choosing to ignore the issue for as long as possible.
{% endcapture %}<div class="notice--primary">{{ note | markdownify }}</div>

Next I try replacing `\xa0` with the correct code for a non-breaking space, `\xc2\xa0`:

```
% sed 's/\\xa0/\xc2\xa0/g' a0 | less
```

Reviewing the results, the `\xa0` bytes have clearly been replaced with spaces. It works.

Similarly, I verified sed was able to replace invalid bytes in other files with an empty string. For example, for `\xad`:

```
% sed 's/\\xad//g' ad
```

That left `\x92`. Based on visual inspections, it should cleary be some type of apostrophe. Internet searches verified `\x92` is an apostrophe in windows-1252. So, I tried converting the character from windows-1252 to utf-8:

```
% echo '\x92' | iconv -f windows-1252 -t utf-8
’
```

It worked. So, I copied the above apostrophe into the following sed pattern to fix the invalid `\x92` bytes:

```
% sed 's/\\x92/’/g' 92
```

Once again, visual inspection verified it worked.

### Apply the fixes

Applying all the fixes, I again tried to load the file into postgres:

```
=> \copy _tmp from program 'tail -n +2 lyrics.csv | sed -e ''s/\\xa0/\xc2\xa0/g'' -e ''s/\\xad//g'' -e ''s/\\x92/’/g'' -e ''s/\\x9d//g'' -e ''s/\\x81//g'' '
ERROR:  invalid byte sequence for encoding "UTF8": 0x8f
CONTEXT:  COPY _tmp, line 13420: "4uM5yY5f3430f8Q27ijmLd  "['[Nappy Intro]\n:\x8f\x8f\x8f\x8f\x8f\x8f\x8f^|\n\n[Klonopin Intro]\nOh God..."
```

And I discovered another invalid byte!

Examining the line and searching the web didn't reveal any obvious substitutions, so I decide to remove `0x8f` as well, adding one more filter to the long incantation:

```
> \copy _tmp from program 'tail -n +2 lyrics.csv | sed -e ''s/\\xa0/\xc2\xa0/g'' -e ''s/\\xad//g'' -e ''s/\\x92/’/g'' -e ''s/\\x9d//g'' -e ''s/\\x81//g'' -e ''s/\\x8f//g'' '
COPY 20404
```

Finally, the entire file loaded!


### Isn't there an easier way?

I'm glad to learned how to inspect and convert invalid UTF8 bytes sequences. I just wish I didn't have to rely on postgres to find them. There's probably a builtin tool I can use to validate encoding, but, despite a fair amount of searching, I haven't found it yet.

## Related

- [ERROR: INVALID BYTE SEQUENCE – FIX BAD ENCODING IN POSTGRESQL](https://www.cybertec-postgresql.com/en/fix-bad-encoding-postgresql/) by Laurenz Albe
- [Getting out of SQL_ASCII, part 2](https://tapoueh.org/blog/2010/02/getting-out-of-sql_ascii-part-2/) by Dimitri Fontaine
