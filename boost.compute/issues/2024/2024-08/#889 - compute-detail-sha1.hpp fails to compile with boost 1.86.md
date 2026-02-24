# #889 - compute/detail/sha1.hpp fails to compile with boost 1.86 [Open]

> Username: bredelings  
> Created at: 2024-08-21 19:32:53 UTC  
> Updated at: 2024-12-12 19:33:53 UTC  
> Url: https://github.com/boostorg/compute/issues/889  

When compiling files that include `<boost/compute/detail/sha1.hpp>`, I see the following error:  
  
```  
/home/bredelings/Work/installed-boost-1.86.0/include/boost/compute/detail/sha1.hpp: In member function ‘boost::compute::detail::sha1::operator std::string()’:  
/home/bredelings/Work/installed-boost-1.86.0/include/boost/compute/detail/sha1.hpp:41:26: error: cannot convert ‘unsigned int [5]’ to ‘unsigned char (&)[20]’  
   41 |             h.get_digest(digest);  
      |                          ^~~~~~  
      |                          |  
      |                          unsigned int [5]  
In file included from /home/bredelings/Work/installed-boost-1.86.0/include/boost/compute/detail/sha1.hpp:18:  
/home/bredelings/Work/installed-boost-1.86.0/include/boost/uuid/detail/sha1.hpp:179:43: note:   initializing argument 1 of ‘void boost::uuids::detail::sha1::get_digest(unsigned char (&)[20])’  
  179 | inline void sha1::get_digest(digest_type& digest)  
      |                              ~~~~~~~~~~~~~^~~~~~  
```  
  
This is because `boost::uuids::detail::sha1::digest_type` has changed from `typedef unsigned int(digest_type)[5];` to `typedef unsigned char digest_type[ 20 ];`.

---

## Comment 1

> Username: nega0  
> Created at: 2024-08-21 19:54:19 UTC  
> Updated at: 2024-08-21 19:54:34 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2302903205  

You'll probably have to cross post this to [boostorg/uuid](https://github.com/boostorg/uuid) and the [boost developers mailing list](https://www.boost.org/community/groups.html#main) in order to get some movement on it.

---

## Comment 2

> Username: bredelings  
> Created at: 2024-08-21 23:40:53 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2303299600  

Fixed by #888

---

## Comment 3

> Username: bredelings  
> Created at: 2024-08-21 23:46:45 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2303304813  

Also fixed (perhaps in a better way) by #887

---

## Comment 4

> Username: nega0  
> Created at: 2024-08-22 02:08:26 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2303539892  

yeah, it's nice that it's in PRs, but it doesn't matter until it's in a proper point release. a new volunteer stepped up this summer to maintain `boost::compute`, but until they're fully on-boarded and these fixes make it into a proper release `boost::compute` v1.86 is dead in the water

---

## Comment 5

> Username: kylelutz  
> Created at: 2024-08-22 03:44:19 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2303615275  

Thanks for the report! https://github.com/boostorg/compute/pull/887 has been merged to `develop`, and will make it into the next release.

---

## Comment 6

> Username: bredelings  
> Created at: 2024-08-22 04:06:46 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2303632844  

That is great.  Can we have a point release to fix this, since it breaks existing software.

---

## Comment 7

> Username: glenfe  
> Created at: 2024-08-22 12:06:42 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2304505443  

@bredelings  would official patches on https://www.boost.org/patches/ against 1.86 for  both UUID and Compute suffice?

---

## Comment 8

> Username: bredelings  
> Created at: 2024-08-22 13:49:16 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2304724558  

Do we need to patch UUID too?  It seemed like patching Compute would be sufficient.  
  
Hmm.  I guess what I would hope is that   
1. official packages from homebrew and Linux distributions like Debian would include the patch.  
2. there is a tarball representing the patched version that I can plug into existing scripts that compile boost during CI.  
3. there is a version number associated with the patched source.  
  
A new point release would accomplish all of these goals, but maybe only (1) is really essential.  Without that all the CI infrastructure using SHA1 from Compute needs to be updated to compile boost 1.85 from scratch.  (2) is helpful so that CI infrastructure that already compiles boost can use the same code for 1.86 as for 1.85 -- download a tarball and compile.  
  
I don't know hard hard spinning a new release is -- for my projects it is mostly automated.  I see on the mailing lists that you all have a lot going on right now.

---

## Comment 9

> Username: bredelings  
> Created at: 2024-08-22 13:50:06 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2304726455  

And obviously a test would be nice, to make sure this doesn't happen again.  But Rome wasn't built in a day...

---

## Comment 10

> Username: bredelings  
> Created at: 2024-08-22 13:58:26 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2304745635  

Also, I tried to join the mailing list yesterday, but haven't been approved yet :-P  I see a thread of discussion about this has already been started though.

---

## Comment 11

> Username: pdimov  
> Created at: 2024-08-22 15:57:04 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2305117363  

> And obviously a test would be nice  
  
The test is here: https://github.com/boostorg/compute/pull/892

---

## Comment 12

> Username: pdimov  
> Created at: 2024-08-22 15:58:21 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2305120012  

