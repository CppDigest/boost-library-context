# #525 - Boost possibly pulls in wrong intrinsics includes with GCC on PPC [Closed]

> Username: barracuda156  
> Created at: 2023-01-24 18:32:37 UTC  
> Updated at: 2023-01-30 23:40:21 UTC  
> Closed at: 2023-01-30 23:40:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/525  

There is a bug which I am not too sure about where it originates, but it seems to me it may be due to Boost. Specifically, `multiprecision` includes headers with are meant for `ppc64le`, on all `ppc`, which breaks builds on macOS PPC. (I cannot test if the same happens on other Big endian systems.)  
  
Examples: https://github.com/arvidn/libtorrent/issues/7042  
https://github.com/iains/darwin-toolchains-start-here/discussions/43  
  
That should definitely not happen :)  
  
It has been observed only with `gcc12` – `gcc11` is fine. Lo and behold, there is no `immintrin.h` in `gcc11`. Which is why I suspect Boost should perform a check and not include the header where it does not belong.

---

## Comment 1

> Username: mborland  
> Created at: 2023-01-24 18:38:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/525#issuecomment-1402417557  

So `gcc12` provides `immintrin.h` on PPC even though it doesn't work?  
  
The block you quoted shows we are only including it where it is available (I know that gcc12 provides `__has_include`):  
  
```  
#ifdef __has_include  
#if __has_include(<immintrin.h>)  
#define BOOST_MP_HAS_IMMINTRIN_H  
#endif  
#endif  
```

---

## Comment 2

> Username: mborland  
> Created at: 2023-01-24 18:46:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/525#issuecomment-1402427090  

For extra safety we could replace `immintrin.h` with `<x86intrin.h>` which includes the former. Can you please check to make sure that somehow did not leak into your system with GCC-12? If is not present I can make the change.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2023-01-24 19:00:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/525#issuecomment-1402443127  

I've seen this before, and could have sworn we'd already fixed this: GCC seems to have the habit of shipping with headers which can't actually be used on the targeted system, which basically renders __has_Include next to useless.  The fix anyway is to check that this really is Intel x64 before even bothering with the __has_include.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2023-01-24 19:04:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/525#issuecomment-1402447979  

This was meant to have been fixed in https://github.com/boostorg/multiprecision/pull/421  
  
See https://github.com/boostorg/multiprecision/issues/419

---

## Comment 5

> Username: barracuda156  
> Created at: 2023-01-24 19:22:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/525#issuecomment-1402470003  

> This was meant to have been fixed in #421  
>   
> See #419  
  
