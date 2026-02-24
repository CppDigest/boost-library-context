# #30 Add appveyor and travis CI configuration files for future pull requests [Closed]

> Username: jeking3  
> Created at: 2017-10-08 12:03:37 UTC  
> Updated at: 2023-07-07 22:17:40 UTC  
> Closed at: 2023-07-07 22:17:39 UTC  
> Url: https://github.com/boostorg/random/pull/30  

### Results ###  
  
Appveyor: https://ci.appveyor.com/project/jeking3/random/build/1.0.18-develop  
Travis: https://travis-ci.org/jeking3/random/builds/286190652  
  
### Known Issues ###  
  
The following builds are commented out because of issues in this or other modules:  
  
| ticket | module | issue |  
| - | - | - |  
| [trac-13248](https://svn.boost.org/trac10/ticket/13248) | random | test_hyperexponential_distribution fails in debug builds on travis CI osx any xcode level |  
| [trac-13249](https://svn.boost.org/trac10/ticket/13249) | test | cygwin build on appveyor fails attempting to use linux calls (still an issue in 1.66.0 with cygwin on appveyor) |  
| [trac-13255](https://svn.boost.org/trac10/ticket/13255) | test | mingw build on appveyor uses undefined vsnprintf |  
| [github-270](https://github.com/boostorg/build/issues/270) | build | cannot use mingw or gcc toolset to build mingw on windows |  
  
These should be investigated, fixed, and tests re-enabled.  
  
### Maintainer Follow-Up ###  
  
To activate these changes, the maintainer of Boost.Random would need to request admin access on github, travis, and appveyor so that they can enable automatic builds on pull requests to the official repository.  
  
I would also recommend a README.md should also be added similar to Boost.Uuid that allows for inspection of the current build status of master and develop branches.

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-10 20:42:46 UTC  
> Url: https://github.com/boostorg/random/pull/30#issuecomment-335601665  

@swatanabe this is ready to merge, and following that if you need any assistance getting the builds running for PRs in the boostorg account, let me know.  I may put a document together on it.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-16 17:14:41 UTC  
> Url: https://github.com/boostorg/random/pull/30#issuecomment-336956947  

@swatanabe anything else you need me to do in order to get this merged?

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-11-01 21:38:59 UTC  
> Url: https://github.com/boostorg/random/pull/30#issuecomment-341251184  

This can evolve to codecov.io support, check out the cool integration they have:  
  
https://github.com/boostorg/format/pull/53

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-12-05 15:44:25 UTC  
> Url: https://github.com/boostorg/random/pull/30#issuecomment-349344218  

@swatanabe anything else you need me to do in order to get this merged?

---

## Comment 5

> Username: swatanabe  
> Created_at: 2017-12-06 01:28:33 UTC  
> Url: https://github.com/boostorg/random/pull/30#issuecomment-349499467  

AMDG  
  
On 12/05/2017 08:44 AM, James E. King, III wrote:  
> @swatanabe anything else you need me to do in order to get this merged?  
>   
  
  There have been some concerns about resource  
limits recently.  Since I don't actually use this,  
I'm not sure that it's worthwhile.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: jeking3  
> Created_at: 2017-12-06 15:47:22 UTC  
> Url: https://github.com/boostorg/random/pull/30#issuecomment-349679879  

I'm confused - you don't want to enable folks submitting pull requests into Boost.Random to be able to self-verify their changes are somewhat decent before a code review?  These changes are not just for you, but also to help folks submit higher quality code on their own.

---
