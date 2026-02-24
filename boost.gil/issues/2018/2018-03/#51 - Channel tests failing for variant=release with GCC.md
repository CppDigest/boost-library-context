# #51 - Channel tests failing for variant=release with GCC [Open]

> Username: mloskot  
> Created at: 2018-03-18 14:55:26 UTC  
> Updated at: 2021-04-14 07:17:41 UTC  
> Url: https://github.com/boostorg/gil/issues/51  

Following addition of `variant=debug,release` to Travis CI in #50, the GCC 5.4.1 build job is failing due to failing channel tests, see https://travis-ci.org/boostorg/gil/jobs/355009262  
```  
testing.capture-output bin.v2/libs/gil/test/channel.test/gcc-5.4.1/release/channel.run  
====== BEGIN OUTPUT ======  
std::exception  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
Interestingly, the same tests built with clang 4.2.1 are passing without any failures, see https://travis-ci.org/boostorg/gil/jobs/355009263  
  
I confirmed this issue locally, on Ubuntu with GCC 5.4.0  
```  
b2 variant=debug,release cxxflags="-Wno-unused-local-typedefs" test  
...  
testing.capture-output ../../bin.v2/libs/gil/test/channel.test/gcc-5.4.0/debug/channel.run  
**passed** ../../bin.v2/libs/gil/test/channel.test/gcc-5.4.0/debug/channel.test  
...  
testing.capture-output ../../bin.v2/libs/gil/test/channel.test/gcc-5.4.0/release/channel.run  
====== BEGIN OUTPUT ======  
std::exception  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
## References  
  
Yet another issue revealed by `variant=release` build configuration.  
Previous one was the image checksum tests, see #49

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-03-28 12:08:56 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-376863576  

More info related to this bug in https://github.com/boostorg/gil/pull/50#issuecomment-376719681

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2018-03-28 17:09:55 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-376963312  

The error occurs when compiling the `channel.cpp` test with `g++ -O3`.  
A noteworthy observation: Using `-Wall` yields these warnings, which may be related:  
```  
../../boost/gil/channel.hpp: In function ‘void test_packed_channel_reference()’:  
../../boost/gil/channel.hpp:441:79: warning: ‘*((void*)&<anonymous> +16)’ is used uninitialized in this function [-Wuninitialized]  
     void set_unsafe(integer_t value) const { this->set_data((this->get_data() & ~channel_mask) | (( static_cast< BitField >( value )<<FirstBit))); }  
                                                             ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~  
../../boost/gil/channel.hpp:441:79: warning: ‘*((void*)&<anonymous> +16)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
     void set_unsafe(integer_t value) const { this->set_data((this->get_data() & ~channel_mask) | (( static_cast< BitField >( value )<<FirstBit))); }  
                                                             ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~  
../../boost/gil/channel.hpp:441:79: warning: ‘*((void*)&<anonymous> +16)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
     void set_unsafe(integer_t value) const { this->set_data((this->get_data() & ~channel_mask) | (( static_cast< BitField >( value )<<FirstBit))); }  
                                                             ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~  
../../boost/gil/channel.hpp: In function ‘void test_packed_dynamic_channel_reference()’:  
../../boost/gil/channel.hpp:563:42: warning: ‘*((void*)&<anonymous> +32)’ is used uninitialized in this function [-Wuninitialized]  
         this->set_data((this->get_data() & ~channel_mask) | value<<_first_bit);  
                        ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~  
```  
  
which confuses me. I don't even understand which of the referenced variables / values the compiler believes to be uninitialized...

---

## Comment 3

> Username: mloskot  
> Created at: 2018-03-29 21:19:00 UTC  
> Updated at: 2021-04-14 07:17:41 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-377375655  

So, I made similar observation while compiling `variant=release` (aka `-O3`) for the `test/pixel.cpp`, but this time the very same `channel.hpp:441:79` warning is giving up more details:  
  
