# #28 Add GHA workflow for generating diff for affected HTML files [Closed]

> Username: ashtum  
> Created at: 2024-05-15 12:56:06 UTC  
> Updated at: 2024-05-29 16:22:52 UTC  
> Closed at: 2024-05-25 18:27:17 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28  

This is a draft PR that generates a diff from HTML files after building boostbook/doc and compares it with the same build from the target branch.  
  
Currently, the generated diff file is accessible in the GHA artifacts section. We will utilize a bot to send a message containing a link to the diff pages in the PR discussion.  
  
Please note changes to `xsl/html-base.xsl` is for demonstration.

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-05-15 13:37:08 UTC  
> Updated_at: 2024-05-15 13:43:46 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2112560047  

1. If this is a draft PR, please convert it to a draft, so that it is obvious it should not be merged yet.  
2. Please install docbook-xsl and docbook-dtd from [Ubuntu packages](https://github.com/boostorg/boostbook/blob/8883b9b3186d71585fbb1ea07ce8b8fcf1fa1a94/.github/workflows/ci.yml#L38-L39). I would like to minimize the dependencies on downloads from external sources. Especially, unverified ones.  
3. I would like us to avoid using `actions/checkout` and [download a source snapshot](https://github.com/boostorg/boostbook/blob/8883b9b3186d71585fbb1ea07ce8b8fcf1fa1a94/.github/workflows/ci.yml#L82-L90) from GitHub instead. This makes sure that we are not affected by `actions/checkout` obsolescence and issues like [this](https://github.com/actions/checkout/issues/1590). I can see you're also using `actions/upload-artifact`, and I'm not sure if this can be similarly replaced with curl, but if it is possible, please do.

---

## Comment 2

> Username: ashtum  
> Created_at: 2024-05-15 15:57:36 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2112923311  

> Please install docbook-xsl and docbook-dtd from [Ubuntu packages](https://github.com/boostorg/boostbook/blob/8883b9b3186d71585fbb1ea07ce8b8fcf1fa1a94/.github/workflows/ci.yml#L38-L39). I would like to minimize the dependencies on downloads from external sources. Especially, unverified ones.  
  
I've updated the script to use Ubuntu packages, By the way, my earlier attempt was to use the same dependencies as those used in the boost-release script: https://github.com/boostorg/release-tools/blob/develop/ci_boost_release.py#L378-L396.  
  
> I would like us to avoid using actions/checkout and [download a source snapshot](https://github.com/boostorg/boostbook/blob/8883b9b3186d71585fbb1ea07ce8b8fcf1fa1a94/.github/workflows/ci.yml#L82-L90) from GitHub instead. This makes sure that we are not affected by actions/checkout obsolescence and issues like https://github.com/actions/checkout/issues/1590. I can see you're also using actions/upload-artifact, and I'm not sure if this can be similarly replaced with curl, but if it is possible, please do.  
  
I believe ultimately we would run these scripts on the server where the bot is running, so it has access to the generated diff files.

---

## Review 3 [Commented]

> Username: Lastique  
> Created_at: 2024-05-15 16:17:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostbook/pull/28#pullrequestreview-2058446811  

📁 .github/workflows/generate-diff.yml

```diff
  16 |+ env:
  17 |+   DOCBOOK_DTD_DIR: /opt/docbook-dtd
  18 |+   DOCBOOK_XSL_DIR: /opt/docbook-xsl
```

> Username: Lastique  
> Created_at: 2024-05-15 16:01:50 UTC  
> Updated_at: 2024-05-15 16:17:09 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#discussion_r1601905291  

These two variables are no longer needed, are they?

---

📁 .github/workflows/generate-diff.yml

```diff
  48 |+       - name: Create user-config.jam
  49 |+         run: |
  50 |+           echo "using boostbook : /usr/share/xml/docbook/stylesheet/docbook-xsl : /usr/share/sgml/docbook/dtd/xml/4.2 ;" > ~/user-config.jam
```

> Username: Lastique  
> Created_at: 2024-05-15 16:03:32 UTC  
> Updated_at: 2024-05-15 16:17:09 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#discussion_r1601907543  

Docbook paths aren't needed, Boost.Build will automatically find the system-installed stylesheets.

---

📁 .github/workflows/generate-diff.yml

```diff
  66 |+           rm -f "${GIT_MODULE}-${GITHUB_SHA}.tar.gz"
  67 |+           cd ../boost-root
  68 |+           rm -rf "tools/boostbook"
```

> Username: Lastique  
> Created_at: 2024-05-15 16:06:40 UTC  
> Updated_at: 2024-05-15 16:17:09 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#discussion_r1601911755  

`$GIT_MODULE` is `boostbook` in this repository, so `$GIT_MODULE` should be used for consistency here and below.

> Username: ashtum  
> Created_at: 2024-05-15 16:24:20 UTC  
> Updated_at: 2024-05-15 16:24:21 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#discussion_r1601934258  

Assuming someone fork the repository to `someone/my-boostbook` then it would be copied to `tools/my-boostbook`, wouldn't it?

> Username: Lastique  
> Created_at: 2024-05-15 16:29:56 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#discussion_r1601941260  

Ah, right, that's a fair point. Though we generally don't concern ourselves with this case, and there are plenty libraries in Boost that assume the git repository is named the same as the library in the Boost tree.  
  
I guess, you can keep the filesystem paths as they are then.

---

📁 .github/workflows/generate-diff.yml

```diff
  72 |+           mv ./html /tmp
  73 |+           cd ../../../../snapshot
  74 |+           curl -L --retry 5 -o "${GIT_MODULE}-develop.tar.gz" "https://github.com/${GITHUB_REPOSITORY}/archive/develop.tar.gz"
```

> Username: Lastique  
> Created_at: 2024-05-15 16:16:07 UTC  
> Updated_at: 2024-05-15 16:17:09 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#discussion_r1601924176  

The target branch may not be `develop`, it can be deduced like [here](https://github.com/boostorg/boostbook/blob/8883b9b3186d71585fbb1ea07ce8b8fcf1fa1a94/.github/workflows/ci.yml#L70-L76).


---

## Comment 4

> Username: Lastique  
> Created_at: 2024-05-15 16:20:20 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2112968635  

> > I would like us to avoid using actions/checkout and [download a source snapshot](https://github.com/boostorg/boostbook/blob/8883b9b3186d71585fbb1ea07ce8b8fcf1fa1a94/.github/workflows/ci.yml#L82-L90) from GitHub instead. This makes sure that we are not affected by actions/checkout obsolescence and issues like [actions/checkout#1590](https://github.com/actions/checkout/issues/1590). I can see you're also using actions/upload-artifact, and I'm not sure if this can be similarly replaced with curl, but if it is possible, please do.  
>   
> I believe ultimately we would run these scripts on the server where the bot is running, so it has access to the generated diff files.  
  
I'm not sure I understand this comment. This script (specifically, the `actions/upload-artifact` part) will be run on the GHA instance, as long as it is part of the CI config. Are you saying you're going to remove it from the config at some point?

---

## Comment 5

> Username: ashtum  
> Created_at: 2024-05-15 16:53:07 UTC  
> Updated_at: 2024-05-15 16:57:58 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2113025992  

> I'm not sure I understand this comment. This script (specifically, the `actions/upload-artifact` part) will be run on the GHA instance, as long as it is part of the CI config. Are you saying you're going to remove it from the config at some point?  
  
If we decide to use bots to get a link to the diff.html page (uploaded on a web server), there's a good chance we won't need to run these scripts as part of CI at all; it will be part of the bot script itself.

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-05-24 20:23:43 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2130304574  

An automated preview of the documentation is available at [https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html](https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html)  
  
Diff2html diff: [https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html](https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-05-24 20:48:44 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2130332475  

An automated preview of the documentation is available at [https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html](https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html)  
  
Diff2html diff: [https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html](https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2024-05-25 04:58:47 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2130768219  

An automated preview of the documentation is available at [https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html](https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html)  
  
Diff2html diff: [https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html](https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-05-25 18:23:41 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2131384428  

An automated preview of the documentation is available at [https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html](https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/index.html)  
  
Diff2html diff: [https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html](https://28.boostbook.prtest2.cppalliance.org/tools/boostbook/doc/html/diff.html)

---

## Comment 10

> Username: ashtum  
> Created_at: 2024-05-25 18:27:17 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2131385246  

Closing this PR as we now receive diff previews from cppalliance-bot.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2024-05-29 13:46:41 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2137459133  

@Lastique You write:  
  
> Please install docbook-xsl and docbook-dtd from [Ubuntu packages](https://github.com/boostorg/boostbook/blob/8883b9b3186d71585fbb1ea07ce8b8fcf1fa1a94/.github/workflows/ci.yml#L38-L39). I would like to minimize the dependencies on downloads from external sources. Especially, unverified ones.  
  
If this is not documented we should add it somewhere. Otherwise, ignore :)

---

## Comment 12

> Username: Lastique  
> Created_at: 2024-05-29 14:11:36 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2137517967  

Not sure what needs to be documented. This is common sense - minimize external sources and software, use verified sources and packages.

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2024-05-29 16:17:50 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2137799715  

> Not sure what needs to be documented.  
  
We might document the URL for where to obtain the correct, verified packages

---

## Comment 14

> Username: Lastique  
> Created_at: 2024-05-29 16:22:52 UTC  
> Url: https://github.com/boostorg/boostbook/pull/28#issuecomment-2137809024  

Just use the Ubuntu packages whenever possible.  
  
If you must download from external sources then at least hard-code the checksum in the script so that we know the downloaded files are genuine.

---
