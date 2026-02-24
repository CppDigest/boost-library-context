# #56 - Release numbering and tag stability policy [Closed]

> Username: nckx  
> Created at: 2021-12-15 02:03:57 UTC  
> Updated at: 2021-12-15 04:40:22 UTC  
> Closed at: 2021-12-15 04:14:29 UTC  
> Url: https://github.com/boostorg/signals2/issues/56  

Hullo!  
  
[Guix](https://guix.gnu.org) provides a boost-signals2 package.  We noticed that the `boost-1.77.0` tag moved significantly between 2019 and 2021.  
  
Looking back, this was (long) before boost 1.77.0 was actually [released](https://lists.boost.org/boost-announce/2021/08/0593.php).  If so, using that tag so early was premature on our part!  
  
Could you confirm my suspicion  
- that you develop for _upcoming_ Boost releases using a movable tag,  
- but that this tag is not touched once that Boost has been _officially released_ — even to fix bugs found later?  
  
From a brief glance at the repository this seems to be the case, but it would be great if that were a deliberate policy.  
  
Thanks in advance!

---

## Comment 1

> Username: fmhess  
> Created at: 2021-12-15 04:14:29 UTC  
> Url: https://github.com/boostorg/signals2/issues/56#issuecomment-994275793  

I just maintain the boost.signals2 submodule, I don't have anything to do with the release tagging.  Maybe ask Marshall Clow or someone on the Boost Release Team?

---

## Comment 2

> Username: nckx  
> Created at: 2021-12-15 04:22:00 UTC  
> Url: https://github.com/boostorg/signals2/issues/56#issuecomment-994279082  

I'm interested in your policy, not Boost's.  Your [tags](https://github.com/boostorg/signals2/tags) are named `boost-X.Y.Z`.

---

## Comment 3

> Username: nckx  
> Created at: 2021-12-15 04:24:39 UTC  
> Url: https://github.com/boostorg/signals2/issues/56#issuecomment-994280300  

(I don't use Boost myself, so maybe I'm wildly off-base about how any of this works.  It just seems odd that the Boost release team would meddle with your tags?)

---

## Comment 4

> Username: glenfe  
> Created at: 2021-12-15 04:30:37 UTC  
> Url: https://github.com/boostorg/signals2/issues/56#issuecomment-994282741  

The Boost release process creates the tags in each Boost library repository in github.com/boostorg automatically. That said, I don't see how it could have created a 1.77.0 tag before the 1.77.0 release...

---

## Comment 5

> Username: nckx  
> Created at: 2021-12-15 04:38:58 UTC  
> Url: https://github.com/boostorg/signals2/issues/56#issuecomment-994285998  

Oh… OK, interesting way of doing things!  
  
@glenfe: I'm not sure how that would happen either.  Maybe a Guix packager made a mistake.  It's not that important, what really counts is confirmation that tags aren't supposed to move once a release is made :smile:   
  
Thank you both!

---

## Comment 6

> Username: nckx  
> Created at: 2021-12-15 04:40:21 UTC  
> Url: https://github.com/boostorg/signals2/issues/56#issuecomment-994286554  

> confirmation that tags aren't supposed to move  
  
(If you're confused: no, this is not universally accepted, alas…)
