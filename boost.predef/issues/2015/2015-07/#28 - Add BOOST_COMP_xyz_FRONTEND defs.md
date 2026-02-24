# #28 - Add BOOST_COMP_xyz_FRONTEND defs. [Open]

> Username: grafikrobot  
> Created at: 2015-07-13 13:47:47 UTC  
> Updated at: 2019-01-10 03:50:35 UTC  
> Url: https://github.com/boostorg/predef/issues/28  

We need to be able to differentiate between "real" compilers and just the front end compiler. Hence we need to add BOOST_COMP_xyz_FRONTEND defs for the compilers. Currently the known front-ends are: Clang (clang.h), EDG (edg.h), Pathscale (ekopath.h), IBM XLCPP (ibm.h), and Intel OSX (as Clang hence intel.h and clang.h).

---

## Comment 1

> Username: jeking3  
> Created at: 2018-12-22 00:26:55 UTC  
> Url: https://github.com/boostorg/predef/issues/28#issuecomment-449529845  

See https://github.com/boostorg/predef/issues/93
