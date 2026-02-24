# #20 Remove linking with Boost.System [Closed]

> Username: Lastique  
> Created at: 2019-01-14 18:05:20 UTC  
> Updated at: 2019-06-02 21:37:48 UTC  
> Closed at: 2019-06-02 21:11:58 UTC  
> Url: https://github.com/boostorg/contract/pull/20  

Since Boost.System is header-only now, no need to link with the library.

---

## Comment 1

> Username: lcaminiti  
> Created_at: 2019-01-14 19:20:08 UTC  
> Url: https://github.com/boostorg/contract/pull/20#issuecomment-454127747  

Cool. I will make these changes in the next release of Boost.Contract.

---

## Comment 2

> Username: lcaminiti  
> Created_at: 2019-06-02 16:34:28 UTC  
> Updated_at: 2019-06-02 16:35:11 UTC  
> Url: https://github.com/boostorg/contract/pull/20#issuecomment-498046150  

I checked and as of Boost 1.70 Boost.System DLL is still built. It doesn't look like header-only Boost.System has been released yet so I can't fully tested and for now I won't remove this linkage. This change to Boost.Contract can be made at any time by whom releases Boost.System as header-only in a future Boost release.  
NOTE: In the meanwhile, this explicit linkage is only there when building Boost.Contract as header-only (Boost.Contract's bjam option bc_hdr=only) and that is not the default build mode. This is probably rarely, if ever, used by users.

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-06-02 16:58:32 UTC  
> Url: https://github.com/boostorg/contract/pull/20#issuecomment-498047842  

The header-only Boost.System has been released in Boost 1.69. The built library is a dummy to keep projects that link against Boost.System building. It does not contain any code.  
  
Please reopen. Linking against Boost.System introduces a useless dependency on libboost_system, in terms of libraries and packages.

---

## Comment 4

> Username: lcaminiti  
> Created_at: 2019-06-02 21:11:58 UTC  
> Url: https://github.com/boostorg/contract/pull/20#issuecomment-498065879  

I am making this change in the next release. I didn't just merge this pull request because also some <library>/boost//system needed to be removed from build/Jamfile.v2 so I making all these changes together.

---

## Comment 5

> Username: Lastique  
> Created_at: 2019-06-02 21:37:48 UTC  
> Url: https://github.com/boostorg/contract/pull/20#issuecomment-498067555  

Ok, thanks.

---
