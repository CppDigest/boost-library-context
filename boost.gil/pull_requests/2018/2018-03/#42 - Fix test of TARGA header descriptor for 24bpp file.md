# #42 Fix test of TARGA header descriptor for 24bpp file [Merged]

> Username: mloskot  
> Created at: 2018-03-14 20:42:47 UTC  
> Updated at: 2018-03-16 08:26:02 UTC  
> Merged at: 2018-03-15 22:28:16 UTC  
> Closed at: 2018-03-15 22:28:17 UTC  
> Url: https://github.com/boostorg/gil/pull/42  

Ensure that for TARGA Data Type 1, entire descriptor byte is set to 0.  
  
-----  
  
/cc @chhenning, @stefanseefeld   
  
## Issue  
  
According to the TGA Specification, http://www.paulbourke.net/dataformats/tga/, regardless of the Data Type (apart from Data Type 1):  
  
> For the Targa 24, it should be 0.  
  
For Targa 24bpp images, the original of `if(_info._bits_per_pixel == 24 && _info._descriptor != 0 )` is false and skips directly to the following `else` clause which throws `io_error("Unsupported descriptor for targa file")`.  
  
This caused failures of `non_bit_aligned_image_test` in `all_formats_test.cpp`.  
  
## References  
  
Might be related to #33  
  
This also cleans up garbage URL removed in #41

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-03-14 20:48:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/42#pullrequestreview-103992955  

📁 include/boost/gil/extension/io/formats/targa/reader_backend.hpp

```diff
  99 |- 
 100 |-         if(_info._bits_per_pixel == 24 && _info._descriptor != 0 ) 
 103 |+         if (_info._bits_per_pixel == 24)
```

> Username: stefanseefeld  
> Created_at: 2018-03-14 20:48:08 UTC  
> Updated_at: 2018-03-15 19:21:16 UTC  
> Url: https://github.com/boostorg/gil/pull/42#discussion_r174604932  

Being a bit nit-picky, but for clarity this should be an `else if`.

> Username: mloskot  
> Created_at: 2018-03-14 21:08:13 UTC  
> Updated_at: 2018-03-15 19:21:16 UTC  
> Url: https://github.com/boostorg/gil/pull/42#discussion_r174610745  

Good catch.  
  
I also think the following should be test of not the whole descriptor byte but 0-3 bits only as per the TGA spec:  
  
>  Image Descriptor Byte.                                    |  
> Bits 3-0 - number of attribute bits associated with each pixel.  
> For the Targa 16, this would be 0 or   1.  For the Targa 24, it should be 0.  For Targa 32, it should be 8.     
  
```  
        else if (_info._bits_per_pixel == 24)  
        {  
            if ((_info._descriptor & 0x0FU) != 0)  
            {  
```  
  
Am I correct?

> Username: stefanseefeld  
> Created_at: 2018-03-14 21:45:18 UTC  
> Updated_at: 2018-03-15 19:21:16 UTC  
> Url: https://github.com/boostorg/gil/pull/42#discussion_r174619898  

That makes sense to me. But you should definitely put either some explanation or a reference to the appropriate location in the spec above it, so a casual reader can understand it.

> Username: mloskot  
> Created_at: 2018-03-14 22:30:02 UTC  
> Updated_at: 2018-03-15 19:21:16 UTC  
> Url: https://github.com/boostorg/gil/pull/42#discussion_r174630049  

@stefanseefeld sure, I will. TBH, there's plenty of places similar references would be strongly welcomed

> Username: stefanseefeld  
> Created_at: 2018-03-14 22:40:42 UTC  
> Updated_at: 2018-03-15 19:21:16 UTC  
> Url: https://github.com/boostorg/gil/pull/42#discussion_r174632176  

Right, understood. But it's good practice to address those locations that you are looking at anyhow, as that's the moment you know what you are doing... :-)

> Username: mloskot  
> Created_at: 2018-03-14 22:41:40 UTC  
> Updated_at: 2018-03-15 19:21:16 UTC  
> Url: https://github.com/boostorg/gil/pull/42#discussion_r174632334  

Yes, I agree. Done.


---

## Comment 2

> Username: chhenning  
> Created_at: 2018-03-15 12:56:34 UTC  
> Url: https://github.com/boostorg/gil/pull/42#issuecomment-373366484  

