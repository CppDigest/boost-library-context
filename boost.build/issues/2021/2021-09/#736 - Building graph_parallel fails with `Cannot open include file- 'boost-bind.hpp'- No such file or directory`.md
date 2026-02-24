# #736 - Building graph_parallel fails with `Cannot open include file: 'boost/bind.hpp': No such file or directory` [Closed]

> Username: pdimov  
> Created at: 2021-09-14 13:10:40 UTC  
> Updated at: 2022-02-02 20:39:57 UTC  
> Closed at: 2022-02-02 20:39:57 UTC  
> Url: https://github.com/boostorg/build/issues/736  

Building `graph_parallel`, for example by using the steps outlined in https://github.com/boostorg/build/issues/734, fails with  
```  
C:\boost-git\develop\boost/property_map/parallel/impl/distributed_property_map.ipp(14): fatal error C1083: Cannot open include file: 'boost/bind.hpp': No such file or directory  
```  
This is caused by the symlink for `boost/bind.hpp` not being created in `boost/`, which is in turn caused by the fact that `boost/bind.hpp` is being included from an `.ipp` file.  
  
`.ipp` files are not currently considered C++ headers by `type/cpp.jam`: https://github.com/boostorg/build/blob/d52f1993d906aa3fd3649a8dd5134402794bf86e/src/tools/types/cpp.jam#L76-L79  
which is why the `#include` scanner doesn't run on them. This can potentially cause more issues than just a missing symlink - dependencies are going to be missed.  
  
I think that the right fix here is to add  
```  
type.register IPP : ipp : HPP ;  
```  
and  
```  
type.set-scanner IPP : c-scanner ;  
```  
to `cpp.jam`, although I'm not 100% sure.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-09-14 14:35:30 UTC  
> Url: https://github.com/boostorg/build/issues/736#issuecomment-919212462  

On Tue, Sep 14, 2021 at 8:10 AM Peter Dimov ***@***.***>  
wrote:  
  
> Building graph_parallel, for example by using the steps outlined in #734  
> <https://github.com/boostorg/build/issues/734>, fails with  
>  
> C:\boost-git\develop\boost/property_map/parallel/impl/distributed_property_map.ipp(14): fatal error C1083: Cannot open include file: 'boost/bind.hpp': No such file or directory  
>  
> This is caused by the symlink for boost/bind.hpp not being created in  
> boost/, which is in turn caused by the fact that boost/bind.hpp is being  
> included from an .ipp file.  
>  
> .ipp files are not currently considered C++ headers by type/cpp.jam:  
> https://github.com/boostorg/build/blob/develop/src/tools/types/cpp.jam#L76-L79  
> which is why the #include scanner doesn't run on them. This can  
> potentially cause more issues than just a missing symlink - dependencies  
> are going to be missed.  
>  
> I think that the right fix here is to add  
>  
> type.register IPP : ipp : HPP ;  
>  
> and  
>  
> type.set-scanner IPP : c-scanner ;  
>  
> to cpp.jam, although I'm not 100% sure.  
>  
Sounds got to me.  
  
--   
-- René Ferdinand Rivera Morell  
-- Don't Assume Anything  -- No Supone Nada  
-- Robot Dreams - http://robot-dreams.net

---

## Comment 2

> Username: gnaggnoyil  
> Created at: 2021-09-16 17:05:04 UTC  
> Updated at: 2021-09-16 17:05:25 UTC  
> Url: https://github.com/boostorg/build/issues/736#issuecomment-921077079  

Sorry to be troublesome, but I added the suggested change to `tags/boost-1.77.0` and rerun the build step and the problem still exists: No symlink created for `bind.hpp` and `graph_parallel` fails to build.

---

## Comment 3

> Username: jeking3  
> Created at: 2022-01-28 03:45:09 UTC  
> Url: https://github.com/boostorg/build/issues/736#issuecomment-1023858925  

I can confirm the proposed fix does not work.

---

## Comment 4

