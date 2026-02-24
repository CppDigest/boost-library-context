# #65 Docca jam module [Merged]

> Username: grisumbras  
> Created at: 2021-03-29 17:19:29 UTC  
> Updated at: 2021-04-02 19:47:54 UTC  
> Merged at: 2021-04-02 19:46:55 UTC  
> Closed at: 2021-04-02 19:46:56 UTC  
> Url: https://github.com/boostorg/docca/pull/65  



---

## Comment 1

> Username: grisumbras  
> Created_at: 2021-03-29 17:29:02 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-809567630  

@vinniefalco @evanlenz This is what I have so far.  
I'm planning to investigate whether I can move `index.xml` generation into `docca.jam` too.

---

## Comment 2

> Username: evanlenz  
> Created_at: 2021-03-29 18:08:49 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-809594361  

This looks really promising! And I appreciate the granular commits (at least for interim, pre-squash grokking).  
  
Let me know if you'd also be interested in issue #66, which I just created. I can't remember if there was anything special about the existing docca build output that the visualization build depended on, but I will be anxious to look into this.

---

## Comment 3

> Username: grisumbras  
> Created_at: 2021-03-30 12:54:24 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-810206953  

@vinniefalco So, this is what I ended up with. Check out example/Jamfile for new usage.  
In short, `docca.reference` produces a qbk file from a bunch of C++ headers. The headers (and `<doxygen:param>` properties) are used to generate an intermediary Doxygen config file, then Doxygen is launched, then docca's stylesheets are applied.  
  
The main difference with the status quo is that instead of putting Doxygen configuration into a doxyfile you should use target properties.

---

## Comment 4

> Username: grisumbras  
> Created_at: 2021-03-30 12:57:21 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-810209997  

Oh, one other change could also be made. I can probably b2's type coversion mechanism to allow this:  
```  
qbk reference : xsl/custom-overrides.xsl [ glob sources ] : <doxygen:param>X=Y … ;  
```  
I don't know if there's any need for that, though.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-03-30 17:14:42 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-810433904  

This looks good, and come to think of it, it is fine not to squash it if you want to keep individual commits. I would ask @pdimov to have a look to see if there is anything which is non-idiomatic or would cause problems with the building of releases. You can merge it when you like, but please make sure its a fast-forward and not a merge commit.

---

## Comment 6

> Username: pdimov  
> Created_at: 2021-03-30 17:22:29 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-810438954  

Looks good to me. If it causes any problems there's plenty of time to fix it.

---

## Comment 7

> Username: evanlenz  
> Created_at: 2021-03-30 18:10:14 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-810469943  

I'm not sure if I'm doing something wrong, but I get the following when I try to run b2 in the example directory:  
  
```  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
error: Unable to find file or target named  
error:     '_reference-dir/extract-xml-pages.xsl'  
error: referred to from project at  
error:     '.'  
```  
  
I'm looking forward to seeing this work, because it looks like an awesome improvement to me!

---

## Comment 8

> Username: grisumbras  
> Created_at: 2021-03-30 18:17:07 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-810474471  

@evanlenz just so I could test it, how do you run b2? From what directory, inside boost or not?

---

## Comment 9

> Username: evanlenz  
> Created_at: 2021-03-30 18:21:12 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-810477249  

I run it from the Cygwin command line:  
  
```  
evanl@THINKPAD-T480 ~/boost/tools/docca/example  
$ which b2  
/home/evanl/boost/b2  
  
evanl@THINKPAD-T480 ~/boost/tools/docca/example  
$ b2 -v  
B2 Version 4.3. OS=NT.  
  Copyright 1993-2002 Christopher Seiwald and Perforce Software, Inc.  
  Copyright 2001 David Turner.  
  Copyright 2001-2004 David Abrahams.  
  Copyright 2002-2019 Rene Rivera.  
  Copyright 2003-2015 Vladimir Prus.  
  
  DEFAULTS: jobs = 8  
  
evanl@THINKPAD-T480 ~/boost/tools/docca/example  
$ b2  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
error: Unable to find file or target named  
error:     '_reference-dir/extract-xml-pages.xsl'  
error: referred to from project at  
error:     '.'  
```

---

## Comment 10

> Username: grisumbras  
> Created_at: 2021-03-31 14:49:12 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-811127727  

I  tried building from `example` directory locally, but that error does not happen for me. So, this is possibly due to either Windows or Cygwin. Sadly, I know absolutely nothing about Cygwin. @pdimov I remember you using Cygwin. Can you please test the branch on your machine?

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2021-03-31 18:19:42 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-811305885  

Could this be a problem with relative paths?

---

## Comment 12

> Username: grisumbras  
> Created_at: 2021-03-31 19:16:45 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-811364334  

@evanlenz please pull the updates for the branch and try again.

---

## Comment 13

> Username: evanlenz  
> Created_at: 2021-04-01 04:49:10 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-811635237  

That did the trick. Thank you!

---

## Comment 14

> Username: grisumbras  
> Created_at: 2021-04-01 09:18:53 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-811775725  

Thans to @pdimov for helping me with investigating this bug.

---

## Comment 15

> Username: grisumbras  
> Created_at: 2021-04-01 12:04:37 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-811860532  

So, now that everything is good, I'll squash it and merge into develop?

---

## Comment 16

> Username: evanlenz  
> Created_at: 2021-04-01 14:56:09 UTC  
> Url: https://github.com/boostorg/docca/pull/65#issuecomment-811965130  

It sounded like @vinniefalco was okay with keeping the granular commits. If that’s still true, keeping the granular commits would be my preference. Not that I exactly want to be on the hook for learning bjam, but I did find your commits helpful for learning!

---
