# #534 program::create_with_source_file doesn't fail with bad input [Closed]

> Username: olivas  
> Created at: 2015-11-18 23:34:47 UTC  
> Updated at: 2015-11-20 03:40:32 UTC  
> Closed at: 2015-11-20 03:40:32 UTC  
> Url: https://github.com/boostorg/compute/pull/534  

Calling boost::compute::program::create_with_source_file silently creates a program if the input filename is incorrect.  Even program.build() succeeds.  This patch throws std::ios_base::failure if stream.fail() is true.  This seems preferable to something else failing way downstream because of a typo in the filename.  
  
I added a test, but didn't actually test it since I'm using this outside the standard boost build system.  I think it should work, but it's easy to figure out what it does.  It simply tries to create a program from a source file, where the filename is an empty string.  The test verifies that this throws.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-11-18 23:48:08 UTC  
> Url: https://github.com/boostorg/compute/pull/534#issuecomment-157901118  

Hi, I will check and test it tomorrow. Could you make this pull request against the `develop` branch?

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-11-18 23:55:41 UTC  
> Url: https://github.com/boostorg/compute/pull/534#issuecomment-157902427  

Also, check clang build of your pull request on Travis (don't worry about g++ internal compiler error, it's a recurrent problem).

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-11-20 03:40:32 UTC  
> Url: https://github.com/boostorg/compute/pull/534#issuecomment-158270185  

Closed in favor of PR #535.

---