```  
$ toolset=gcc-7 cxxflags="-std=c++11" variant=release test//pixel  
  
../../boost/gil/channel.hpp:441:79: warning: ‘v2’ is used uninitialized in this function [-Wuninitialized]  
     void set_unsafe(integer_t value) const { this->set_data((this->get_data() & ~channel_mask) | (( static_cast< BitField >( value )<<FirstBit))); }  
                                                             ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~  
test/pixel.cpp:91:24: note: ‘v2’ was declared here  
         pixel2_value_t v2(C2::_pixel);  
                        ^~  
In file included from ../../boost/gil/planar_pixel_reference.hpp:29:0,  
                 from test/pixel.cpp:20:  
../../boost/gil/channel.hpp:441:79: warning: ‘pv2’ is used uninitialized in this function [-Wuninitialized]  
     void set_unsafe(integer_t value) const { this->set_data((this->get_data() & ~channel_mask) | (( static_cast< BitField >( value )<<FirstBit))); }  
                                                             ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~  
../../boost/gil/channel.hpp: In function ‘void test_packed_pixel()’:  
../../boost/gil/channel.hpp:410:63: warning: ‘p121’ is used uninitialized in this function [-Wuninitialized]  
     integer_t get() const { return integer_t((this->get_data()&channel_mask) >> FirstBit); }  
                                              ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~  
../../boost/gil/channel.hpp:441:79: warning: ‘<anonymous>’ is used uninitialized in this function [-Wuninitialized]  
     void set_unsafe(integer_t value) const { this->set_data((this->get_data() & ~channel_mask) | (( static_cast< BitField >( value )<<FirstBit))); }  
                                                             ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~  
../../boost/gil/channel.hpp:441:79: warning: ‘<anonymous>’ is used uninitialized in this function [-Wuninitialized]  
     void set_unsafe(integer_t value) const { this->set_data((this->get_data() & ~channel_mask) | (( static_cast< BitField >( value )<<FirstBit))); }  
                                                             ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~  
```  
  
The `p121` from the warning above, for example, is `rgb121_pixel_t p121;` in `test/pixel.cpp`. Possibly, a red herring.  
  
  
Now, regarding the `*((void*)&<anonymous> +16)` in your warnings, interestingly there is similar report https://github.com/boostorg/python/issues/180  where the `boost/python/extract.hpp:185` is  
  
```  
return *(T*)(  
    // Only do the stage2 conversion once  
    m_data.stage1.convertible ==  m_data.storage.bytes  
        ? m_data.storage.bytes  
        : (rvalue_from_python_stage2)(m_source, m_data.stage1, registered<T>::converters)  
    );  
```  
  
This `*(T*)` rings a bell from `packed_channel_reference_base` member:  
  
```  
const bitfield_t& get_data() const { return *static_cast<const bitfield_t*>(_data_ptr); }  
```  
  
Hmm, aren't we having an aliasing situation here?  
  
-----  
  
Finally, I don't think we can consider the channel bug as killed. See these `test/channel` builds for `variant=release` and `variant=debug`  
  
* GCC 7  
  
