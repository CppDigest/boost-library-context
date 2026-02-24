# #1719 - Allow gzip in zlib port (or steps to implement it) [Open]

> Username: AeroStun  
> Created at: 2019-10-05 23:44:51 UTC  
> Updated at: 2020-03-10 03:24:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1719  

I am currently writing an API giving back JSON data that may be highly redundant, and I wish to offer users the standard `Content-Encoding`s available.  
  
Thanks to the Zlib C++ port included now in Beast, I have successfully allowed `Content-Encoding: deflate`.  
However, I wish to also offer `Content-Encoding: gzip`, especially since not every web client allows deflate.  
So my real question boils down to this: What would it take to port the gzip part of zlib into the Inflate/Deflate streams (beside the obvious window bits +16) ?  
  
This is mainly for my personal project, but I would argue that such a facility would be great in Beast so that people who wish to serve compressed HTTP bodies could do so in gzip just as easily as in deflate, given the C Zlib has support for both already.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-06 02:56:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-538707213  

Great question. The reason I pulled out the gzip, is because I did not have tests for it, and because the websocket stream doesn't need it. Better to avoid having untested, unused code that would eventually fall to bit-rot.  
  
I agree that having gzip would be useful and it should be fairly straightforward to put it back, once you spend a little time understanding how I ported zlib to relatively modern, header-only C++. But first, we need to write tests for what is currently there, and it needs to have good a code coverage percentage. Otherwise, we will not know if we broke anything when adding gzip back (and having tests is its own reward).  
  
@djarek did some work on this: https://github.com/djarek/beast/commits/v265   
  
I think a good course of action is:  
  
1. Review and integrate Damian's changes  
2. Fill in any gaps in the existing test coverage  
3. Try adding gzip support back in, using the original zlib code as a guide  
4. Write tests for gzip  
  
It might also be necessary to apply the more recent mainline zlib commits to beast, this requires porting that C code over to the converted C++ that beast uses. I can answer questions.

---

## Comment 2

> Username: AeroStun  
> Created at: 2019-10-08 20:01:13 UTC  
> Updated at: 2019-10-09 01:20:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-539678354  

Now onto step n°2; I'm searching for existing zlib tests (so far I found the golang one), as well as places in Beast's zlib that need some test cases.  
If you have any tips, I'll gladly take them.  
  
Also, you specified that we may need to apply more recent upstream zlib commits; did you mean that we should do that as part of adding gzip back in, or as a preparation step ?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-08 20:10:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-539682035  

I don't have any tips other than... it might help to get a deep-dive understanding of how huffman coding works. It would be nice to refactor the tests to abstract that compressor/decompressor, so the same tests could be run against zlib.  
  
As for the more recent upstream zlib commits, they should be a preparatory step - ideally after we have a bit better test coverage. Let codecov be the guide:  
  
https://codecov.io/github/boostorg/beast/commit/045dfeb47f6750a940ced9202af0a6331bdcb085  
  
Any tests we can add to reach the uncovered lines is great, since we will have more confidence that we did not break anything when applying the newer upstream commits or when adding back the gzip.

---

## Comment 4

> Username: AeroStun  
> Created at: 2019-10-10 20:44:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-540784391  

Actually, would you prefer the compressor/decompressor abstractions to be based off an abstract base class or to pass templates around ? (i.e., should the abstraction request the abstracted context at runtime or at compile-time)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-10-10 21:27:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-540806631  

We're talking only about test code, I would make it an abstract base class. Because Beast is always on the verge of choking up Travis with the 2GB memory limit and 1-hour compile-time limit. Templates would double the number of instantiations of test code and increase the risk that the CI compile will fail.

---

## Comment 6

> Username: AeroStun  
> Created at: 2019-10-10 21:37:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-540810820  

Okay; good thing I already did that, and was just waiting for your answer to go ahead and apply it on the tests

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-10-10 21:43:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-540812613  

These days I try to avoid templates where possible, and I use "split compilation" (putting function definitions into a separate translation unit) to keep compile times down. That's why I changed `http::basic_parser` to be an abstract base instead of using CRTP. I still use templates when it makes sense to do so, i.e. generic algorithms like `http::write`.

---

## Comment 8

> Username: AeroStun  
> Created at: 2019-10-16 22:10:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-542912590  

I found this while debugging; I added the comments to the enumerators  
Do you remember ever using the order in which these are declared ? (`include/boost/beast/zlib/zlib.hpp`)  
  
```cpp  
/** Flush option.  
*/  
enum class Flush  
{  
    // order matters  
    none,    // #define Z_NO_FLUSH      0  
    block,   // #define Z_BLOCK         5  
    partial, // #define Z_PARTIAL_FLUSH 1  
    sync,    // #define Z_SYNC_FLUSH    2  
    full,    // #define Z_FULL_FLUSH    3  
    finish,  // #define Z_FINISH        4  
    trees    // #define Z_TREES         6  
};  
```  
  
Because as you clearly thought of, `order matters`, however it is not the same order as in zlib (the `block` should be in the 6th place, but is in the 2nd, as shown by the macro values); this made a lot of the tests fail when using upstream zlib.  
I assume this is a bug on Beast's side, but if some code relies on the order you originally wrote (a quick "Search for all usages" did not bring up anything that would look to me like a dependency to the underlying value), well, I better fix my zlib testing wrapper... or fix Beast; I can only let you choose.  
Of course I did try moving `Flush::block` to its normal position, and that fixed all the test errors except three (there were something like 20).  
Nothing is broken when testing Beast.zlib.  
  
