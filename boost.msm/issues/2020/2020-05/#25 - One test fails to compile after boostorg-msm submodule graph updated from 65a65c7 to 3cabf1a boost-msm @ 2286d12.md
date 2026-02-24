# #25 - One test fails to compile after boostorg/msm submodule graph updated from 65a65c7 to 3cabf1a boost/msm @ 2286d12 [Closed]

> Username: 215020267  
> Created at: 2020-05-28 08:32:21 UTC  
> Updated at: 2024-02-13 09:15:59 UTC  
> Closed at: 2024-02-13 09:15:35 UTC  
> Url: https://github.com/boostorg/msm/issues/25  

**Environment:**  
VS 2019 + Windows Server 2016  
  
**Issue description:**  
One test fails to compile after boostorg/msm submodule graph updated from 65a65c7 to 3cabf1a boost/msm @ 2286d12. Could you please take a look?  
  
**Reproduce steps:**  
git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
open a VS 2017 x64 command prompt and browse to D:\Boost\src  
.\bootstrap  
.\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
.\b2 variant=release --build-dir=..\out\x64rel address-model=64  
.\b2 -j16 variant=release --build-dir=..\out\amd64rel libs\msm\test  
[test.log.57.log](https://github.com/boostorg/msm/files/4694269/test.log.57.log)  
  
  
**ErrorMessage:**  
.\boost/bind.hpp(41): note: The practice of declaring the Bind placeholders (_1, _2, ...) in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.  
libs\msm\test\TestConstructor.cpp(264): error C2440: '<function-style-cast>': cannot convert from 'const boost::msm::msm_terminal<Expr>' to '`anonymous-namespace'::player_::Playing'  
        with  
        [  
            Expr=boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left,boost::proto::argsns_::list2<const boost::msm::back::define_states_creation<boost::proto::exprns_::is_proto_expr> &,boost::msm::msm_terminal<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<const `anonymous-namespace'::player_::Playing_::Song1 &>,0>>>,2>  
        ]  
libs\msm\test\TestConstructor.cpp(264): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
  
    call "..\out\amd64rel\boost\bin.v2\standalone\msvc\msvc-14.2\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"..\out\amd64rel\boost\bin.v2\libs\msm\test\TestConstructor.test\msvc-14.2\release\threading-multi\TestConstructor.obj.rsp"   
  
...failed compile-c-c++ ..\out\amd64rel\boost\bin.v2\libs\msm\test\TestConstructor.test\msvc-14.2\release\threading-multi\TestConstructor.obj...

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-13 09:15:35 UTC  
> Updated at: 2024-02-13 09:15:59 UTC  
> Url: https://github.com/boostorg/msm/issues/25#issuecomment-1940859767  

fixed. No more found in any runner
