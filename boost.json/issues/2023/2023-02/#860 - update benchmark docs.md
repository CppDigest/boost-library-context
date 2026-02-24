# #860 - update benchmark docs [Open]

> Username: sdarwin  
> Created at: 2023-02-17 16:01:02 UTC  
> Updated at: 2023-03-23 16:52:19 UTC  
> Url: https://github.com/boostorg/json/issues/860  

I have figured out a script to "update the docs" for json benchmarks. https://github.com/sdarwin/json/tree/update-docs-2  .   However, now hesitant to submit a PR because of how the system is functioning. Currently, in the json/doc/images/ directory are benchmark images consuming 1.3MB. An update to those images will increase the size of the git repository by 1MB, and thus boost by 1MB. Each time the images are updated it happens again.  Maybe json boost docs should display fewer benchmark images?

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-03-01 09:47:37 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1449723373  

What about storing images externally? I know that git-lfs is often used to store binary files. And supposedly GitHub support it. But I've never used it myself.

---

## Comment 2

> Username: sdarwin  
> Created at: 2023-03-01 12:54:55 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1450107193  

git-lfs is an idea. However there is a consideration that the boost documentation is built by automated tools https://github.com/boostorg/release-tools and also sometimes by end users.  It introduces a new requirement to have git-lfs installed in all those cases.    
Another possibility could be to show fewer benchmarks in the docs, and then also link the reader externally to https://benchmark.cppalliance.org/jsonbenchmarks-intel/2023-02-12-17-22-05-33d733d/charts/2023-02-28-05-00-01.html

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-03-02 08:26:21 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1451478015  

Oh, right! We actually could just link to external images from the docs! But for that we need a stable link to latest release's benchmark results.

---

## Comment 4

> Username: sdarwin  
> Created at: 2023-03-02 14:03:35 UTC  
> Updated at: 2023-03-02 14:23:47 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1451914098  

> a stable link to latest release's benchmark results.  
  
The latest release was commit 31b10c1.   Copied to:  
  
https://benchmark.cppalliance.org/jsonbenchmarks-releases/latest.html

---

## Comment 5

> Username: grisumbras  
> Created at: 2023-03-03 13:35:32 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1453543039  

I can't figure out how to integrate it in the docs. There doesn't seem to be a way to generate an iframe in a quickbook document. iframe also looks a bit wierd (second scroll bar). Can we generate the benchmark results as images? SVG should be good enough, I think.

---

## Comment 6

> Username: sdarwin  
> Created at: 2023-03-03 15:48:35 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1453729967  

> Can we generate the benchmark results as images?  
  
Yes. There are currently 28 images. The suggestion I was proposing is to show perhaps 1 or 2 images and then refer the reader externally to the other website, not embed the images in an iframe.

---

## Comment 7

> Username: grisumbras  
> Created at: 2023-03-03 16:04:30 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1453753719  

I don't see the point of showing only a few images, because they represent different tests. Maybe we should only show images for one target (e.g. clang). But if the images are generated any way, why not show all of them?

---

## Comment 8

> Username: sdarwin  
> Created at: 2023-03-03 16:23:38 UTC  
> Updated at: 2023-03-03 16:23:48 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1453778525  

> why not show all of them?    
  
It's possible. The code has been written.  But refer to the original post in this issue about at least a potential "why not".

---

## Comment 9

> Username: sdarwin  
> Created at: 2023-03-03 17:41:17 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1453876676  

Is it against the rules to use javascript in the final html doc output?  If docs are ultimately written to .html files, and those are rendered by browsers, and browsers support javascript.  The benchmarks are originating from https://github.com/boostorg/json/blob/develop/bench/results.html .  That file uses a compact format that renders images dynamically, without png image files.  What if the doc/Jamfile copied `results.html` to the final output directory and it was used directly as part of the docs.

---

## Comment 10

> Username: grisumbras  
> Created at: 2023-03-03 20:55:14 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1454116370  

To be honest, I have no idea how to do that. QuickBook doesn't have an option to pass through to HTML, because it produces docbook. And I couldn't find any kind of pass through tag for docbook.

---

## Comment 11

> Username: sdarwin  
> Created at: 2023-03-03 21:24:30 UTC  
> Updated at: 2023-03-04 01:50:52 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1454148808  

Not sure either.  What if the benchmark docs page is edited to replace the entire section of images and titles with the word "benchmarksplaceholder".  After quickbook and docbook are finished, the resulting page with be mostly correct, except the graphics will be missing.  The page will be installed to the location /html/json/benchmarks.html .    Then, is it possible to have the Jamfile, b2, which is still executing, to run a post-processing step, where it does a find-and-replace such as one-liner bash command `sed`, and fill in the word "benchmarksplaceholder" in `/html/json/benchmarks.html` with the content of `results.html` (or a customized copy of `results.html`)  
  
UPDATE  
  
bash isn't necessarily cross-platform compatible so better choices for the find-and-replace step could be native b2 (if it can do that) or python.

---

## Comment 12

> Username: grisumbras  
> Created at: 2023-03-09 18:06:07 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1462532760  

I discovered a way to embed HTML into the page after all. In #870 I'm using it to show data copied from `bench/results.html`. Now I need a way to get the data for the latest release. It should be an XML file, and it will be fully embedded  into the page. Alternatively, I probably can pull data using AJAX.

---

## Comment 13

> Username: sdarwin  
> Created at: 2023-03-09 20:47:29 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1462780809  

That is fantastic! It solves the main issue. If you manually copy the benchmark results from https://github.com/sdarwin/json/blob/0485ab8af94bd68fa7df92e8864208338b2a3cf7/bench/results.html  , those were recently generated, on a fast server.    At that point, everything is working.    It may not be necessary to add any more features.    
  
> should be an XML file  
  
The format that's already available are these "basicresults" files. Such as  https://benchmark.cppalliance.org/jsonbenchmarks-intel/2023-03-01-12-28-40-4d4d5fc/basicresults/2023-03-08-05-00-02  They are comma delimited and hopefully easy to parse.        
  
Using those has pros and cons.  They are more up-to-date. It creates a dependency on the server. A static html file in the docs is guaranteed to work.  Both choices are options.

---

## Comment 14

> Username: grisumbras  
> Created at: 2023-03-23 12:28:21 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1481109524  

I've just realised that these files lack msvc benchmarks. And as far as I can see, benchmarking  hasn't been done with msvc recently.

---

## Comment 15

> Username: sdarwin  
> Created at: 2023-03-23 16:52:19 UTC  
> Url: https://github.com/boostorg/json/issues/860#issuecomment-1481540782  

The Windows operating system has many proprietary systems processes, that can't be quiesced, and benchmarks showed random variations, green and red, that made the results appear unreliable.  so they were removed from the pull request emails.    
  
In terms of daily benchmarks, that requires keeping a windows benchmark server running full time in the data center.  It was done, for some time, before.
