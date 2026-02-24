# #192 Consider using inline namespaces for versioning [Merged]

> Username: ldionne  
> Created at: 2015-10-21 00:02:45 UTC  
> Updated at: 2016-01-03 19:53:28 UTC  
> Merged at: 2015-11-17 19:30:42 UTC  
> Closed at: 2015-11-17 19:30:42 UTC  
> Url: https://github.com/boostorg/hana/pull/192  

I'm still not really sure of this. In particular, I think one of the goals of this is to be able to use multiple versions of Hana simultaneously. However, that also requires having differently-named directories (?), e.g. everything from Hana version 1.0 should be in `boost/hana/v1`. If inline namespaces only make sense in that case, there's absolutely no chance that I'm going to use them, since this is just too cumbersome IMO.  
  
Morale: I need to get more educated about inline namespaces, or find someone who is. Anyone?

---

## Comment 1

> Username: ldionne  
> Created_at: 2015-11-11 17:59:13 UTC  
> Updated_at: 2015-11-11 17:59:33 UTC  
> Url: https://github.com/boostorg/hana/pull/192#issuecomment-155861913  

For reference, I just asked [a question](http://thread.gmane.org/gmane.comp.lib.boost.devel/264195) on the Boost.Devel mailing list.

---

## Comment 2

> Username: ldionne  
> Created_at: 2015-11-17 14:32:06 UTC  
> Url: https://github.com/boostorg/hana/pull/192#issuecomment-157386643  

I'm not convinced about using inline namespaces yet because we'd have to change the inline namespace essentially on every release, since we can't make the commitment not to change the ABI.  
  
Instead, let's use a macro to open and close Hana's namespace, which will make such a change trivial if we settle on it. If we settle on _not_ providing inline namespaces, then we could always substitute all uses of `BOOST_HANA_NAMESPACE_BEGIN` by the corresponding namespaces.  
  
The reason why I'm introducing those macros right now without knowing if they will be useful is because this PR is huge and it has to be kept up to date with `develop`. Since it touches to a lot of files, there are always a bunch of conflicts. Hence, I want this merged ASAP.

---

## Comment 3

> Username: m-j-w  
> Created_at: 2016-01-03 19:53:28 UTC  
> Url: https://github.com/boostorg/hana/pull/192#issuecomment-168534942  

There's one case where that might come in handy at some point in the future, hopefully in a not too far future: C++17. Then, there could be namespaces that make use of those new features, whether it is for instance fold expressions, or Concepts TS, or Modules TS, or Ranges TS etc. So something like `boost::hana::cpp14` and `boost::hana::cpp17` loaded into `boost::hana` accordingly.  
  
Apart from that, and since you have a header-only library, it might not be that relevant to always ensure a non-breaking ABI, provided the version number itself provides some indication (e.g. no breaks between patch-levels, or perhaps also minor version numbers). After all, you're not providing directly a dynamic library and user code would have to be recompiled anyway.  
However, once having deployed a version with the whole of the boost libraries, breaking changes should always be prominently documented for users to be able to quickly update their code, and that regardless of having reached an official '1.0' version.   
But please, don't consider me as being too educated in that matter. This is merely a personal point of view.

---
