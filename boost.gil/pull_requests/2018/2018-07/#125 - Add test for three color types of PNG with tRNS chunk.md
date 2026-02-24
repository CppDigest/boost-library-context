# #125 Add test for three color types of PNG with tRNS chunk [Merged]

> Username: mloskot  
> Created at: 2018-07-31 17:13:07 UTC  
> Updated at: 2019-08-07 20:33:31 UTC  
> Merged at: 2019-08-07 20:33:21 UTC  
> Closed at: 2019-08-07 20:33:22 UTC  
> Url: https://github.com/boostorg/gil/pull/125  

The tests cover issue #117 and pull request #118, extending the coverage  
for all color types that may have tRNS chunks:  
  
- color type 0 (grayscale), the tRNS chunk contains a single gray level  
- color type 2 (truecolor), the tRNS chunk contains a single RGB color  
- color type 3 (indexed color), the tRNS chunk contains a series of  
    one-byte alpha values  
  
Add sample PNG files from [the "official" test-suite for PNG created by Willem van Schaik](http://www.schaik.com/pngsuite/).  
  
Add `<define>BOOST_GIL_IO_TEST_ALLOW_READING_IMAGES` to Jamfile for PNG tests.  
NOTE: Deliberately not defined `BOOST_GIL_IO_TEST_ALLOW_WRITING_IMAGES` as it seems to require some extra configuration, currently it causes some tests to fail. So, Jamfile likely needs more updates and clean-up.  
  
### References  
  
- Color types w/ tRNS discussed in https://github.com/boostorg/gil/pull/125#issuecomment-511905528  
- PNG suite files with tRNS chunks identified using [png-chunk-extraction.go](https://github.com/parsiya/Go-Security/blob/master/png-tests/png-chunk-extraction.go) program.  
- Closes #120  
  
### Tasklist  
  
- [x] Find a good PNG test image with tRNS chunk  
- [x] Complete the test to verify tRNS has been read and used as expected  
- [ ] Review  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: chhenning  
> Created_at: 2018-09-25 12:49:12 UTC  
> Url: https://github.com/boostorg/gil/pull/125#issuecomment-424329502  

Is the image read correctly?

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-09-25 12:54:10 UTC  
> Url: https://github.com/boostorg/gil/pull/125#issuecomment-424331147  

@chhenning It is read successfully, without any exception about incompatibility. It also causes reading of the tRNS chunks, then setting `png_set_tRNS_to_alpha`.  
  
That is all I observe. I haven't tried to verify if the image is read correctly.

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-09-25 17:43:03 UTC  
> Url: https://github.com/boostorg/gil/pull/125#issuecomment-424436451  

NOTE: None of the CI builds run the `full` IO tests, but the `simple`, hence this added `read_alpha_palette_with_trns_chunk` test is not checked on our CI-s.

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-10-03 11:32:07 UTC  
> Url: https://github.com/boostorg/gil/pull/125#issuecomment-426603183  

@stefanseefeld & @chhenning any feedback on this one?

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-07-16 17:13:03 UTC  
> Url: https://github.com/boostorg/gil/pull/125#issuecomment-511905528  

In order to properly test `tRNS` support, we may need three different images:  
  
From http://www.libpng.org/pub/png/spec/1.2/PNG-Chunks.html:  
> The tRNS chunk specifies that the image uses simple transparency: either alpha values associated with palette entries (for indexed-color images) or a single transparent color (for grayscale and truecolor images).   
>   
> For color type 3 (indexed color), the tRNS chunk contains a series of one-byte alpha values, corresponding to entries in the PLTE chunk:  
>   
> ```  
> Alpha for palette index 0:  1 byte  
> Alpha for palette index 1:  1 byte  
> ...etc...  
> ```  
>   
> For color type 0 (grayscale), the tRNS chunk contains a single gray level value, stored in the format:  
>   
> ```  
> Gray:  2 bytes, range 0 .. (2^bitdepth)-1  
> ```  
>   
> For color type 2 (truecolor), the tRNS chunk contains a single RGB color value, stored in the format:  
>   
> ```  
> Red:   2 bytes, range 0 .. (2^bitdepth)-1  
> Green: 2 bytes, range 0 .. (2^bitdepth)-1  
> Blue:  2 bytes, range 0 .. (2^bitdepth)-1  
> ```

---

## Comment 6

> Username: mloskot  
> Created_at: 2019-08-07 20:33:02 UTC  
> Url: https://github.com/boostorg/gil/pull/125#issuecomment-519258097  

Following second round of research, https://github.com/boostorg/gil/pull/125#issuecomment-511905528 around a month ago, the test received collection of PNG files with the variety of `tRNS`.  
  
Despite long time, no reviews have arrived, so I'm going to merge it.

---