> Username: jeking3  
> Created at: 2022-01-28 04:58:38 UTC  
> Url: https://github.com/boostorg/build/issues/736#issuecomment-1023887577  

@pdimov The c-scanner regex cannot figure this out in the property_map.hpp header:  
  
https://github.com/boostorg/property_map/blob/develop/include/boost/property_map/property_map.hpp#L598-L604  
  
```  
#ifdef BOOST_GRAPH_USE_MPI  
// Hide include from dependency trackers; the right fix  
// is not to have it at all, but who knows what'll break  
#define BOOST_PMAP_HEADER_NAME <boost/property_map/parallel/parallel_property_maps.hpp>  
#include BOOST_PMAP_HEADER_NAME  
#undef BOOST_PMAP_HEADER_NAME  
#endif  
```  
  
I dumped out the b2 output with all debugging on and tracked it down to here:  
  
> (builtin):>>>>|>>>>|>>>> scanner.propagate object(c-scanner)@11925 : <object(c-scanner)@11925>boost/assert.hpp <object(c-scanner)@11925>boost/config.hpp <object(c-scanner)@11925>boost/static_assert.hpp <object(c-scanner)@11925>cstddef <object(c-scanner)@11925>iterator <object(c-scanner)@11925>boost/concept/assert.hpp <object(c-scanner)@11925>boost/concept_check.hpp <object(c-scanner)@11925>boost/concept_archetype.hpp <object(c-scanner)@11925>boost/mpl/assert.hpp <object(c-scanner)@11925>boost/mpl/if.hpp <object(c-scanner)@11925>boost/mpl/or.hpp <object(c-scanner)@11925>boost/mpl/and.hpp <object(c-scanner)@11925>boost/mpl/has_xxx.hpp <object(c-scanner)@11925>boost/type_traits/is_same.hpp <object(c-scanner)@11925>boost/property_map/vector_property_map.hpp : <object(c-scanner)@11925>boost/property_map/property_map.hpp  
  
I could not find an entry for parallel_property_maps.hpp and now we know why, and that is why the dependencies are not getting processed properly, because we're never getting to the ipp file in the first place.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-01-28 15:31:21 UTC  
> Url: https://github.com/boostorg/build/issues/736#issuecomment-1024331240  

Thanks Jim. Looks like it's time to heed that comment and remove this include, then fix graph_parallel to include the parallel header instead.

---

## Comment 6

> Username: pdimov  
> Created at: 2022-01-29 05:41:46 UTC  
> Url: https://github.com/boostorg/build/issues/736#issuecomment-1024840934  

Done in https://github.com/boostorg/property_map/commit/56fd79a22db1a00dc5238677c0e011e5ec9477be and https://github.com/boostorg/graph_parallel/commit/cf752ed392c0cfdcdd20ff1a47f99824ea6abc2f.

---

## Comment 7

> Username: jeking3  
> Created at: 2022-02-01 11:56:04 UTC  
> Url: https://github.com/boostorg/build/issues/736#issuecomment-1026763726  

I cannot reproduce the issue any more, however the "ipp" changes are probably still a good idea.  Additionally I found some other uses of #define to #include which the code scanner is going to miss (BOOST_HASH_SET_HEADER, BOOST_HASH_MAP_HEADER):  
  
