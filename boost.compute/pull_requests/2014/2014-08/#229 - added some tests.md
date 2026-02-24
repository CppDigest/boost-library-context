# #229 added some tests [Closed]

> Username: f-koehler  
> Created at: 2014-08-11 10:04:20 UTC  
> Updated at: 2014-11-24 15:17:53 UTC  
> Closed at: 2014-11-24 15:17:53 UTC  
> Url: https://github.com/boostorg/compute/pull/229  

Added tests for `context_error` and `unsupported_extension`.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-08-11 10:19:38 UTC  
> Url: https://github.com/boostorg/compute/pull/229#issuecomment-51763057  

[![Coverage Status](https://coveralls.io/builds/1067757/badge)](https://coveralls.io/builds/1067757)  
  
Coverage increased (+0.16%) when pulling **78615d27b220f63ecc6673930fbbe936c9609cfe on f-koehler:tests** into **bd427b8a1bdb8564f0438d44bbdbb40bf3fce4f6 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-08-12 03:06:10 UTC  
> Url: https://github.com/boostorg/compute/pull/229#discussion_r16093619  

i'd usually name these something like `exception.context_error` based on where they are in the source tree (`boost/compute/exception/`). this allows you to use ctest to run only specific sets of test for specific parts of the code, which can be useful when testing new changes. for example, currently you can run all of the exception tests by doing `ctest -R exception.*` (see the `exception.opencl_error` line below).

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-08-12 03:06:58 UTC  
> Url: https://github.com/boostorg/compute/pull/229#discussion_r16093632  

this one would be `TestContextError` (else the report from boost.test will be a little off)

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-08-12 03:07:37 UTC  
> Url: https://github.com/boostorg/compute/pull/229#discussion_r16093645  

this one would be `TestUnsupportedExtension`

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-08-12 03:07:45 UTC  
> Url: https://github.com/boostorg/compute/pull/229#discussion_r16093649  

cool!

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-08-12 03:07:55 UTC  
> Url: https://github.com/boostorg/compute/pull/229#discussion_r16093654  

cool!

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-08-12 03:13:26 UTC  
> Url: https://github.com/boostorg/compute/pull/229#discussion_r16093777  

hmm, i'm having some trouble running this line, does it work for you? i think it's failing for me because there is no active OpenGL context to set up the OpenCL sharing context with. i think we first would have to make an OpenGL context (perhaps by creating a `QGLWidget` and displaying it). this is a bit more work (which is why i haven't gotten to it before), but it would be really good to start testing the OpenGL interop code.

---

## Comment 8

> Username: kylelutz  
> Created_at: 2014-08-12 03:57:33 UTC  
> Url: https://github.com/boostorg/compute/pull/229#issuecomment-51870965  

Left a few inline comments. The OpenGL issue may be a little hard to solve. Thanks for working on these!

---

## Comment 9

> Username: kylelutz  
> Created_at: 2014-11-20 06:17:19 UTC  
> Url: https://github.com/boostorg/compute/pull/229#issuecomment-63765915  

@f-koehler These are good changes and I think integrating the tests for `context_error` and `unsupported_extension_error` would be great to have (however, getting the OpenGL stuff to work for automated testing is a bit more difficult).  
  
If you have the time would you mind updating this pull request with just those tests? Otherwise I should be able to make the changes and merge it in for you. Let me know. Thanks!

---

## Comment 10

> Username: f-koehler  
> Created_at: 2014-11-23 13:48:21 UTC  
> Url: https://github.com/boostorg/compute/pull/229#issuecomment-64118409  

I had much other stuff the past months so I did not have any time for this project. I hope I can dive back in tomorrow. If I do not find the time I will let you know.

---

## Comment 11

> Username: f-koehler  
> Created_at: 2014-11-24 15:17:53 UTC  
> Url: https://github.com/boostorg/compute/pull/229#issuecomment-64207965  

See [PR 311](https://github.com/kylelutz/compute/pull/311). Did not add the interop code back in as I did not like what I wrote some months ago.

---
