# #147 Add test to verify headers are self-contained [Merged]

> Username: mloskot  
> Created at: 2018-10-03 18:33:33 UTC  
> Updated at: 2018-12-12 08:48:27 UTC  
> Merged at: 2018-10-05 19:35:16 UTC  
> Closed at: 2018-10-05 19:35:17 UTC  
> Url: https://github.com/boostorg/gil/pull/147  

For each header, a translation unit is generated along with corresponding compile target.  
Currently, only `boost/gil/*.hpp` and `boost/gil/io/* headres` are included.  
  
On CI services, compile self_contained_headers targets only if `TEST_HEADERS` environment variable is set. This is to avoid build timeouts due to CI services limits.  
When running b2 locally, the tests are compiled by default.  
  
-----  
  
NOTE: The self-contained headers tests are built by default. There will be compilation errors due to actual 'broken' headers! Obviously, those need to be fixed via dedicated PRs/commits.  
  
### References  
  
* #148   
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-10-03 18:42:17 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/147#pullrequestreview-161306194  

I'm not sure to fully understand the (Jamfile) logic, but conceptually this looks good to me. So, trusting that the CI will catch any issues, I'm going to approve this.

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-10-03 18:46:43 UTC  
> Url: https://github.com/boostorg/gil/pull/147#issuecomment-426753050  

Feel free to ask specific questions about that script. I'll do my best to explain.

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-10-03 19:48:29 UTC  
> Url: https://github.com/boostorg/gil/pull/147#issuecomment-426775247  

A sample of errors caught by the two CI-s:  
  
* https://ci.appveyor.com/project/stefanseefeld/gil/build/1.0.483-develop/job/1nf3rwqa5qn3nwc1  
  
```  
C:\projects\boost\boost/gil/io/bit_operations.hpp(28):   
    error C2061: syntax error: identifier 'byte_t'  
```  
  
* https://travis-ci.org/boostorg/gil/jobs/436780478  
  
```  
./boost/gil/premultiply.hpp: In member function ‘void boost::gil::premultiply::operator()(const SrcP&, DstP&) const’:  
./boost/gil/premultiply.hpp:59:3:   
    error: ‘for_each’ is not a member of ‘boost::mpl’  
        mpl::for_each<src_colour_channels>(channel_premultiply<SrcP, DstP>(src, dst));  
       ^  
```

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-10-05 09:14:15 UTC  
> Url: https://github.com/boostorg/gil/pull/147#issuecomment-427298626  

The new test require more time from the CI jobs, while the allowance is limited, eg.  
https://travis-ci.org/boostorg/gil/builds/437264162  
  
My plan is to:  
  
- Improve `Jamfile` to make the `self_contained_headers` test built on demand only via explicit `b2 <target>` request or dedicated environment variable  
- Update CI builds to run the headers test only during single job, eg. GCC 5

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-10-05 16:14:27 UTC  
> Url: https://github.com/boostorg/gil/pull/147#issuecomment-427419739  

Done in https://github.com/boostorg/gil/pull/147/commits/9dc67eae1d6dbc8b33bc8c825e26ead9cd0af8a4  
  
> On CI services, compile self_contained_headers targets only if TEST_HEADERS environment variable is set. This is to avoid build timeouts due to CI services limits. When running b2 locally, the tests are compiled by default.

---
