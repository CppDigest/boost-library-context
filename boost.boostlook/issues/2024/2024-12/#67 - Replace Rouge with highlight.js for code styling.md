# #67 - Replace Rouge with highlight.js for code styling [Closed]

> Username: julioest  
> Created at: 2024-12-16 20:50:33 UTC  
> Updated at: 2025-04-14 15:54:32 UTC  
> Closed at: 2025-04-14 15:54:31 UTC  
> Url: https://github.com/boostorg/boostlook/issues/67  

Replace the current Rogue syntax highlighter with highlight.js.   
  
**Note:**  
This affects only libraries that use asciidoc + boostlook

---

## Comment 1

> Username: julioest  
> Created at: 2025-02-11 23:50:04 UTC  
> Url: https://github.com/boostorg/boostlook/issues/67#issuecomment-2652315097  

Hey @sdarwin, we’re running into issues overwriting the library’s source highlighter to highlight.js from the boostlook side. I’ve been using MP11 to test.   
  
We tried setting the asciidoc source highlighter attribute at various levels through b2 (using site-config.jam as well as trying modifying its build.jam ). We also tried through asciidoctor extension (boostlook.rb).   
  
The only solution that worked was CLI command. But, ideally, we don’t authors to run  b2 asciidoctor-attribute=source-highlighter=highlight.js manually. Any idea on how we could do this?   
  
Also, I know this isn’t ideal at all, but we thought we could leverage adding this attribute on the CI side  
https://github.com/boostorg/release-tools/tree/develop/build_docs  
  
cc @daveoconnor

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-02-12 00:17:13 UTC  
> Url: https://github.com/boostorg/boostlook/issues/67#issuecomment-2652346346  

There are multiple ways a library's docs are built in general, for all libs. Consider the most common methods:    
  
- a library author runs `./b2 doc/`   (modifications go in that lib's own Jamfile, or other doc/ files)  
  
- the build_docs scripts you mention install pre-requisites, and then ultimately run `./b2 doc/`  (modifications go in that lib's own Jamfile)  
  
- or, the build_docs scripts run a variation of `doc/build_antora.sh` ( see boostorg/url as an example.  Modifications go in  `doc/build_antora.sh` , or other scripts which are then called.)  
  
- all docs are built for an official release, with `ci_boost_release.py` .    
  
If by "asciidoc + boostlook" you definitely mean "antora + boostlook" , there is an antora-specific section of `ci_boost_release.py`       that is downloading and running scripts from website-v2-docs, which means customizations may well go into website-v2-docs.  
  
If a library author is using "asciidoc + boostlook" , but _not_ "antora + boostlook", then they should modify their own doc/Jamfile or other local doc configuration files.  
  
Which are the exact libraries this applies to, "asciidoc + boostlook" ?

---

## Comment 3

> Username: julioest  
> Created at: 2025-02-12 00:49:24 UTC  
> Updated at: 2025-02-12 00:54:56 UTC  
> Url: https://github.com/boostorg/boostlook/issues/67#issuecomment-2652384823  

Thanks for the insight on the building process.  
  
> If a library author is using "asciidoc + boostlook" , but not "antora + boostlook", then they should modify their own doc/Jamfile or other local doc configuration files.  
  
Right, that was our initial thinking until it was decided to use highlight.js across all lib docs as well.   
  
> Which are the exact libraries this applies to, "asciidoc + boostlook" ?  
  
Not sure of others, but [CharConv](https://www.boost.io/doc/libs/latest/libs/charconv/doc/html/charconv.html) is definitely one.  
  
looping in @rbbeeston

---

## Comment 4

> Username: sdarwin  
> Created at: 2025-02-12 01:05:34 UTC  
> Url: https://github.com/boostorg/boostlook/issues/67#issuecomment-2652407941  

When a snapshot, or official release is built, that uses `ci_boost_release.py` .  
  
If you override the process somehow in `ci_boost_release.py`, then all "published" docs (in the downloads, releases, as well as the website) will have the new configuration.  
  
However, individual libraries, built separately by an author, won't have the change.  If an author is testing their docs locally... it will vary from what is on the website.    
  
"doc previews", are built separately/individually.  They wouldn't include a modification from `ci_boost_release.py`.  
  
Up until now, these all methods corresponded, more or less.   Well, not entirely.  The top html header of the page is/was replaced by the website, on-the-fly.    
  
Ideally, the results of various scripts or methods of building any set of docs are consistent.   Currently, the majority of libraries run b2.   So the results are the same across multiple scripts, which is expected.

---

## Comment 5

> Username: rbbeeston  
> Created at: 2025-02-12 01:19:14 UTC  
> Url: https://github.com/boostorg/boostlook/issues/67#issuecomment-2652422360  

Ultimately the goal is to have a consistent highlighting method on the website, irrespective of what the author uses locally,  regardless of what method to build the docs is used.    
  
If we can implement it in b2 and that covers the majority of cases, I'm willing to call that a win, and hopefully authors would be willing to go that direction, or perhaps mostly be willing to agree to using highlight js as a standard, at least for the site documentation.

---

## Comment 6

> Username: sdarwin  
> Created at: 2025-02-12 14:34:48 UTC  
> Url: https://github.com/boostorg/boostlook/issues/67#issuecomment-2653890127  

This comment is somewhat hypothetical, but if library docs are overridden in `ci_boost_release.py`, then at the same time there should be instructions in the Contributor's Guide explaining how an author can align their local builds to match what is in the release, so they don't remain out-of-sync.  
Julio mentioned a command line flag `b2 asciidoctor-attribute=source-highlighter=highlight.js`. A solution for the authors, and also for an override, should probably not be with a cli flag.   Rather, by editing config files: user-config.jam or Jamfile,  so that it continues to be as easy as just running `./b2` to generate docs.

---

## Comment 7

> Username: rbbeeston  
> Created at: 2025-04-14 15:54:31 UTC  
> Url: https://github.com/boostorg/boostlook/issues/67#issuecomment-2802168781  

using a different solution, no longer needed
