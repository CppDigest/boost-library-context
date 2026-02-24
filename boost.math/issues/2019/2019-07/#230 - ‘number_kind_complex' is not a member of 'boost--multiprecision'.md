# #230 - ‘number_kind_complex' is not a member of 'boost::multiprecision' [Closed]

> Username: alxbnct  
> Created at: 2019-07-29 15:05:28 UTC  
> Updated at: 2019-08-03 21:20:43 UTC  
> Closed at: 2019-08-03 21:20:43 UTC  
> Url: https://github.com/boostorg/math/issues/230  

Shalom,  
  
```  
// test.cc  
#include <vector>  
#include <boost/math/tools/norms.hpp>  
  
int main() {  
    std :: vector<double> v{8, 0, 0};  
    double sup = boost::math::tools::lp_norm(v.cbegin(), v.cend(), 7);  
}  
  
```  
After running  
  
`g++ test.cc -std=c++2a -Wall`  
  
I got  
```  
In file included from ex4_boost_norm.cc:3:  
/usr/include/boost/math/tools/norms.hpp: In function ‘auto boost::math::tools::sup_norm(ForwardIterator, ForwardIterator)’:  
/usr/include/boost/math/tools/norms.hpp:82:94: error: ‘number_kind_complex’ is not a member of ‘boost::multiprecision’; did you mean ‘number_kind_integer’?  
   82 |                   boost::multiprecision::number_category<T>::value == boost::multiprecision::number_kind_complex)  
      |                                                                                              ^~~~~~~~~~~~~~~~~~~  
      |                                                                                              number_kind_integer  
/usr/include/boost/math/tools/norms.hpp: In function ‘auto boost::math::tools::l2_norm(ForwardIterator, ForwardIterator)’:  
/usr/include/boost/math/tools/norms.hpp:163:94: error: ‘number_kind_complex’ is not a member of ‘boost::multiprecision’; did you mean ‘number_kind_integer’?  
  163 |                   boost::multiprecision::number_category<T>::value == boost::multiprecision::number_kind_complex)  
      |                                                                                              ^~~~~~~~~~~~~~~~~~~  
      |                                                                                              number_kind_integer  
/usr/include/boost/math/tools/norms.hpp: In function ‘auto boost::math::tools::lp_norm(ForwardIterator, ForwardIterator, unsigned int)’:  
/usr/include/boost/math/tools/norms.hpp:281:106: error: ‘number_kind_complex’ is not a member of ‘boost::multiprecision’; did you mean ‘number_kind_integer’?  
  281 |                   boost::multiprecision::number_category<RealOrComplex>::value == boost::multiprecision::number_kind_complex)  
      |                                                                                                          ^~~~~~~~~~~~~~~~~~~  
      |                                                                                                          number_kind_integer  
/usr/include/boost/math/tools/norms.hpp: In function ‘auto boost::math::tools::lp_distance(ForwardIterator, ForwardIterator, ForwardIterator, unsigned int)’:  
/usr/include/boost/math/tools/norms.hpp:372:106: error: ‘number_kind_complex’ is not a member of ‘boost::multiprecision’; did you mean ‘number_kind_integer’?  
  372 |                   boost::multiprecision::number_category<RealOrComplex>::value == boost::multiprecision::number_kind_complex)  
      |                                                                                                          ^~~~~~~~~~~~~~~~~~~  
      |                                                                                                          number_kind_integer  
/usr/include/boost/math/tools/norms.hpp: In function ‘auto boost::math::tools::l1_distance(ForwardIterator, ForwardIterator, ForwardIterator)’:  
/usr/include/boost/math/tools/norms.hpp:428:94: error: ‘number_kind_complex’ is not a member of ‘boost::multiprecision’; did you mean ‘number_kind_integer’?  
  428 |                   boost::multiprecision::number_category<T>::value == boost::multiprecision::number_kind_complex)  
      |                                                                                              ^~~~~~~~~~~~~~~~~~~  
      |                                                                                              number_kind_integer  
/usr/include/boost/math/tools/norms.hpp: In function ‘auto boost::math::tools::l2_distance(ForwardIterator, ForwardIterator, ForwardIterator)’:  
/usr/include/boost/math/tools/norms.hpp:504:94: error: ‘number_kind_complex’ is not a member of ‘boost::multiprecision’; did you mean ‘number_kind_integer’?  
  504 |                   boost::multiprecision::number_category<T>::value == boost::multiprecision::number_kind_complex)  
      |                                                                                              ^~~~~~~~~~~~~~~~~~~  
      |                                                                                              number_kind_integer  
/usr/include/boost/math/tools/norms.hpp: In function ‘auto boost::math::tools::sup_distance(ForwardIterator, ForwardIterator, ForwardIterator)’:  
/usr/include/boost/math/tools/norms.hpp:579:94: error: ‘number_kind_complex’ is not a member of ‘boost::multiprecision’; did you mean ‘number_kind_integer’?  
  579 |                   boost::multiprecision::number_category<T>::value == boost::multiprecision::number_kind_complex)  
      |                                                                                              ^~~~~~~~~~~~~~~~~~~  
      |                                                                                              number_kind_integer  
/usr/include/boost/math/tools/norms.hpp: In instantiation of ‘auto boost::math::tools::lp_norm(ForwardIterator, ForwardIterator, unsigned int) [with ForwardIterator = __gnu_cxx::__normal_iterator<const double*, std::vector<double> >]’:  
ex4_boost_norm.cc:7:66:   required from here  
/usr/include/boost/math/tools/norms.hpp:284:15: error: ‘RealOrComplex’ {aka ‘double’} is not a class, struct, or union type  
  284 |         using Real = typename RealOrComplex::value_type;  
      |               ^~~~  
/usr/include/boost/math/tools/norms.hpp:302:16: error: unable to deduce ‘auto’ from ‘result’  
  302 |         return result;  
      |                ^~~~~~  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-07-29 15:26:05 UTC  
> Url: https://github.com/boostorg/math/issues/230#issuecomment-516039387  

Could you tell me:  
  
```  
$ g++ --version  
```  
  
your operating system, and your Boost version?

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-07-29 15:30:43 UTC  
> Url: https://github.com/boostorg/math/issues/230#issuecomment-516041285  

@jzmaddock : We got our write slapped for putting this dependency on Boost.Multiprecision in Boost.Math; do you think it's worth it to keep this functionality?

---

## Comment 3

> Username: alxbnct  
> Created at: 2019-07-29 15:40:10 UTC  
> Url: https://github.com/boostorg/math/issues/230#issuecomment-516044955  

Well, the version of gcc is 9.1.0 and I run this on ubuntu 16.04 with the latest version of Boost. It's not until I modified all the "number_kind_complex" to "number_kind_integer" in norms.hpp , I could compile and got the correct answer.

---

## Comment 4

> Username: NAThompson  
> Created at: 2019-07-29 16:59:20 UTC  
> Updated at: 2019-07-29 17:00:43 UTC  
> Url: https://github.com/boostorg/math/issues/230#issuecomment-516074283  

@Moshes-Abraham : Strangely, I get no error on `g++-9` on Ubuntu 18.04. Can you clone the latest version of Boost and try that?  
  
```  
$ git clone --recursive https://github.com/boostorg/boost  
```  
  
Are you sure it isn't finding an old version of Boost.Multiprecision?

---

## Comment 5

> Username: NAThompson  
> Created at: 2019-08-03 21:20:43 UTC  
> Url: https://github.com/boostorg/math/issues/230#issuecomment-517955524  

Assuming that this problem has gone away. Feel free to reopen if I'm wrong.
