# #47 Adding the new sort library to .gitmodules [Closed]

> Username: spreadsort  
> Created at: 2015-01-22 02:45:04 UTC  
> Updated at: 2015-02-12 04:15:27 UTC  
> Closed at: 2015-02-05 20:30:59 UTC  
> Url: https://github.com/boostorg/boost/pull/47  



---

## Comment 1

> Username: spreadsort  
> Created_at: 2015-01-22 02:45:50 UTC  
> Url: https://github.com/boostorg/boost/pull/47#issuecomment-70963207  

The new sort library is ready to include in master.  We should also add it to the tests, but that'll be a separate pull request.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2015-02-05 20:30:59 UTC  
> Url: https://github.com/boostorg/boost/pull/47#issuecomment-73120844  

We need the libs/sort checkout reference for this to work. Please redo with the missing ref.

---

## Comment 3

> Username: spreadsort  
> Created_at: 2015-02-07 18:44:32 UTC  
> Url: https://github.com/boostorg/boost/pull/47#issuecomment-73376960  

I'm sorry, but I have no idea what a checkout reference is.  libs/sort is merged into develop, and I want to add it to master; what do I need to do to make that happen?

---

## Comment 4

> Username: spreadsort  
> Created_at: 2015-02-11 01:43:13 UTC  
> Url: https://github.com/boostorg/boost/pull/47#issuecomment-73821396  

On Thu Feb 05 2015 at 3:31:06 PM Rene Rivera notifications@github.com  
wrote:  
  
> We need the libs/sort checkout reference for this to work. Please redo  
> with the missing ref.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/boost/pull/47#issuecomment-73120844.  
>   
>  I'm sorry, but I have no idea what a checkout reference is. libs/sort is  
> merged into develop, and I want to add it to master; what do I need to do  
> to make that happen?

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2015-02-11 02:30:11 UTC  
> Url: https://github.com/boostorg/boost/pull/47#issuecomment-73825095  

It should have the commit of the subrepo itself in libs/sort.. Like this one: https://github.com/boostorg/boost/commit/eb3053d06da38aeda854097c903cea2b4f3f8738

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2015-02-11 02:31:34 UTC  
> Url: https://github.com/boostorg/boost/pull/47#issuecomment-73825184  

PS. I don't know how to make that happen from the CLI though. As I did it from the Eclipse GUI. But I suspect all you need to do is: `git add libs/sort` after the adding the submodule.

---

## Comment 7

> Username: spreadsort  
> Created_at: 2015-02-12 02:18:27 UTC  
> Url: https://github.com/boostorg/boost/pull/47#issuecomment-74006059  

On Tue Feb 10 2015 at 9:30:18 PM Rene Rivera notifications@github.com  
wrote:  
  
> It should have the commit of the subrepo itself in libs/sort.. Like this  
> one: eb3053d  
> https://github.com/boostorg/boost/commit/eb3053d06da38aeda854097c903cea2b4f3f8738  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/boost/pull/47#issuecomment-73825095.  
>   
> I have no idea how to link a subproject into boost so that updates to the  
> master version of the subproject get automatically incorporated in the  
> master version of boost.  I only know how to copy a static version in,  
> which seems suboptimal, as I'll need to update it manually every time I  
> update the submodel master version.  Is that the way submodules are  
> supposed to work?  I would appreciate help from a release manager  
> integrating the new Sort library into Boost.  I've read documentation, but  
> I've implemented the steps that don't say they're supposed to be done by a  
> release manager, and I don't know how to do the steps that are supposed to  
> be done by a release manager.

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2015-02-12 04:15:27 UTC  
> Url: https://github.com/boostorg/boost/pull/47#issuecomment-74015004  

Actually I'm now totally confused about the state of this :-) I'll check on  
stuff tomorrow and I'll add things in myself if need be. Git is such a pain  
for submodules :-(  
  
On Wed, Feb 11, 2015 at 8:18 PM, spreadsort notifications@github.com  
wrote:  
  
> On Tue Feb 10 2015 at 9:30:18 PM Rene Rivera notifications@github.com  
> wrote:  
>   
> > It should have the commit of the subrepo itself in libs/sort.. Like this  
> > one: eb3053d  
> > <  
> > https://github.com/boostorg/boost/commit/eb3053d06da38aeda854097c903cea2b4f3f8738  
> >   
> > —  
> > Reply to this email directly or view it on GitHub  
> > https://github.com/boostorg/boost/pull/47#issuecomment-73825095.  
> >   
> > I have no idea how to link a subproject into boost so that updates to the  
> > master version of the subproject get automatically incorporated in the  
> > master version of boost. I only know how to copy a static version in,  
> > which seems suboptimal, as I'll need to update it manually every time I  
> > update the submodel master version. Is that the way submodules are  
> > supposed to work? I would appreciate help from a release manager  
> > integrating the new Sort library into Boost. I've read documentation, but  
> > I've implemented the steps that don't say they're supposed to be done by a  
> > release manager, and I don't know how to do the steps that are supposed to  
> > be done by a release manager.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/boost/pull/47#issuecomment-74006059.  
  
##   
  
-- Rene Rivera  
-- Grafik - Don't Assume Anything  
-- Robot Dreams - http://robot-dreams.net  
-- rrivera/acm.org (msn) - grafikrobot/aim,yahoo,skype,efnet,gmail

---
