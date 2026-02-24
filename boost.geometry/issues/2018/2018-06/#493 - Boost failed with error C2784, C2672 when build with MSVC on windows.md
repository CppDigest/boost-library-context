# #493 - Boost failed with error C2784, C2672 when build with MSVC on windows [Closed]

> Username: shanshan0309  
> Created at: 2018-06-21 08:20:41 UTC  
> Updated at: 2018-06-21 12:30:20 UTC  
> Closed at: 2018-06-21 12:30:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/493  

Hi All,  
  
We tried to build and run geometry test for Boost with VS2017 Update 7 on Windows. It failed to build due to the error C2784, C2672. Could you please help take a look at this? Thank you!  
  
**Reproduce steps:**  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. Open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\Release --address-model=32  
5. .\b2 variant=release --build-dir=..\out\Release --address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\geometry\test  
  
**Expected result:**  
All tests passed  
  
**Actual result:**  
Whole log file please see attachment.  
[Boost_geometry_x64_test.log](https://github.com/boostorg/geometry/files/2122706/Boost_geometry_x64_test.log)  
.\boost/geometry/srs/projections/proj/aitoff.hpp(153): **error C2672: 'boost::pow': no matching overloaded function found**  
.\boost/geometry/srs/projections/proj/aitoff.hpp(134): note: while compiling class template member function 'void boost::geometry::projections::detail::aitoff::base_aitoff_spheroid<CalculationType,Parameters>::inv(double &,double &,double &,double &) const'  
        with  
        [  
            CalculationType=double,  
            Parameters=boost::geometry::projections::parameters<double>  
        ]  
.\boost/geometry/srs/projections/impl/base_dynamic.hpp(142): note: see reference to function template instantiation 'void boost::geometry::projections::detail::aitoff::base_aitoff_spheroid<CalculationType,Parameters>::inv(double &,double &,double &,double &) const' being compiled  
        with  
        [  
            CalculationType=double,  
            Parameters=boost::geometry::projections::parameters<double>  
        ]  
  
...  
  
.\boost/geometry/strategies/transform/srs_transformer.hpp(56): note: see reference to class template instantiation 'boost::geometry::srs::projection<boost::geometry::srs::dynamic,double>' being compiled  
libs\geometry\test\srs\srs_transformer.cpp(45): note: see reference to class template instantiation 'boost::geometry::strategy::transform::srs_forward_transformer<boost::geometry::srs::projection<boost::geometry::srs::dynamic,double>>' being compiled  
.\boost/bind/placeholders.hpp(54): note: see reference to class template instantiation 'boost::arg<9>' being compiled  
.\boost/bind/placeholders.hpp(53): note: see reference to class template instantiation 'boost::arg<8>' being compiled  
.\boost/bind/placeholders.hpp(52): note: see reference to class template instantiation 'boost::arg<7>' being compiled  
.\boost/bind/placeholders.hpp(51): note: see reference to class template instantiation 'boost::arg<6>' being compiled  
.\boost/bind/placeholders.hpp(50): note: see reference to class template instantiation 'boost::arg<5>' being compiled  
.\boost/bind/placeholders.hpp(49): note: see reference to class template instantiation 'boost::arg<4>' being compiled  
.\boost/bind/placeholders.hpp(48): note: see reference to class template instantiation 'boost::arg<3>' being compiled  
.\boost/bind/placeholders.hpp(47): note: see reference to class template instantiation 'boost::arg<2>' being compiled  
.\boost/bind/placeholders.hpp(46): note: see reference to class template instantiation 'boost::arg<1>' being compiled  
.\boost/geometry/srs/projections/proj/aitoff.hpp(153): **error C2784: 'boost::rational<Integer> boost::pow(boost::rational<Integer>,IntType)': could not deduce template argument for 'boost::rational<Integer>' from 'CalculationType'**  
        with  
        [  
            CalculationType=double  
        ]  
.\boost/rational.hpp(873): note: see declaration of 'boost::pow'  
  
Thanks,  
Shanshan

---

## Comment 1

> Username: vissarion  
> Created at: 2018-06-21 08:59:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/493#issuecomment-399028833  

Probably related to this https://github.com/boostorg/geometry/issues/491  
You could test this branch https://github.com/awulkiew/geometry/tree/fix/pow if in a hurry, it should be merged in develop shortly.

---

## Comment 2

> Username: shanshan0309  
> Created at: 2018-06-21 09:04:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/493#issuecomment-399030474  

Thank you @vissarion for looking into this.

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-06-21 12:29:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/493#issuecomment-399086747  

Done.
