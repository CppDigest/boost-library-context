# #175 - Boost\libs\numeric\ublas failed to build due to error C2059, C2065, C2923, C2976, C2447 .... on MSVC [Open]

> Username: Zhaojun-Liu  
> Created at: 2022-06-07 07:40:49 UTC  
> Updated at: 2022-08-25 01:45:20 UTC  
> Url: https://github.com/boostorg/ublas/issues/175  

**Issue Description:**  
Boost\libs\numeric\ublas failed to build due to error C2059, C2065, C2923, C2976, C2447 .... on MSVC. Could you please take a look? The master branch commit we used is a1552ab. Thanks.  
  
**Build Steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git boost  
2. open a VS 2019 x86 command prompt and browse to boost  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
5. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 -j16 variant=release --build-dir=..\out\x86rel libs\numeric\ublas\test  
  
**Error Info:**  
test_tensor.cpp  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(215): error C2059: syntax error: ''template<''  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(216): error C2065: 'size_type': undeclared identifier  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(216): error C2923: 'std::vector': 'size_type' is not a valid template type argument for parameter '_Ty'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(216): note: see declaration of 'size_type'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(216): error C2976: 'std::vector': too few template arguments  
C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\VC\Tools\MSVC\14.29.30133\include\vector(496): note: see declaration of 'std::vector'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(216): error C2065: 'size_type': undeclared identifier  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(216): error C2065: 'layout_type': undeclared identifier  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(216): error C2923: 'boost::numeric::ublas::basic_strides': 'size_type' is not a valid template type argument for parameter '__int_type'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(216): note: see declaration of 'size_type'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(216): error C2923: 'boost::numeric::ublas::basic_strides': 'layout_type' is not a valid template type argument for parameter '__layout'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(216): note: see declaration of 'layout_type'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(217): error C2143: syntax error: missing ';' before '{'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(217): error C2447: '{': missing function header (old-style formal list?)  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(235): error C2059: syntax error: ''template<''  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(235): error C2059: syntax error: '...'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(236): error C2065: 'layout_type': undeclared identifier  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(236): error C2923: 'boost::numeric::ublas::basic_strides': 'layout_type' is not a valid template type argument for parameter '__layout'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(236): note: see declaration of 'layout_type'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(237): error C2143: syntax error: missing ';' before '{'  
F:\gitP\boostorg\boost\boost\numeric\ublas\tensor\strides.hpp(237): error C2447: '{': missing function header (old-style formal list?)  
.\boost/numeric/ublas/tensor/tensor.hpp(333): error C2059: syntax error: ''template<''  
.\boost/numeric/ublas/tensor/tensor.hpp(726): note: see reference to class template instantiation 'boost::numeric::ublas::tensor<T,F,A>' being compiled  
.\boost/numeric/ublas/tensor/tensor.hpp(334): error C2065: 'other_layout': undeclared identifier  
.\boost/numeric/ublas/tensor/tensor.hpp(334): error C2923: 'boost::numeric::ublas::tensor': 'other_layout' is not a valid template type argument for parameter 'F'  
.\boost/numeric/ublas/tensor/tensor.hpp(334): note: see declaration of 'other_layout'  
.\boost/numeric/ublas/tensor/tensor.hpp(334): error C2976: 'boost::numeric::ublas::tensor': too few template arguments  
.\boost/numeric/ublas/tensor/tensor.hpp(79): note: see declaration of 'boost::numeric::ublas::tensor'  
.\boost/numeric/ublas/tensor/tensor.hpp(335): error C2334: unexpected token(s) preceding ':'; skipping apparent function body  
.\boost/numeric/ublas/tensor/tensor.hpp(30): fatal error C1075: '{': no matching token found  
  
**Detailed log file:**  
[test.log.8.log](https://github.com/boostorg/ublas/files/8851066/test.log.8.log)

---

## Comment 1

> Username: amitsingh19975  
> Created at: 2022-08-01 13:40:22 UTC  
> Url: https://github.com/boostorg/ublas/issues/175#issuecomment-1201221103  

If you are trying to use `Tensor`, the minimum `C++` standard is 20. Furthermore, for windows, the msvc >= 14.29. If it doesn't work, please post the msvc version.

---

## Comment 2

> Username: Zhaojun-Liu  
> Created at: 2022-08-02 02:28:36 UTC  
> Url: https://github.com/boostorg/ublas/issues/175#issuecomment-1201943693  

Hi @amitsingh19975,  
I tried option /std:c++latest, the error still exists. And the msvc version as below:  
![image](https://user-images.githubusercontent.com/103498042/182278645-2ca704dc-be08-4fd7-8636-285914e147c3.png)

---

## Comment 3

> Username: amitsingh19975  
> Created at: 2022-08-02 08:43:44 UTC  
> Updated at: 2022-08-02 10:06:23 UTC  
> Url: https://github.com/boostorg/ublas/issues/175#issuecomment-1202195719  

Are you using `b2` for compiling? If yes, try passing c++ standard `cxxstd=latest` like this. If the problem persists, send me the command you use to compile. Try to use GCC on windows to check if the same problem occurs. If it does, you are doing something wrong while compiling, like using `b2` incorrectly.

---

## Comment 4

> Username: Zhaojun-Liu  
> Created at: 2022-08-02 10:00:17 UTC  
> Updated at: 2022-08-25 01:45:20 UTC  
> Url: https://github.com/boostorg/ublas/issues/175#issuecomment-1202277403  

I try to update the build steps as follows:  
1. **.\bootstrap**  
2. **.\b2 headers variant=release cxxstd=latest --build-dir=..\out\x86rel address-model=32**  
3. **.\b2 variant=release cxxstd=latest --build-dir=..\out\x86rel address-model=32**  
and then runtest as below  
**.\b2 -j16 variant=release cxxstd=latest --build-dir=..\out\x86rel libs\numeric\ublas\test**  
  
and the issue persists.

---

## Comment 5

> Username: amitsingh19975  
> Created at: 2022-08-02 10:10:24 UTC  
> Url: https://github.com/boostorg/ublas/issues/175#issuecomment-1202287903  

Try to follow the steps in the following yml file https://github.com/boostorg/ublas/blob/develop/.github/workflows/windows.yml.

---

## Comment 6

> Username: Zhaojun-Liu  
> Created at: 2022-08-25 01:44:46 UTC  
> Url: https://github.com/boostorg/ublas/issues/175#issuecomment-1226674786  

I compared the difference between our compilation steps and the yml file you provided, and after executing different parts, the error can still be reproduced.
