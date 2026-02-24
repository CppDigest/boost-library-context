# #24 - long double for boost::qvm::normalize gives error during compilation [Closed]

> Username: prater27  
> Created at: 2020-04-02 01:15:17 UTC  
> Updated at: 2020-04-13 12:58:52 UTC  
> Closed at: 2020-04-13 12:58:52 UTC  
> Url: https://github.com/boostorg/qvm/issues/24  

I am trying to compile a project (Qt5creator, Ubuntu 18.04LTS) where I use boost and qvm library. When using long double for the normalize template, I am getting a compilation error:   
  
/usr/local/include/boost/qvm/gen/vec_operations3.hpp:584: error: undefined reference to `long double boost::qvm::sqrt<long double>(long double)'  
  
 It does not happen if I give for example double or float. This code should reproduce the error:  
int main(void){  
    boost::qvm::vec<long double,3> vector={10,20,30};  
    boost::qvm:: normalize(vector);  
    return 0;  
}  
  
  
Qt5Creator pops-up this part of the code:  
  
      sfinae  
            {  
            using ::boost::qvm::normalized;  
            }  
  
        template <class A>  
        BOOST_QVM_INLINE_OPERATIONS  
        typename enable_if_c<  
            vec_traits<A>::dim==3,  
            void>::type  
        normalize( A & a )  
            {  
            typedef typename vec_traits<A>::scalar_type T;  
            T const a0=vec_traits<A>::template read_element<0>(a);  
            T const a1=vec_traits<A>::template read_element<1>(a);  
            T const a2=vec_traits<A>::template read_element<2>(a);  
            T const m2=a0*a0+a1*a1+a2*a2;  
            if( m2==scalar_traits<typename vec_traits<A>::scalar_type>::value(0) )  
                BOOST_QVM_THROW_EXCEPTION(zero_magnitude_error());  
            T const rm=scalar_traits<T>::value(1)/sqrt<T>(m2);  
            vec_traits<A>::template write_element<0>(a)*=rm;  
            vec_traits<A>::template write_element<1>(a)*=rm;  
            vec_traits<A>::template write_element<2>(a)*=rm;  
            }  
  
  
Thanks in advance

---

## Comment 1

> Username: zajo  
> Created at: 2020-04-02 06:32:33 UTC  
> Updated at: 2020-04-03 01:55:33 UTC  
> Url: https://github.com/boostorg/qvm/issues/24#issuecomment-607649333  

The boost::qvm namespace contains function templates (with the main  
template left undefined), and specializations calling the matching C  
function from <math.h>, e.g.  
  
template <class T> T acos( T );  
template <> float acos<float>( float x ) { return ::acosf(x); }  
template <> double acos<double>( double x ) { return ::acos(x); }  
  
Specializations for long double were not provided. This is now fixed in  
develop, to be merged into master pending Travis CI.  
  
Thanks for pointing this out.

---

## Comment 2

> Username: zajo  
> Created at: 2020-04-03 01:56:14 UTC  
> Url: https://github.com/boostorg/qvm/issues/24#issuecomment-608186159  

This is now merged in master, will get released with 1.73.0.

---

## Comment 3

> Username: prater27  
> Created at: 2020-04-13 12:58:52 UTC  
> Url: https://github.com/boostorg/qvm/issues/24#issuecomment-612887525  

Great! Thanks
