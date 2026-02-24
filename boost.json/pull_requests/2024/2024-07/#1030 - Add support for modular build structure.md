# #1030 Add support for modular build structure. [Closed]

> Username: grafikrobot  
> Created at: 2024-07-20 22:34:44 UTC  
> Updated at: 2024-08-26 12:48:59 UTC  
> Closed at: 2024-08-26 12:48:59 UTC  
> Url: https://github.com/boostorg/json/pull/1030  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-07-20 22:43:51 UTC  
> Url: https://github.com/boostorg/json/pull/1030#issuecomment-2241320972  

An automated preview of the documentation is available at [https://1030.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1030.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-07-20 23:48:27 UTC  
> Url: https://github.com/boostorg/json/pull/1030#issuecomment-2241337441  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1030/pullrequest-condensed-ff9d06a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1030/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1030/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-07-27 16:28:27 UTC  
> Url: https://github.com/boostorg/json/pull/1030#issuecomment-2254193341  

An automated preview of the documentation is available at [https://1030.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1030.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-08-02 04:27:27 UTC  
> Url: https://github.com/boostorg/json/pull/1030#issuecomment-2264522237  

An automated preview of the documentation is available at [https://1030.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://1030.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-08-02 05:33:05 UTC  
> Url: https://github.com/boostorg/json/pull/1030#issuecomment-2264591456  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1030/pullrequest-condensed-89b6b78.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1030/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1030/pullrequest.html)

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2024-08-18 16:06:46 UTC  
> Url: https://github.com/boostorg/json/pull/1030#issuecomment-2295312189  

Please review and merge this PR at your earliest convenience.

---

## Review 7 [Commented]

> Username: grisumbras  
> Created_at: 2024-08-21 04:07:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/1030#pullrequestreview-2249624062  

📁 build.jam

```diff
   6 |+ require-b2 5.2 ;
   7 |+ 
   8 |+ constant boost_dependencies :
```

> Username: grisumbras  
> Created_at: 2024-08-21 03:48:25 UTC  
> Updated_at: 2024-08-21 04:07:33 UTC  
> Url: https://github.com/boostorg/json/pull/1030#discussion_r1724335391  

I don't see this described in [modular Boost README](https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc). Is this constant required, or can I just use this list of libraries directly?

> Username: grafikrobot  
> Created_at: 2024-08-21 14:28:30 UTC  
> Updated_at: 2024-08-21 14:28:31 UTC  
> Url: https://github.com/boostorg/json/pull/1030#discussion_r1725157043  

It was a later addition after I wrote that doc. And was a result of comments from Peter and Adrey. And, yes, you can use the libraries as requirements directly as long as they get set as requirements and usage-requirements. I chose to put it in the top level jam file to put the public dependencies in a clearly visible place.

---

📁 build.jam

```diff
  26 |+ 
  27 |+ explicit
  28 |+     [ alias boost_json : build//boost_json ]
```

> Username: grisumbras  
> Created_at: 2024-08-21 04:00:54 UTC  
> Updated_at: 2024-08-21 04:07:33 UTC  
> Url: https://github.com/boostorg/json/pull/1030#discussion_r1724345158  

Does these have to be aliases? Can't I just move everything in `build/Jamfile` into `build/.jam`?

> Username: grafikrobot  
> Created_at: 2024-08-21 14:35:48 UTC  
> Url: https://github.com/boostorg/json/pull/1030#discussion_r1725169454  

Hm.. Theoretically the build/jamfile logic could move here. But technically, IIRC, it's not possible yet. As the boost-root/status/jamfile interrogates the build/jamfile to figure out what is build-able. After everything is modular we can revisit the boost-root/status/jamfile.

---

📁 build.jam

```diff
  25 |+     ;
  26 |+ 
  27 |+ explicit
```

> Username: grisumbras  
> Created_at: 2024-08-21 04:06:37 UTC  
> Updated_at: 2024-08-21 04:07:33 UTC  
> Url: https://github.com/boostorg/json/pull/1030#discussion_r1724349476  

Do all these targets have to be explicit? Currently the project builds as much as possible by default, and subprojects decide what parts of them have to be explicit.

> Username: grafikrobot  
> Created_at: 2024-08-21 14:29:43 UTC  
> Url: https://github.com/boostorg/json/pull/1030#discussion_r1725159236  

No they don't have to be explicit. What builds by default is up to you.


📁 build/Jamfile

```diff
  77 |       <link>static:<define>BOOST_JSON_STATIC_LINK=1
  78 |       <toolset>msvc:<define>_SCL_SECURE_NO_WARNINGS
  79 |+       <define>BOOST_JSON_NO_LIB=1
```

> Username: grisumbras  
> Created_at: 2024-08-21 03:59:11 UTC  
> Updated_at: 2024-08-21 04:07:33 UTC  
> Url: https://github.com/boostorg/json/pull/1030#discussion_r1724343587  

Why this? Wouldn't it forecfully disable autolinking for users?

> Username: grafikrobot  
> Created_at: 2024-08-21 14:35:36 UTC  
> Updated_at: 2024-08-21 14:35:37 UTC  
> Url: https://github.com/boostorg/json/pull/1030#discussion_r1725169179  

It only disables auto-linking for B2 users. Which don't need auto-linking as B2 will already add the appropriate link options. Auto-linking is only useful (although debatable) for uses that don't use a build system that doesn't add the link options.


---

## Review 8 [Changes requested]

> Username: grisumbras  
> Created_at: 2024-08-22 01:03:23 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/json/pull/1030#pullrequestreview-2252569860  

Also, please rebase on current develop and squash, so that I could merge the PR.

📁 build/Jamfile

```diff
  57 |- project boost/json
  58 |+ project
  59 |+     : common-requirements <library>$(boost_dependencies)
```

> Username: grisumbras  
> Created_at: 2024-08-22 01:02:27 UTC  
> Updated_at: 2024-08-22 01:03:23 UTC  
> Url: https://github.com/boostorg/json/pull/1030#discussion_r1726007404  

Shouldn't this be `<use>`?

> Username: grisumbras  
> Created_at: 2024-08-22 02:38:38 UTC  
> Url: https://github.com/boostorg/json/pull/1030#discussion_r1726161526  

Actually, through local testing I discovered, that I need  
```  
<use>$(boost_dependencies)/<warnings-as-errors>off  
```


---

## Comment 9

> Username: grisumbras  
> Created_at: 2024-08-26 12:48:59 UTC  
> Url: https://github.com/boostorg/json/pull/1030#issuecomment-2310133823  

Merged via #1035

---
