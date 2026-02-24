# #83 Implement ability to have "rootless" projects. [Merged]

> Username: grafikrobot  
> Created at: 2015-06-11 20:45:15 UTC  
> Updated at: 2021-10-02 21:19:25 UTC  
> Merged at: 2018-12-13 20:13:53 UTC  
> Closed at: 2018-12-13 20:13:53 UTC  
> Url: https://github.com/boostorg/build/pull/83  

With this it is no longer an error to not have a jamroot file when loading a project. Instead the  
top-most jamfile project will operate as the jamroot.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-01-09 01:50:30 UTC  
> Url: https://github.com/boostorg/build/pull/83#issuecomment-356154558  

I think this is a good idea, but we need to make sure that it's actually safe.  Jamroot has some obscure differences from regular Jamfiles.  Here are the ones that I'm aware of off the top of my head:  
- Jamroot is required to have project-id when --build-dir is passed on the command line  
- Only Jamroot can have a build-dir when --build-dir is passed.

---

## Comment 2

> Username: swatanabe  
> Created_at: 2018-10-18 16:00:39 UTC  
> Url: https://github.com/boostorg/build/pull/83#issuecomment-431065765  

AMDG  
  
- In project-test3, I'd prefer to remove the initial test,  
  as it no longer does anything useful.  
- Can we have a more descriptive name than project-test5?  
- I'm not convinced that basing the test off of project-test3/4  
  is useful.  Those are primarily testing the behavior of properties,  
  which is only tangentially related to what you're testing  
  here.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2018-10-24 17:59:52 UTC  
> Url: https://github.com/boostorg/build/pull/83#issuecomment-432765356  

@swatanabe I wrote totally different tests. Any thoughts?

---

## Comment 4

> Username: swatanabe  
> Created_at: 2018-10-25 16:00:05 UTC  
> Url: https://github.com/boostorg/build/pull/83#issuecomment-433108512  

AMDG  
  
On 10/24/2018 11:59 AM, Rene Rivera wrote:  
> @swatanabe I wrote totally different tests. Any thoughts?  
>   
  
Looks good, although I personally prefer to keep  
everything in the .py file instead of creating  
a separate directory.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2018-11-18 19:51:46 UTC  
> Url: https://github.com/boostorg/build/pull/83#issuecomment-439720420  

@swatanabe this is now as final as it's going to get. I.e. docs are adjusted to match the feature. Okay to merge?

---
