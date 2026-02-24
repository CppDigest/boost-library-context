# #14 Added support for screen origin bit of targa files (Ticket 8429) [Merged]

> Username: martin-osborne  
> Created at: 2015-03-22 09:46:03 UTC  
> Updated at: 2018-04-01 15:16:24 UTC  
> Merged at: 2018-04-01 15:16:03 UTC  
> Closed at: 2018-04-01 15:16:03 UTC  
> Url: https://github.com/boostorg/gil/pull/14  

Currently GIL refuses to load TARGA files whose screen origin is  
in the upper left-hand corner.  See Trac ticket 8429 for sample  
image files created from GIMP 2.

---

## Comment 1

> Username: martin-osborne  
> Created_at: 2015-03-22 09:49:03 UTC  
> Url: https://github.com/boostorg/gil/pull/14#issuecomment-84575746  

I appreciate that the patch contains _magic numbers_.  I wasn't certain if they should be added to the `targa_descriptor` structure or elsewhere.

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-03-16 21:28:53 UTC  
> Url: https://github.com/boostorg/gil/pull/14#issuecomment-373850000  

@chhenning, @stefanseefeld if this enhancement is a good idea, I can check it, add test and make it ready to merge. Please, let me know.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2018-03-16 21:35:42 UTC  
> Url: https://github.com/boostorg/gil/pull/14#issuecomment-373851480  

The idea to support this feature certainly sounds fine. I can't speak to the code itself (as I don't know TARGA at all), but I trust that if we can add a test using a sample image that uses that feature (and the test fails before applying this PR, and succeeds with it), we should be good.  
But please make sure the patch applies cleanly to the develop branch... :-)

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-03-18 10:55:28 UTC  
> Url: https://github.com/boostorg/gil/pull/14#issuecomment-373988779  

I'm not TARGA expert either and I will have to review this change against the spec. It should be doable, I think.  
Yes, test and test image is a must have, in topic  branch based on the develop branch (frequently updated, if necessary), of course.  
  
p.s. PR with new feature or bug fix that does not include proper test case potentially imposes more burden than it helps.

---

## Comment 5

> Username: martin-osborne  
> Created_at: 2018-03-20 08:08:05 UTC  
> Url: https://github.com/boostorg/gil/pull/14#issuecomment-374509194  

Hi,  
  
Thanks for looking into this.    
  
If I understand the conversation correctly, I need to update the pull request with test(s) for @mloskot to review.  I'll set about making those changes.  If I've misunderstood the conversation, or if you think it would be more efficient to do the work yourselves that's fine too.

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-03-20 08:17:19 UTC  
> Updated_at: 2018-03-20 08:17:54 UTC  
> Url: https://github.com/boostorg/gil/pull/14#issuecomment-374511209  

@martin-osborne Thanks for coming back to this.  
  
Actually, I assumed you may no longer be interested and I was going to take over it, add corresponding test case and re-submit as new pull request. If you are willing to do it, that would be preferred.  
  
What I would do is this (@chhenning & @stefanseefeld may want to update the proposed procedure):  
  
```  
git checkout develop  
git pull origin develop  
git checkout ticket_8429  
git rebase develop  
# add relevant test case and test image  
git add -A  
git commit --amend  
git push -f https://github.com/martin-osborne/gil.git ticket_8429  
```  
  
After that, this pull request will receive the necessary update and will get CI-checked.

---

## Comment 7

> Username: martin-osborne  
> Created_at: 2018-04-01 09:59:39 UTC  
> Url: https://github.com/boostorg/gil/pull/14#issuecomment-377775988  

Some additional information:  
  
Each of the xx_ul_origin.tga sample images were created from the corresponding xx.tga using GIMP.  The header of the ul_origin files differ from the source image file header in the following two ways:  
  
1. The "Y Origin of Image" has been set to the image height 0x007c, previously 0x0000.  
2. The screen origin bit has been set, 0x20.  
  
See the end of this post for actual dumps.  
  
As far as I can tell the _y_origin value isn't used anywhere except in targa_read_test.cpp which checks the header of 24BPP_compressed.tga only.  
  
Updating scanline_reader to support images with the screen origin bit set looked to be a significant amount of work, and wasn't something I'd looked into before, so I simply fail the initialization with a helpful error message.  Hope that's okay.  
  
Once again, thanks for taking the time to look into this.  Do let me know if you want me to make further changes.  
  
Martin.  
  
```  
24BPP_compressed.tga  
0000000 0000 000a 0000 0000 0000 0000 007c 007c  
0000010 0018     
  
24BPP_compressed_ul_origin.tga  
0000000 0000 000a 0000 0000 0000 007c 007c 007c  
0000010 2018  
  
  
24BPP_uncompressed.tga  
0000000 0000 0002 0000 0000 0000 0000 007c 007c  
0000010 0018                                     
  
24BPP_uncompressed_ul_origin.tga  
0000000 0000 0002 0000 0000 0000 007c 007c 007c  
0000010 2018                                     
  
  
32BPP_compressed.tga  
0000000 0000 000a 0000 0000 0000 0000 007c 007c  
0000010 0820                                     
  
32BPP_compressed_ul_origin.tga  
0000000 0000 000a 0000 0000 0000 007c 007c 007c  
0000010 2820                                     
  
  
32BPP_uncompressed.tga  
0000000 0000 0002 0000 0000 0000 0000 007c 007c  
0000010 0820                                     
  
32BPP_uncompressed_ul_origin.tga  
0000000 0000 0002 0000 0000 0000 007c 007c 007c  
0000010 2820                                     
```

---

## Comment 8

> Username: chhenning  
> Created_at: 2018-04-01 15:16:23 UTC  
> Url: https://github.com/boostorg/gil/pull/14#issuecomment-377793648  

Thanks Martin!

---
