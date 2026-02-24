# #98 - When compiling bzip2/zlib from source, should they be compiled with global visibility? [Open]

> Username: gcflymoto  
> Created at: 2019-06-29 01:33:43 UTC  
> Updated at: 2022-02-01 15:43:37 UTC  
> Url: https://github.com/boostorg/iostreams/issues/98  

Hi, as of Boost 1.69 on non-windows systems the default visibility is set to hidden. When requesting boost to build bzip2/zlib from source, it causes libboost_bzip2.so and libboost_zlib.so shared lib symbols to be built with hidden visibility, then they cannot be linked to libboost_iostreams.so    
  
```  
.../libboost_iostreams.so: undefined reference to `BZ2_bzCompressEnd'  
.../libboost_iostreams.so: undefined reference to `BZ2_bzDecompressEnd'  
.../libboost_iostreams.so: undefined reference to `BZ2_bzCompressInit'  
.../libboost_iostreams.so: undefined reference to `BZ2_bzDecompress'  
.../libboost_iostreams.so: undefined reference to `BZ2_bzDecompressInit'  
.../libboost_iostreams.so: undefined reference to `BZ2_bzCompress'  
```  
  
Is there any workaround for this besides building all of boost with global visibility? or building bzip2/zlib externally? Can I patch the Jamfile or rebuild just bzip2/zlip/iostreams with global visibility?

---

## Comment 1

> Username: rdoeffinger  
> Created at: 2022-02-01 15:43:37 UTC  
> Url: https://github.com/boostorg/iostreams/issues/98#issuecomment-1026986006  

Assuming you can't just use a pre-built zlib/libbz2 which would be easiest,  building them separately and pointing linkflags and compileflags to the location should work I believe?
