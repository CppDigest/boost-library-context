# #15 Add appveyor and travis build scripts for continuous integration [Merged]

> Username: jeking3  
> Created at: 2017-10-10 22:11:28 UTC  
> Updated at: 2017-10-18 19:24:51 UTC  
> Merged at: 2017-10-11 13:46:38 UTC  
> Closed at: 2017-10-11 13:46:38 UTC  
> Url: https://github.com/boostorg/format/pull/15  

### Results ###  
  
Appveyor: https://ci.appveyor.com/project/jeking3/format/build/1.0.2-develop  
Travis: https://travis-ci.org/jeking3/format/builds/286199980  
  
### Known Issues ###  
  
The following builds are commented out because of issues in this or other modules:  
  
| ticket | module | issue |  
| - | - | - |  
| [13249](https://svn.boost.org/trac10/ticket/13249) | test | cygwin build on appveyor fails attempting to use linux calls |  
| [13255](https://svn.boost.org/trac10/ticket/13255) | test | mingw build on appveyor uses undefined vsnprintf |  
  
These should be investigated, fixed, and tests re-enabled.  
  
### Maintainer Follow-Up ###  
  
Maintainer of Boost.Format needs to request admin access on github, travis, and appveyor so that they can enable automatic builds on pull requests to the official repository.  
  
A readme file should also be added with build results, similar to Boost.Uuid.

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-10-10 22:26:12 UTC  
> Url: https://github.com/boostorg/format/pull/15#issuecomment-335626502  

The travis and appveyor files are much more complicated than the ones I have recently added to other non-CMT libraries which did not have them before. So maybe someone else can comment on whether or not they are valid or not. I do believe we should have travis and appveyor files for all CMT libraries, so I appreciate this effort. A problem, however, is that only the admin can add libraries to be tested using Travis CI, although I believe that is not the case for appveyor, but CMT maintainers do not have admin rights to CMT libraries even if they have write access to those libraries. So even if we get travis and appveyor files for all CMT libraries we will need someone with admin rights for those libraries to setup the travis ( and maybe appveyor ) testing.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-10 22:45:21 UTC  
> Url: https://github.com/boostorg/format/pull/15#issuecomment-335630009  

These are iterations of the files I have already pushed into uuid, winapi, predef, and random.  I asked for maintainer responsibilities on boost format so I could see this through to completion.  I agree with you that sometimes the files look overwhelming or extraneous, but I customize each one to the repository and its needs.  This one is almost identical to the one I submitted for random.  ANY CI is better than no CI.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-10 22:48:15 UTC  
> Url: https://github.com/boostorg/format/pull/15#issuecomment-335630509  

If you think it will be worthwhile I can change the build mechanism to use a script that uses a json or yaml file to describe the matrix to pass to bjam, however on appveyor there are limitations on which build image can support which compiler type and version.  I would love to have a more universal script all repositories can use, but in the interim having any CI is better than none for quality.

---

## Comment 4

> Username: eldiener  
> Created_at: 2017-10-10 22:59:09 UTC  
> Url: https://github.com/boostorg/format/pull/15#issuecomment-335632485  

No, there is no reason to have anything other than a .yml file. I am just not knowledgable enough on the .yml files for travis and appveyor to know whether or not the ones here are correct. My own are largely a copy and simplification of other libaries which already have them, so I can freely admit little expertise in what should be in them for travis and appveyor. That is why I am hoping someone else looks at them. I have no problem merging the PR if told that the .yml files are OK. But if you already have maintainer rights for 'format', you can do so yourself if you like.

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-10-11 01:00:32 UTC  
> Url: https://github.com/boostorg/format/pull/15#issuecomment-335650613  

I am not officially a the maintainer for Boost.Format at this time.  I did email the mailing list about it.

---

## Comment 6

> Username: jeking3  
> Created_at: 2017-10-11 14:45:42 UTC  
> Url: https://github.com/boostorg/format/pull/15#issuecomment-335835190  

Thanks, now just waiting for the maintainer application to be approved and move forward.

---
