# #28 msvc: allow dlls without lib file [Closed]

> Username: bernhard-b  
> Created at: 2014-08-04 18:20:18 UTC  
> Updated at: 2021-10-02 20:59:13 UTC  
> Closed at: 2018-01-23 03:51:50 UTC  
> Url: https://github.com/boostorg/build/pull/28  

On Windows it is possible to create a dll consisting only of a resource (message) file which is used for the eventlog. Because it doesn't export any function the msvc linker doesn't create a lib file. Due to that boost build always thinks that the dll needs to be build. i have registered the generator twice with the feature suppress-import-lib (there is no use/doc of it) and only generate the IMPORT_LIB target for one. i haven't figured out another way to achieve that.  
What is missing that /NOENTRY needs to be passed to the linker. The pull request is to gain ideas/comments how to fully implement the feature for dlls without any exports.  
regards,  
bernhard

---

## Comment 1

> Username: bernhard-b  
> Created_at: 2014-08-25 17:57:23 UTC  
> Url: https://github.com/boostorg/build/pull/28#issuecomment-53301016  

that helps. i have updated the file.

---

## Comment 2

> Username: vprus  
> Created_at: 2014-08-25 18:23:55 UTC  
> Url: https://github.com/boostorg/build/pull/28#issuecomment-53306100  

Is there ever a case where we want to create a DLL that has executable code (so /NOENTRY would be wrong) but does not produce an import library either?

---

## Comment 3

> Username: bernhard-b  
> Created_at: 2014-08-30 14:53:23 UTC  
> Url: https://github.com/boostorg/build/pull/28#issuecomment-53960534  

There is no use case for this, as static code would run before the dll is loaded into the process and specifying NOENTRY disables the function, which get's called by LoadLibrary. So in my opinion only resource/message dlls befinit from this feature.

---

## Comment 4

> Username: bernhard-b  
> Created_at: 2014-08-30 14:58:33 UTC  
> Url: https://github.com/boostorg/build/pull/28#issuecomment-53960659  

About the feature name i can add no-import-lib. Does this better describe the feature? The NOENTRY flag is needed so the linker doesn't try to search for the function, which is called by LoadLibrary ( http://msdn.microsoft.com/en-us/library/windows/desktop/ms682583%28v=vs.85%29.aspx ).

---

## Comment 5

> Username: KayEss  
> Created_at: 2014-12-10 10:35:22 UTC  
> Url: https://github.com/boostorg/build/pull/28#issuecomment-66432765  

Aren't there two use cases here?  
1. A DLL that contains resources and no code  
2. A DLL that contains code (and possibly resources) but doesn't export anything  
  
The second case is common with plug in architectures where objects register themselves with some registry that they import from another DLL:  
  
```  
const class plugin : public plugin_base {  
    plugin()  
    : plugin_base("mine") {  
    }  
}  c_plugin_registration;  
```  
  
Does always adding `/NOENTRY` still break this second use? Right now I'm getting around this by exporting a junk `int`.

---

## Comment 6

> Username: swatanabe  
> Created_at: 2018-01-23 03:51:50 UTC  
> Url: https://github.com/boostorg/build/pull/28#issuecomment-359668318  

This appears to have been merged, but was never closed.

---

## Comment 7

> Username: pdimov  
> Created_at: 2021-09-07 13:20:28 UTC  
> Url: https://github.com/boostorg/build/pull/28#issuecomment-914300927  

FWIW, as mentioned, using `/NOENTRY` breaks building python extensions such as the MPI one, because they use `<suppress-import-lib>true` because they don't need to export anything, but are not resource DLLs. See https://github.com/boostorg/build/issues/734.

---

## Comment 8

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:11 UTC  
> Url: https://github.com/boostorg/build/pull/28#issuecomment-932819600  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