@jzmaddock Thank you. I have not tried with the latest Boost yet, I can try. With 1.76 it is still broken.  
```  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:125:25: note: overloaded builtin '__builtin_vec_floor' is implemented by builtin '__builtin_vsx_xvrdpim'  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:129:24: error: '__builtin_vsx_xvrdpip' requires the '-mvsx' option  
  129 |         __r = vec_ceil ((__v2df) __A);  
      |                        ^  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:129:24: note: overloaded builtin '__builtin_vec_ceil' is implemented by builtin '__builtin_vsx_xvrdpip'  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:133:25: error: '__builtin_vsx_xvrdpiz' requires the '-mvsx' option  
  133 |         __r = vec_trunc ((__v2df) __A);  
      |                         ^  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:133:25: note: overloaded builtin '__builtin_vec_trunc' is implemented by builtin '__builtin_vsx_xvrdpiz'  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:136:24: error: '__builtin_vsx_xvrdpic' requires the '-mvsx' option  
  136 |         __r = vec_rint ((__v2df) __A);  
      |                        ^  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:136:24: note: overloaded builtin '__builtin_vec_rint' is implemented by builtin '__builtin_vsx_xvrdpic'  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h: In function '__m128 _mm_round_ps(__m128, int)':  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:209:24: error: '__builtin_vsx_xvrspic' requires the '-mvsx' option  
  209 |         __r = vec_rint ((__v4sf) __A);  
      |                        ^  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:209:24: note: overloaded builtin '__builtin_vec_rint' is implemented by builtin '__builtin_vsx_xvrspic'  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:232:24: error: '__builtin_vsx_xvrspic' requires the '-mvsx' option  
  232 |         __r = vec_rint ((__v4sf) __A);  
      |                        ^  
/opt/local/lib/gcc12/gcc/powerpc-apple-darwin10/12.2.0/include/smmintrin.h:232:24: note: overloaded builtin '__builtin_vec_rint' is implemented by builtin '__builtin_vsx_xvrspic'  
In file included from /opt/local/libexec/boost/1.76/include/boost/config/detail/suffix.hpp:546,  
                 from /opt/local/libexec/boost/1.76/include/boost/config.hpp:61,  
                 from /opt/local/var/macports/build/_opt_local_var_macports_sources_rsync.macports.org_macports_release_tarballs_ports_net_libtorrent-rasterbar/libtorrent-rasterbar/work/libtorrent-rasterbar-2.0.8/include/libtorrent/config.hpp:50,  
                 from /opt/local/var/macports/build/_opt_local_var_macports_sources_rsync.macports.org_macports_release_tarballs_ports_net_libtorrent-rasterbar/libtorrent-rasterbar/work/libtorrent-rasterbar-2.0.8/src/bt_peer_connection.cpp:41:  
/opt/local/libexec/boost/1.76/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp: In function 'unsigned char boost::multiprecision::detail::addcarry_limb(unsigned char, boost::multiprecision::limb_type, boost::multiprecision::limb_type, boost::multiprecision::limb_type*)':  
/opt/local/libexec/boost/1.76/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp:103:22: error: '_addcarry_u32' was not declared in this scope  
  103 |    return BOOST_JOIN(BOOST_MP_ADDC, 32)(carry, a, b, reinterpret_cast<unsigned int*>(p_result));  
      |                      ^~~~~~~~~~~~~  
/opt/local/libexec/boost/1.76/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp: In function 'unsigned char boost::multiprecision::detail::subborrow_limb(unsigned char, boost::multiprecision::limb_type, boost::multiprecision::limb_type, boost::multiprecision::limb_type*)':  
/opt/local/libexec/boost/1.76/include/boost/multiprecision/cpp_int/intel_intrinsics.hpp:108:22: error: '_subborrow_u32' was not declared in this scope  
  108 |    return BOOST_JOIN(BOOST_MP_SUBB, 32)(carry, a, b, reinterpret_cast<unsigned int*>(p_result));  
      |                      ^~~~~~~~~~~~~  
```  
  
There is another complication (which you can see in `libtorrent-rasterbar` case): if the build system check for physical CPU, that is wrong on Apple, where Rosetta supported native PPC. (That is, OS emulates the arch, and for all practical purposes it is `ppc32`). I would assume that something like `&& !defined(__POWERPC__)` should be added (this define exists only on macOS and BeOS, neither of which ever supported `ppc64le` or VSX.)

---

## Comment 6

> Username: barracuda156  
> Created at: 2023-01-24 19:24:16 UTC  
> Updated at: 2023-01-24 19:39:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/525#issuecomment-1402474012  

@mborland @jzmaddock Good news: Boost 1.81 does not have this bug. At least with `libtorrent-rasterbar`, where 1.76 fails (native PPC too, not only Rosetta), 1.81 does not.  
  
Maybe worth backporting though, since many people use older Boost (say, Macports sets 1.76 for many systems as default).

---

## Comment 7

> Username: jzmaddock  
> Created at: 2023-01-25 11:55:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/525#issuecomment-1403495275  

Sorry we don't do point releases of old Boost versions.

---

## Comment 8

> Username: barracuda156  
> Created at: 2023-01-25 16:08:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/525#issuecomment-1403856473  

Let me confirm it is fixed for Rosetta too with new Boost, and I close the issue.

---

## Comment 9

> Username: barracuda156  
> Created at: 2023-01-30 23:40:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/525#issuecomment-1409526599  

@jzmaddock @mborland Yes, in 10.6.8 Rosetta everything is good with Boost 1.81 too. No more pulling in wrong headers. Thank you!
