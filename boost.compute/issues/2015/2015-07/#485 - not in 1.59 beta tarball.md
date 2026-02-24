# #485 - not in 1.59 beta tarball? [Closed]

> Username: xnox  
> Created at: 2015-07-20 21:35:06 UTC  
> Updated at: 2016-01-13 19:04:19 UTC  
> Closed at: 2016-01-13 19:04:13 UTC  
> Url: https://github.com/boostorg/compute/issues/485  

looks like it is missing from the 1.59 beta tarball. Did this component "make" it, or not?

---

## Comment 1

> Username: xnox  
> Created at: 2015-07-20 21:35:36 UTC  
> Url: https://github.com/boostorg/compute/issues/485#issuecomment-123050707  

ah:"Boost.Compute is not (yet) an official Boost library" from http://boostorg.github.io/compute/

---

## Comment 2

> Username: jszuppe  
> Created at: 2015-07-20 22:18:33 UTC  
> Url: https://github.com/boostorg/compute/issues/485#issuecomment-123069354  

Boost.Compute "made" it - see https://groups.google.com/forum/#!topic/boost-compute/zuhfo2Fro6g. It's already added on develop branch of boost super-project https://github.com/boostorg/boost. I can't find which version will be the first to include Boost.Compute.

---

## Comment 3

> Username: kylelutz  
> Created at: 2015-07-21 02:54:31 UTC  
> Url: https://github.com/boostorg/compute/issues/485#issuecomment-123133913  

I'm actively working to get Boost.Compute merged into the Boost `master` branch. Currently, I'm waiting on a couple open pull-requests to `boostorg/boost` (see https://github.com/boostorg/boost/pull/86 and https://github.com/boostorg/boost/pull/87) which are neccessary to get Boost.Compute integrated (according to the new library checklist at https://svn.boost.org/trac/boost/wiki/ReleasePractices/ManagerCheckList).

---

## Comment 4

> Username: xnox  
> Created at: 2015-07-24 08:10:14 UTC  
> Url: https://github.com/boostorg/compute/issues/485#issuecomment-124421700  

Cool! So it is in progress =)  
  
> "But look, you found the notice didn't you?"  
> "Yes," said Arthur, "yes I did. It was on display in the bottom of a locked filing cabinet stuck in a disused lavatory with a sign on the door saying 'Beware of the Leopard'."

---

## Comment 5

> Username: kylelutz  
> Created at: 2015-10-24 17:35:33 UTC  
> Url: https://github.com/boostorg/compute/issues/485#issuecomment-150836189  

Quick update, now just waiting on getting https://github.com/boostorg/boost/pull/96 merged.

---

## Comment 6

> Username: jszuppe  
> Created at: 2016-01-07 15:58:46 UTC  
> Url: https://github.com/boostorg/compute/issues/485#issuecomment-169706442  

https://github.com/boostorg/boost/pull/96 merged

---

## Comment 7

> Username: kylelutz  
> Created at: 2016-01-13 19:04:13 UTC  
> Url: https://github.com/boostorg/compute/issues/485#issuecomment-171399390  

The Boost.Compute submodule has been merged into `boostorg/master`, it should appear in the next Boost release (1.61).
