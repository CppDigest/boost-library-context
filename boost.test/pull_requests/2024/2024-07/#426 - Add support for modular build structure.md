# #426 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:25:22 UTC  
> Updated at: 2025-04-07 14:21:41 UTC  
> Merged at: 2025-04-07 14:21:41 UTC  
> Closed at: 2025-04-07 14:21:41 UTC  
> Url: https://github.com/boostorg/test/pull/426  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 15:55:15 UTC  
> Url: https://github.com/boostorg/test/pull/426#issuecomment-2295309011  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: mborland  
> Created_at: 2024-08-19 12:48:27 UTC  
> Url: https://github.com/boostorg/test/pull/426#issuecomment-2296497055  

@grafikrobot Do the modular changes come with an increase in memory usage? The only failure in the CI is OOM with UBSAN that I have not seen before.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2024-08-19 13:58:30 UTC  
> Url: https://github.com/boostorg/test/pull/426#issuecomment-2296649581  

I am seeing a "no space left on device" error.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2024-08-19 13:58:51 UTC  
> Url: https://github.com/boostorg/test/pull/426#issuecomment-2296650396  

> @grafikrobot Do the modular changes come with an increase in memory usage? The only failure in the CI is OOM with UBSAN that I have not seen before.  
  
They don't as they are build system changes only. But if those tests where close to OOM before it's not surprising that it might OOM at any time.

---

## Comment 5

> Username: mborland  
> Created_at: 2024-08-19 14:24:08 UTC  
> Url: https://github.com/boostorg/test/pull/426#issuecomment-2296708500  

> > @grafikrobot Do the modular changes come with an increase in memory usage? The only failure in the CI is OOM with UBSAN that I have not seen before.  
>   
> They don't as they are build system changes only. But if those tests where close to OOM before it's not surprising that it might OOM at any time.  
  
Build system changes could definitely change the memory consumption. Since test has quite a few dependencies are the build sizes all the same? Were build sizes tested before and after modularity to ensure size parity? Is each dependency dragging in copies of all it's dependencies, or is it like how it used to be where there's just one copy of everything?

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2024-09-01 02:56:31 UTC  
> Url: https://github.com/boostorg/test/pull/426#issuecomment-2323138474  

> > > @grafikrobot Do the modular changes come with an increase in memory usage? The only failure in the CI is OOM with UBSAN that I have not seen before.  
> >   
> >   
> > They don't as they are build system changes only. But if those tests where close to OOM before it's not surprising that it might OOM at any time.  
>   
> Build system changes could definitely change the memory consumption. Since test has quite a few dependencies are the build sizes all the same? Were build sizes tested before and after modularity to ensure size parity? Is each dependency dragging in copies of all it's dependencies, or is it like how it used to be where there's just one copy of everything?  
  
I'm still investigating this but one data point since you asked.. It appears the on-disk build sizes with the modular changes is significantly smaller from before (as in for some test builds 14M vs 50M). I'm trying to figure out the memory size. Which is not easy to do given the various programs that running b2 will launch.

---

## Comment 7

> Username: fanquake  
> Created_at: 2025-03-17 04:22:47 UTC  
> Url: https://github.com/boostorg/test/pull/426#issuecomment-2728089098  

@grafikrobot Is there any further update on your investigations here?

---