By the way, the Beast.zlib vs upstream zlib tests will soon be fully working and ready for review.  
[This Travis build](https://travis-ci.org/AeroStun/beast/builds/598885831) will tell us if I only have three bugs left to fix in these tests, or if there is more that I didn't see on my local setup.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-10-16 22:59:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-542925224  

Beast websocket shouldn't care about the order, I can't remember why I wrote that. I think it is because zlib itself does something dumb with the numeric value. At this point it seems you know more than me.

---

## Comment 10

> Username: AeroStun  
> Created at: 2019-10-17 06:51:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-543032745  

Just found `flush <= *old_flush` in Beast.zlib code  
That does look like something dumb it does with the numeric value, since it completely crashes the ws pmd tests  
I'll hopefully be able to fix the bugs that it triggers by tomorrow

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-10-17 12:49:33 UTC  
> Updated at: 2019-10-17 12:50:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-543158933  

Ah yes... `flush <= *old_flush` that's why I wrote "order matters." I should have put a big comment there.

---

## Comment 12

> Username: AeroStun  
> Created at: 2019-10-23 13:27:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-545443173  

After working on that PR, I do wonder what will be the best way to give the user the ability to choose what headers they want to have (none (raw), zlib, gzip).  
Because right now it seems that we only support raw deflate, and really the way zlib does it in terms of window bits ranges with [8; 15] for zlib, [-15; -8] for raw and [24; 31] for gzip is terrible.  
  
Next step being porting upstream zlib's commits here, we do still have quite some time to think about it, but I thought the question needs to be asked early enough.

---

## Comment 13

> Username: stale[bot]  
> Created at: 2019-11-22 13:52:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-557539821  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-12-08 16:19:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-562965406  

Please email me vinnie.falco@gmail.com I would like to discuss

---

## Comment 15

> Username: vinniefalco  
> Created at: 2019-12-08 16:19:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-562965490  

FYI this is a new library I am working on https://github.com/vinniefalco/json/

---

## Comment 16

> Username: AeroStun  
> Created at: 2020-01-27 02:03:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-578569462  

Currently looking at the upstream zlib commits since the last stable version. There doesn't seem to be much that would make sense to port; mostly gzip file utilities, crc32 (do you mind if I use Boost.CRC for our impl of gzip?), examples, and fixes for bugs that I can't find in your impl.  
There's only [this bugfix commit](https://github.com/madler/zlib/commit/5c44459c3b28a9bd3283aaceab7c615f8020c531) that I'm not exactly sure what's going on so I'll need some time to really see if your impl completely avoids the problem or not. There doesn't seem to be any trivial test for it, but I'll still check if I can make one.  
  
So really my main question now is: at what zlib commit did you stop when porting the deflate code? I assumed that you stopped at the latest stable version of zlib, since it's the one that we are putting our tests through, but lemme know if there is an older range of commits that I should port here before going onto the gzip part.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2020-01-27 05:17:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-578595120  

I stopped at the zlib that is in the repo:  
https://github.com/boostorg/beast/tree/develop/test/extern/zlib-1.2.11  
  
I'd rather not add a dependency for Boost.CRC. We should port the code from ZLib and put a header-only C++ spin on it.  
  
Thanks

---

## Comment 18

> Username: AeroStun  
> Created at: 2020-03-10 00:17:32 UTC  
> Updated at: 2020-03-10 00:17:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-596839559  

Would you like gzip to be backported from ryanjanson/Deflate into this repository so `Accept-Encoding` can use that when it gets to be implemented?

---

## Comment 19

> Username: vinniefalco  
> Created at: 2020-03-10 02:07:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-596864062  

I don't know anything about ryanjanson/Deflate, haven't seen it. First we need to bring the Beast C++ port up to the latest commit in the original zlib by porting each newer commit one at a time. Then we should port the original gzip code from original zlib. Alternatively we can port the gzip from the commit that Beast branched from, and then port each newer commit. Does this make sense?

---

## Comment 20

> Username: AeroStun  
> Created at: 2020-03-10 02:13:30 UTC  
> Updated at: 2020-03-10 02:23:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-596865571  

So you want to backport the commits of madler/zlib:develop ? The latest release of zlib is still what you have branched off of originally  
I should note that the newer zlib commits are mostly fixes on gzip utility code that we wouldn't even use in Beast

---

## Comment 21

> Username: vinniefalco  
> Created at: 2020-03-10 03:16:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-596880590  

Oh? I didn't know that... I have to take a closer look at his repo then. I thought there were some fixes and improvements relevant to all deflate/inflate.

---

## Comment 22

> Username: AeroStun  
> Created at: 2020-03-10 03:24:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1719#issuecomment-596882428  

There are 5 _possibly_ relevant commits out of 47; I did read them back when I extracted Beast.zlib into ryanjanson/Deflate (it's the one linked in the Boost mailing list), but I do not exactly remember what I did out of them, so I'll have to check that real quick, when I wake up tomorrow
