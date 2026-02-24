# #25 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-05-01 15:41:22 UTC  
> Updated at: 2024-08-19 10:51:34 UTC  
> Merged at: 2024-08-19 10:51:34 UTC  
> Closed at: 2024-08-19 10:51:34 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-06-14 16:58:57 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#issuecomment-2168427733  

An automated preview of the documentation is available at [https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html](https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html)  
  
Diff2html diff: [https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html](https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-07-16 03:48:00 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#issuecomment-2229953476  

An automated preview of the documentation is available at [https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html](https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html)  
  
Diff2html diff: [https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html](https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html)

---

## Review 3 [Commented]

> Username: Lastique  
> Created_at: 2024-07-21 18:22:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostbook/pull/25#pullrequestreview-2190379999  

📁 build.jam

```diff
  23 |+     [ install dist-share
  24 |+         : [ glob $(dirs)/$(patterns) $(dirs)/*/$(patterns)
  25 |+             $(dirs)/*/*/$(patterns) ]
```

> Username: Lastique  
> Created_at: 2024-07-21 18:22:49 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#discussion_r1685789934  

This should probably be `glob-tree`, shouldn't it? I.e. it should glob recursively.

> Username: grafikrobot  
> Created_at: 2024-07-24 04:17:21 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#discussion_r1689088996  

I essentially moved that code from https://github.com/boostorg/boost/blob/develop/tools/Jamfile.v2#L51 .. Not sure why it was done that way. I could change it, but perhaps not worth the trouble as that glob does cover all the files.

> Username: Lastique  
> Created_at: 2024-07-24 10:27:15 UTC  
> Updated_at: 2024-07-24 10:27:16 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#discussion_r1689534920  

I think, it's better to change this to avoid surprises in the future. I'm not even sure `patterns` are needed either. Just install everything there is.

> Username: grafikrobot  
> Created_at: 2024-07-24 12:59:27 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#discussion_r1689752013  

Changed it, for the next rev, to glob-tree-ex. But the patterns are needed in the glob functions b2 has. 🤷🏽

> Username: Lastique  
> Created_at: 2024-07-24 15:32:13 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#discussion_r1690027845  

> But the patterns are needed in the glob functions b2 has.  
  
I meant the patterns need not be specific and should pick all files in the tree.

> Username: grafikrobot  
> Created_at: 2024-07-24 16:11:14 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#discussion_r1690091707  

Problem is that a pattern that picks all files also picks all directories. Which then fails as it tries to copy those dirs as if they are files. Ostensibly the install and glob could be smarter about this.

> Username: Lastique  
> Created_at: 2024-07-24 17:38:04 UTC  
> Updated_at: 2024-07-24 17:38:05 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#discussion_r1690196194  

I see. Let the patterns be for now then.  
  
> Ostensibly the install and glob could be smarter about this.  
  
Yeah, that would be nice. I'm sure it'll bite someone one day.


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-07-25 04:53:50 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#issuecomment-2249400124  

An automated preview of the documentation is available at [https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html](https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html)  
  
Diff2html diff: [https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html](https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-08-17 14:26:20 UTC  
> Url: https://github.com/boostorg/boostbook/pull/25#issuecomment-2294874507  

An automated preview of the documentation is available at [https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html](https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html)  
  
Diff2html diff: [https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html](https://25.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html)

---