> 1. official packages from homebrew and Linux distributions like Debian would include the patch.  
  
They generally do as they maintain a collection of patches anyway. But the issue needs to be reported to them.

---

## Comment 13

> Username: nega0  
> Created at: 2024-08-22 20:14:33 UTC  
> Updated at: 2024-08-22 20:15:06 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2305552289  

@glenfe A point release would be preferred over patch files.  
  
1. Boost's patches aren't checksum'd  
2. https://boost.org/patches/ isn't referenced from anywhere else on the website  
3. Patches are only listed on individual release pages, à la https://www.boost.org/users/history/version_1_85_0.html, and then located inconsistently between versions  
4. Patches aren't even co-located with releases  
5. Patches are not considered "News". They are not on the front page, unlike releases. They are not on the News page, unlike releases.  
  
In other words, patch discovery is terrible. One has to be "in the know" already. Release discovery is easy. One just has to visit https://boost.org.

---

## Comment 14

> Username: glenfe  
> Created at: 2024-08-23 12:29:15 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2306992246  

@nega0 @bredelings We typically do a point release only for critical bugs, or if several libraries are affected and do not build. The release managers discussed this case and voted in favor of a patch.  
  
But you make a good point about patch discovery being bad. We will correct that on https://boost.org and if the https://boost.io layout is adopted, we will make sure that patches are prominently featured there too.  
  
In the meantime, can the relevant commits be merged to master?

---

## Comment 15

> Username: bredelings  
> Created at: 2024-08-23 12:42:31 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2307015629  

I see that Debian/Ubuntu have not included the patch for 1.83, which is a bit worrisome.  I guess this is the result of @nega0's points (4) and (5) above.  
  
@glenfe, what do you think about posting @nega0's message to the boost list?

---

## Comment 16

> Username: nega0  
> Created at: 2024-08-23 13:36:18 UTC  
> Updated at: 2024-08-23 13:37:07 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2307112584  

> @nega0 @bredelings We typically do a point release only for critical bugs, or if several libraries are affected and do not build. The release managers discussed this case and voted in favor of a patch.  
  
That's disappointing.  
   
> But you make a good point about patch discovery being bad. We will correct that on https://boost.org and if the https://boost.io layout is adopted, we will make sure that patches are prominently featured there too.  
  
That's great. Speaking of the website, is the patch vs release decision making process documented there? If not, can it be? A paragraph or two at https://www.boost.org/patches/ would be a good location once its discoverability has been elevated  
  
> @glenfe, what do you think about posting @nega0's message to the boost list?  
  
You're welcome to... I'm subscribed but can't seem to post. That's a "me" issue. Once I have that worked out I can jump into the discussion there.

---

## Comment 17

> Username: glenfe  
> Created at: 2024-08-24 18:27:08 UTC  
> Updated at: 2024-08-24 18:28:23 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2308485826  

@kylelutz After you merge the relevant commits to master, I will create upload a patch against 1.86 to the website and notify everyone.    
  
@nega0 Yes, please post to the Boost developers list. If the sentiment is prominently echoed, it might justify the effort to go beyond a patch.

---

## Comment 18

> Username: thebrandre  
> Created at: 2024-09-27 04:46:16 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2378391376  

Just out of interest: will there be a an official patch on the website as suggested by the conversation in the mailing list *[Boost.Compute broken in 1.86.0 because of UUID changes, 1.86.1?](https://lists.boost.org/Archives/boost/2024/08/257526.php)*?  
  
This would be nice to know! So I can simply wait for that before upgrading boost to 1.86.

---

## Comment 19

> Username: bredelings  
> Created at: 2024-09-29 22:13:00 UTC  
> Updated at: 2024-09-29 22:13:44 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2381629817  

My impression is that there is an official patch.  However, it has been put in a secret place where nobody can find it.

---

## Comment 20

> Username: glenfe  
> Created at: 2024-09-30 00:06:55 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2381663651  

The change in question still hasn't been merged to master, so I have to assume the maintainer of Compute (@kylelutz) does not desire an official patch on the Boost website.

---

## Comment 21

> Username: bredelings  
> Created at: 2024-11-27 22:29:18 UTC  
> Updated at: 2024-11-27 23:33:37 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2504904233  

This issue appears not to be fixed in version 1.87 beta1.  
  
I've sent an e-mail to the mailing list.

---

## Comment 22

> Username: aaronsuydam  
> Created at: 2024-12-12 19:30:07 UTC  
> Updated at: 2024-12-12 19:30:35 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2539846301  

I didn't know this was broken, there wasn't a notice in the documentation. Do we have a workaround for it? I can downgrade from 1.86 if need be but that'd be frustrating.

---

## Comment 23

> Username: bredelings  
> Created at: 2024-12-12 19:31:41 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2539849160  

It looks like the fix will be in 1.87 which will be out soon...

---

## Comment 24

> Username: pdimov  
> Created at: 2024-12-12 19:33:52 UTC  
> Url: https://github.com/boostorg/compute/issues/889#issuecomment-2539852830  

1.87 has just been released.
