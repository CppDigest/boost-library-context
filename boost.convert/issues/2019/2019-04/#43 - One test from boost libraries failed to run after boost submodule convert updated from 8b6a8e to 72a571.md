# #43 - One test from boost libraries failed to run after boost submodule convert updated from 8b6a8e to 72a571 [Closed]

> Username: spacelg  
> Created at: 2019-04-24 05:58:14 UTC  
> Updated at: 2019-04-24 08:13:03 UTC  
> Closed at: 2019-04-24 08:13:02 UTC  
> Url: https://github.com/boostorg/convert/issues/43  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
One test from boost libraries failed to run after boost submodule convert updated from 8b6a8e to 72a571 [boost/convert@89f0ab7](https://github.com/boostorg/boost/commit/89f0ab7a8ad04e8f4f4b8e4f02d673068d77e48d).Could you please take a look?  
  
Reproduce steps:.  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\convert\test  
  
[log_x64_test_21.log](https://github.com/boostorg/convert/files/3110726/log_x64_test_21.log)  
  
  
ErrorMessage:  
.\boost/convert/stream.hpp(47): fatal error C1083: Cannot open include file: 'boost/parameter/is_argument_pack.hpp': No such file or directory   
.\boost/mpl/has_key.hpp(33): error C2504: 'boost::mpl::has_key_impl<boost::parameter::aux::arg_list_tag>::apply<AssociativeSequence,Key>': base class undefined  
        with  
        [  
            AssociativeSequence=boost::parameter::aux::tagged_argument<boost::cnv::parameter::type::base,const boost::cnv::base>,  
            Key=boost::cnv::parameter::type::base  
        ]  
.\boost/convert/base.hpp(118): error C2039: 'type': is not a member of 'boost::mpl::has_key<argument_pack,boost::cnv::parameter::type::base>'  
        with  
        [  
            argument_pack=boost::parameter::aux::tagged_argument<boost::cnv::parameter::type::base,const boost::cnv::base>  
        ]  
.\boost/convert/base.hpp(118): error C2512: 'boost::type': no appropriate default constructor available  
.\boost/convert/base.hpp(118): note: The target type has no constructors  
.\boost/convert/base.hpp(118): error C2672: 'boost::cnv::cnvbase<boost::cnv::printf>::_assign': no matching overloaded function found  
.\boost/convert/base.hpp(118): error C2780: 'void boost::cnv::cnvbase<boost::cnv::printf>::_assign(const argument_pack &,boost::cnv::parameter::type::fill,boost::mpl::true_)': expects 3 arguments - 2 provided  
.\boost/convert/base.hpp(120): error C2512: 'boost::type': no appropriate default constructor available  
.\boost/convert/base.hpp(120): note: The target type has no constructors  
.\boost/convert/base.hpp(120): error C2672: 'boost::cnv::cnvbase<boost::cnv::printf>::_assign': no matching overloaded function found

---

## Comment 1

> Username: yet-another-user  
> Created at: 2019-04-24 08:13:02 UTC  
> Url: https://github.com/boostorg/convert/issues/43#issuecomment-486117593  

Indeed. Most humble apologies. I reverted the premature merge and built. I'll monitor regression tests page to make sure it builds there. Many thanks.