```  
jking@pulsar:~/boost-root$ grep -r BOOST_HASH_SET_HEADER libs/  
libs/phoenix/include/boost/phoenix/config.hpp:#define BOOST_PHOENIX_HASH_SET_HEADER BOOST_HASH_SET_HEADER  
libs/config/include/boost/config/stdlib/libstdcpp3.hpp:#   define BOOST_HASH_SET_HEADER <ext/hash_set>  
libs/config/include/boost/config/stdlib/libstdcpp3.hpp:#   define BOOST_HASH_SET_HEADER <backward/hash_set>  
libs/config/include/boost/config/stdlib/libstdcpp3.hpp:#if !__has_include(BOOST_HASH_SET_HEADER) || (__GNUC__ >= 10)  
libs/config/include/boost/config/detail/suffix.hpp:// Set BOOST_HASH_SET_HEADER if not set already:  
libs/config/include/boost/config/detail/suffix.hpp:#if defined(BOOST_HAS_HASH) && !defined(BOOST_HASH_SET_HEADER)  
libs/config/include/boost/config/detail/suffix.hpp:#  define BOOST_HASH_SET_HEADER <hash_set>  
libs/config/doc/html/boost_config/boost_macro_reference.html:                  classes. When defined, <code class="computeroutput"><span class="identifier">BOOST_HASH_SET_HEADER</span></code>  
libs/config/doc/html/boost_config/boost_macro_reference.html:                  <code class="computeroutput"><span class="identifier">BOOST_HASH_SET_HEADER</span></code>  
libs/config/doc/macro_reference.qbk:When defined, `BOOST_HASH_SET_HEADER` and `BOOST_HASH_MAP_HEADER` will contain  
libs/config/doc/macro_reference.qbk:[[`BOOST_HASH_SET_HEADER`][  
libs/config/test/boost_has_hash.ipp:#include BOOST_HASH_SET_HEADER  
libs/serialization/include/boost/serialization/hash_set.hpp:#include BOOST_HASH_SET_HEADER  
libs/serialization/test/test_set_hashed.cpp:#include BOOST_HASH_SET_HEADER  
  
  
jking@pulsar:~/boost-root$ grep -r BOOST_HASH_MAP_HEADER libs/  
libs/phoenix/include/boost/phoenix/config.hpp:#define BOOST_PHOENIX_HASH_MAP_HEADER BOOST_HASH_MAP_HEADER  
libs/config/include/boost/config/stdlib/libstdcpp3.hpp:#   define BOOST_HASH_MAP_HEADER <ext/hash_map>  
libs/config/include/boost/config/stdlib/libstdcpp3.hpp:#   define BOOST_HASH_MAP_HEADER <backward/hash_map>  
libs/config/include/boost/config/detail/suffix.hpp:// Set BOOST_HASH_MAP_HEADER if not set already:  
libs/config/include/boost/config/detail/suffix.hpp:#if defined(BOOST_HAS_HASH) && !defined(BOOST_HASH_MAP_HEADER)  
libs/config/include/boost/config/detail/suffix.hpp:#  define BOOST_HASH_MAP_HEADER <hash_map>  
libs/config/doc/html/boost_config/boost_macro_reference.html:                  and <code class="computeroutput"><span class="identifier">BOOST_HASH_MAP_HEADER</span></code>  
libs/config/doc/html/boost_config/boost_macro_reference.html:                  <code class="computeroutput"><span class="identifier">BOOST_HASH_MAP_HEADER</span></code>  
libs/config/doc/macro_reference.qbk:When defined, `BOOST_HASH_SET_HEADER` and `BOOST_HASH_MAP_HEADER` will contain  
libs/config/doc/macro_reference.qbk:[[`BOOST_HASH_MAP_HEADER`][  
libs/config/test/boost_has_hash.ipp:#include BOOST_HASH_MAP_HEADER  
libs/serialization/include/boost/serialization/hash_map.hpp:#include BOOST_HASH_MAP_HEADER  
libs/serialization/test/test_map_hashed.cpp:#include BOOST_HASH_MAP_HEADER  
```

---

## Comment 8

> Username: jeking3  
> Created at: 2022-02-02 20:37:19 UTC  
> Url: https://github.com/boostorg/build/issues/736#issuecomment-1028336543  

@pdimov looks like there are a couple more cases of this (see above), not sure if anything needs to be done.  I cannot reproduce the original issue any more so we could close this out if you want.

---

## Comment 9

> Username: pdimov  
> Created at: 2022-02-02 20:39:57 UTC  
> Url: https://github.com/boostorg/build/issues/736#issuecomment-1028338516  

Up to the B2 maintainers to say whether the `.ipp` issue is real and if so, whether it needs to be fixed. I suppose further discussion will take place in the bfgroup issue so, closing this.
