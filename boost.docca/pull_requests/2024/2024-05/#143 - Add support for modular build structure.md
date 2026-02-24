# #143 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-05-01 15:42:01 UTC  
> Updated at: 2024-08-20 23:16:41 UTC  
> Merged at: 2024-08-20 23:16:41 UTC  
> Closed at: 2024-08-20 23:16:41 UTC  
> Url: https://github.com/boostorg/docca/pull/143  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2024-05-01 16:26:27 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2088724188  

Can I merge this or do we have to wait?

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-05-01 16:34:16 UTC  
> Updated_at: 2024-05-01 16:34:34 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2088734817  

We  have to wait until the PR referenced is merged. I'll change this from Draft to Ready when you can merge.

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-06-16 19:30:18 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2171824647  

An automated preview of the documentation is available at [https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-07-16 03:45:13 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2229951068  

An automated preview of the documentation is available at [https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-08-17 14:41:17 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2294878750  

An automated preview of the documentation is available at [https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Review 6 [Commented]

> Username: grisumbras  
> Created_at: 2024-08-20 13:57:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/docca/pull/143#pullrequestreview-2248075149  

This PR adds build.jam, and leaves Jamfile unchanged. Can I remove Jamfile and put its contents into build.jam instead?

📁 build.jam

```diff
   6 |+ require-b2 5.2 ;
   7 |+ 
   8 |+ import project ;
```

> Username: grisumbras  
> Created_at: 2024-08-20 13:52:22 UTC  
> Updated_at: 2024-08-20 13:57:41 UTC  
> Url: https://github.com/boostorg/docca/pull/143#discussion_r1723357399  

What is the purpose of this import?

> Username: grafikrobot  
> Created_at: 2024-08-20 14:10:38 UTC  
> Url: https://github.com/boostorg/docca/pull/143#discussion_r1723390005  

Looks like a mistake.

> Username: grisumbras  
> Created_at: 2024-08-20 14:11:34 UTC  
> Url: https://github.com/boostorg/docca/pull/143#discussion_r1723391826  

Ok.


---

## Comment 7

> Username: grafikrobot  
> Created_at: 2024-08-20 14:14:33 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2298970963  

> This PR adds build.jam, and leaves Jamfile unchanged. Can I remove Jamfile and put its contents into build.jam instead?  
  
Hm.. Looks like I missed that there was a Jamfile. I'll incorporate the equivalent into the build.jam.

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2024-08-20 14:18:08 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2298979236  

@grisumbras okay, I added the test project to the all target. So, yes, you can delete the Jamfile once the build.jam is in.

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-08-20 14:20:17 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2298984224  

An automated preview of the documentation is available at [https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-08-20 14:26:24 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2298998703  

An automated preview of the documentation is available at [https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2024-08-20 18:27:48 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2299482303  

An automated preview of the documentation is available at [https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Comment 12

> Username: grafikrobot  
> Created_at: 2024-08-20 18:31:48 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2299490250  

> @grisumbras okay, I added the test project to the all target. So, yes, you can delete the Jamfile once the build.jam is in.  
  
NM.. I deleted it as part of this PR. Should be good to merge, again.

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2024-08-20 18:42:29 UTC  
> Url: https://github.com/boostorg/docca/pull/143#issuecomment-2299513153  

An automated preview of the documentation is available at [https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://143.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---
