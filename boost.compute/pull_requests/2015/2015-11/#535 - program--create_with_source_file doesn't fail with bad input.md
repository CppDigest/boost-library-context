# #535 program::create_with_source_file doesn't fail with bad input [Merged]

> Username: olivas  
> Created at: 2015-11-19 01:03:15 UTC  
> Updated at: 2015-11-20 03:40:01 UTC  
> Merged at: 2015-11-20 03:39:58 UTC  
> Closed at: 2015-11-20 03:39:58 UTC  
> Url: https://github.com/boostorg/compute/pull/535  

Calling boost::compute::program::create_with_source_file silently creates a program if the input filename is incorrect.  Even program.build() succeeds.  This patch throws std::ios_base::failure if stream.fail() is true.  This seems preferable to something else failing way downstream because of a typo in the filename.  
  
I added a test, but didn't actually test it since I'm using this outside the standard boost build system.  I think it should work, but it's easy to figure out what it does.  It simply tries to create a program from a source file, where the filename is an empty string.  The test verifies that this throws.

---

## Comment 1

> Username: olivas  
> Created_at: 2015-11-19 01:08:34 UTC  
> Url: https://github.com/boostorg/compute/pull/535#issuecomment-157914363  

Oops. Good catch.  Thanks for that, haahh.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-11-19 21:20:13 UTC  
> Url: https://github.com/boostorg/compute/pull/535#issuecomment-158201371  

IMO ok. You can close PR against master.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-11-20 03:40:01 UTC  
> Url: https://github.com/boostorg/compute/pull/535#issuecomment-158270095  

Looks good, thanks for the fix!

---