@mloskot Are we good here?

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-03-15 13:48:37 UTC  
> Updated_at: 2018-03-15 13:49:56 UTC  
> Url: https://github.com/boostorg/gil/pull/42#issuecomment-373381408  

@chhenning No, we are not. The CI builds are failing.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2018-03-15 14:16:44 UTC  
> Url: https://github.com/boostorg/gil/pull/42#issuecomment-373390701  

At least the AppVeyor failure seemed to be due to some - hopefully transient - connection problem, entirely unrelated to Boost.GIL. I just restarted it, and the above problem appears to be gone.  
But even then, the Windows builds are expected to fail, due to the IO test error I mentioned earlier, entirely unrelated to this PR. This is why I think that fixing that should have priority over any other changes.

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-03-15 15:20:01 UTC  
> Updated_at: 2018-03-15 15:20:53 UTC  
> Url: https://github.com/boostorg/gil/pull/42#issuecomment-373413546  

@stefanseefeld   
> But even then, the Windows builds are expected to fail, due to the IO test error I mentioned earlier, entirely unrelated to this PR.  
> This is why I think that fixing that should have priority over any other changes.  
  
Yes, and no :-)  
  
I build and run all tests locally and  
  
1. Current develop: all tests pass for me on Windwos with VS2017, **but the IO one**  
  
![gil-tests-targa-failing](https://user-images.githubusercontent.com/80741/37472223-dce6c3f0-286b-11e8-9733-3fa69dfc4c55.png)  
  
2. Current develop with this PR-s **ml/targa-fix-descriptor-test** merged:  
  
```  
D:\dev\boost\boost\libs\gil (ml/workshop) git log --pretty=oneline --graph --max-count=3  
*   a9012eed7421c35bb1659322e57c1b610275ea9d (HEAD -> ml/workshop) Merge branch 'ml/targa-fix-descriptor-test' into ml/workshop  
|\  
| * 82f718a6d7c661ebcc747352483a868975b3095d (mloskot/ml/targa-fix-descriptor-test, ml/targa-fix-descriptor-test) Fix test of TARGA header descriptor for 24bpp file  
* | 4221a91319804baf1367b323d25e72a0cd000158 (ml/add-cmake) Add basic CMake configuration for Boost.GIL  
|/  
```  
  
make **all tests pass**  
  
![gil-tests-all-passing](https://user-images.githubusercontent.com/80741/37472356-2a16b7de-286c-11e8-9d54-6b9059e9ec82.png)  
  
I was hoping the TARGA issue is the root of the problem why the IO tests are failing on AppVeyor., https://ci.appveyor.com/project/stefanseefeld/gil/build/1.0.147-develop/job/f46lf6f3aw35vydh#L1244  
  
Unfortunately, it doesn't seem to be and the output is obscure enough to make it unclear what is going wrong. Unless, I just can not see it.

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2018-03-15 15:25:30 UTC  
> Url: https://github.com/boostorg/gil/pull/42#issuecomment-373415644  

Interestingly enough, the original failure you address in this PR didn't even result in a failed test in our CI builds. Nonetheless, it's good to see the failure you observed being fixed. So once the Travis-CI build finishes successfully, I recommend we merge this PR.  
(Would you be able to try reproducing the build process from AppVeyor, i.e. using Boost.Build, and thus hopefully be able to reproduce the other IO failure I keep seeing ?)

---

## Comment 7

> Username: mloskot  
> Created_at: 2018-03-15 15:28:26 UTC  
> Updated_at: 2018-03-15 16:23:46 UTC  
> Url: https://github.com/boostorg/gil/pull/42#issuecomment-373416687  

@stefanseefeld   
> Would you be able to try reproducing the build process from AppVeyor, i.e. using Boost.Build,  
> and thus hopefully be able to reproduce the other IO failure I keep seeing ?  
  
See #45

---

## Comment 8

> Username: mloskot  
> Created_at: 2018-03-15 19:22:19 UTC  
> Updated_at: 2018-03-15 19:22:28 UTC  
> Url: https://github.com/boostorg/gil/pull/42#issuecomment-373493796  

I've updated the PR bringing the #45 fix in

---

## Comment 9

> Username: stefanseefeld  
> Created_at: 2018-03-15 22:28:37 UTC  
> Url: https://github.com/boostorg/gil/pull/42#issuecomment-373543098  

Yay, glad to see CI builds pass again ! May thanks !

---
