# #1 jpeg reading: allow the user to specify scale_num/scale_denum in image_r... [Closed]

> Username: peschmae0  
> Created at: 2014-01-22 10:55:51 UTC  
> Updated at: 2015-11-19 07:45:05 UTC  
> Closed at: 2015-11-19 07:45:05 UTC  
> Url: https://github.com/boostorg/gil/pull/1  

libjpeg downscale images by a factor of n/m during image reading/decompression whenever scale_num and scale_denom are specified in the read options. This pull requests adds parameters to the gil jpeg settings structure to enable the user setting these values.  
  
n/m are integer values, for m = 8, n may be in the range 1...16, the downscaling is very efficient as libjpeg directly accesses the respective coefficent(s) on the 8x8 pixel DCT.

---

## Comment 1

> Username: mloskot  
> Created_at: 2014-01-22 12:04:21 UTC  
> Url: https://github.com/boostorg/gil/pull/1#issuecomment-33015620  

Simon, it's always a good idea to reflect change in API in tests, so perhaps you could add a simple test case for your changes.  
I'm sure @chhenning would agree with that.

---

## Comment 2

> Username: peschmae0  
> Created_at: 2014-01-22 12:39:35 UTC  
> Url: https://github.com/boostorg/gil/pull/1#issuecomment-33017956  

Sure, I'll do that eventually. For the time being I am more looking for comments on whether this is a reasonable approach or if it should be implemented differently  
  
And I was wondering if this is the right place to post this in order to get it merged eventually (chosing the right place for posting patches among boost.org with some svn repos and trac, a sf.net page, github, etc is kind of nonobvious ;-))

---

## Comment 3

> Username: mloskot  
> Created_at: 2014-01-22 13:41:53 UTC  
> Url: https://github.com/boostorg/gil/pull/1#issuecomment-33022449  

From https://svn.boost.org/trac/boost/wiki/StartModPatchAndPullReq  
  
> If you would like to see some change in a Boost library, one approach is to submit a patch. There are two ways to accomplish that - submitting a traditional patch file, or submitting a pull request  
  
So, your patch has landed at the right place.  
  
The only question is if Christian (@chhenning) watches the GIL repo at GitHub and will spot new PRs.  
If you don't hear from Christian here for a while, I'd suggest to send e-mail to Boost developers list (http://lists.boost.org/mailman/listinfo.cgi/boost).

---

## Comment 4

> Username: peschmae0  
> Created_at: 2014-01-22 14:29:36 UTC  
> Url: https://github.com/boostorg/gil/pull/1#issuecomment-33027374  

Great, thanks!

---