![boost-gil-channel-gcc7](https://user-images.githubusercontent.com/80741/38114209-cc9bfed8-33a7-11e8-896b-a60c0cf1c1fc.png)  
  
* clang 5.0  
  
![boost-gil-channel-clang5](https://user-images.githubusercontent.com/80741/38114213-cfd6b03e-33a7-11e8-8bd8-ddef2f78b4ad.png)  
  
I'll remind that the original report https://github.com/boostorg/gil/issues/51#issue-306247078 is due to the `test/channel` failing with GCC 5.4.1 and passing with clang 4.2.1 :-)  
  
We seem to be chasing some undefined behaviour, so that theory about aliasing issues seem plausible.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2018-03-29 22:19:06 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-377389842  

Indeed. It might be worthwhile trying to compile with `g++ -fno-strict-aliasing -O3` to see whether that works around the failure seen with `g++ -O3`.

---

## Comment 5

> Username: chhenning  
> Created at: 2018-03-31 15:16:51 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-377700113  

Can someone please provide a minimal reproducible code sample?

---

## Comment 6

> Username: mloskot  
> Created at: 2018-04-13 22:20:42 UTC  
> Updated at: 2018-04-13 22:40:48 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-381274337  

@chhenning I've been trying smaller extracts from the failing test but none seem to reproduce the problem. Seems like it's the kind of [added cout and works](https://github.com/boostorg/gil/pull/50#issuecomment-376966346) mystery.  
  
-----  
  
@stefanseefeld @chhenning I've just realised that merging of #83 (the `bitsN` -> `<cstdint>` refactoring) seems to have **fixed** the channel tests failures!  
  
Looking at the history of [our CircleCI workflows](https://circleci.com/gh/boostorg/workflows/gil/tree/develop):  
  
- last CircleCI build where the test is failing for the GCC 5.01: https://circleci.com/workflow-run/42b255d9-5f6b-4023-9965-c52d605a811e  
  
- first CircleCI build where the test is passing on very compiler: https://circleci.com/workflow-run/0c2440f9-3367-4f96-a206-2f467df40ba9  
  
How about that?!

---

## Comment 7

> Username: chhenning  
> Created at: 2018-04-13 22:49:25 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-381278596  

Great!   
  
Are you using any fancy compiler switches like the ones @stefanseefeld mentioned?

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2018-04-13 22:55:43 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-381279455  

You call '-O3' fancy ??? :-)  
  
On Fri, Apr 13, 2018, 18:49 chhenning, <notifications@github.com> wrote:  
  
> Great!  
>  
> Are you using any fancy compiler switches like the ones @stefanseefeld  
> <https://github.com/stefanseefeld> mentioned?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/gil/issues/51#issuecomment-381278596>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ABL411CN7mDM_ss89k9LNG71ulrCVY0sks5toSt2gaJpZM4SvOrc>  
> .  
>

---

## Comment 9

> Username: mloskot  
> Created at: 2018-04-13 23:05:02 UTC  
> Updated at: 2018-04-13 23:09:34 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-381280769  

@chhenning during local testing I tried all sorts of switches, optimisation levels, with and without strict aliasing etc.  
  
On CircleCI all builds are using the same switches as defined in our root Jamfile https://github.com/boostorg/gil/blob/develop/Jamfile  
  
And there are two build variants: `debug` (those run first) and `release` with `optimisation=speed` (those run if corresponding debug pass, hence you see two walls of builds).  
  
However, it's not quite calming to me what we're observing. If we assume there is no GCC 5.x bug, and we take into account my #83 changes did just rename the same types/aliases w/o correcting any casts, or other issues, then there still might be a bug hiding :)

---

## Comment 10

> Username: chhenning  
> Created at: 2018-04-13 23:24:47 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-381283135  

:-)  
  
This one I call fancy: `-fno-strict-aliasing`

---

## Comment 11

> Username: chhenning  
> Created at: 2018-04-13 23:27:20 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-381283463  

@mloskot you are correct. What about the other bug?

---

## Comment 12

> Username: mloskot  
> Created at: 2018-04-13 23:31:25 UTC  
> Updated at: 2018-04-13 23:36:35 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-381283932  

@chhenning No good news yet on the checksum bug from me, it still is failing on VS2017 64bit release build, see the AppVeyor. Got busy with the bitsN refactoring and other things.

---

## Comment 13

> Username: pdimov  
> Created at: 2021-04-14 02:54:10 UTC  
> Url: https://github.com/boostorg/gil/issues/51#issuecomment-819191472  

Not sure how relevant this issue is today, but the warning seems to indicate exactly what it says: a pixel value is used uninitialized. The tests seems to have been reorganized though so it's not easy to track now.
