# #121 - Add skip_iterator class [Closed]

> Username: kylelutz  
> Created at: 2014-05-25 06:08:06 UTC  
> Updated at: 2015-04-22 15:00:01 UTC  
> Closed at: 2015-04-22 14:59:55 UTC  
> Url: https://github.com/boostorg/compute/issues/121  

Add a `skip_iterator` adaptor which skips over multiple elements each time it is incremented.  
  
For example, the following test should pass:  
  
```  
int data[] = { 1, 2, 3, 4, 5, 6, 7, 8 };  
compute::vector<int> vec(data, data + 8, queue);  
  
compute::vector<int> result(4, context);  
  
// copy every other element to result  
compute::copy(  
    compute::skip_iterator<int>(vec.begin(), 2),  
    compute::skip_iterator<int>(vec.end(), 2),  
    result.begin(),  
    queue  
);  
CHECK_RANGE_EQUAL(int, 4, result, (1, 3, 5, 7));  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2015-04-11 17:38:32 UTC  
> Url: https://github.com/boostorg/compute/issues/121#issuecomment-91890780  

I've read about Boost.Iterator (iterator facade and adaptor) and I started coding skip_iterator.   
  
Sadly, I'm stuck. I can't find an elegant way to modify index expression `expr.m_index_expr`, so it would include skip parameter.   
  
``` cpp  
(...)  
  
template<class InputIterator, class T, class IndexExpr>  
struct skip_iterator_index_expr  
{  
    typedef typename std::iterator_traits<InputIterator>::value_type result_type;  
  
    skip_iterator_index_expr(const InputIterator &input_iter,  
                                  const T &skip_value,  
                                  const IndexExpr &index_expr)  
        : m_input_iter(input_iter),  
          m_skip_value(skip_value),  
          m_index_expr(index_expr)  
    {  
    }  
  
    InputIterator m_input_iter;  
    const T& m_skip_value;  
    IndexExpr m_index_expr;  
};  
  
template<class InputIterator, class T, class IndexExpr>  
inline meta_kernel& operator<<(meta_kernel &kernel,  
                               const skip_iterator_index_expr<InputIterator,  
                                                                   T,  
                                                                   IndexExpr> &expr)  
{  
    return kernel << expr.m_input_iter[expr.m_index_expr];  
}  
  
(...)  
```  
  
Should skip value type be restricted to integers types? Should skip value be only greater than zero?

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-04-11 23:05:11 UTC  
> Url: https://github.com/boostorg/compute/issues/121#issuecomment-91938659  

I actually happen to have some code which implements this lying around, just never got around to actually fully documenting it and making a pull-request. I decided to name it `strided_iterator` after the one found in the Boost.Range library (see [boost/range/adaptor/strided.hpp](http://www.boost.org/doc/libs/1_58_0/boost/range/adaptor/strided.hpp)).  
  
I think it makes sense to restrict the stride parameter to integral types (I use the `difference_type` from the underlying iterator as the stride parameter type). Also I think it makes sense to limit the stride to values greater than zero.  
  
Here's the code I have, feel free to use/copy/adapt it for implementing this.  
- strided_iterator.hpp  
  
```  
#ifndef BOOST_COMPUTE_ITERATOR_STRIDED_ITERATOR_HPP  
#define BOOST_COMPUTE_ITERATOR_STRIDED_ITERATOR_HPP  
  
#include <cstddef>  
#include <iterator>  
  
#include <boost/config.hpp>  
#include <boost/iterator/iterator_adaptor.hpp>  
  
#include <boost/compute/detail/meta_kernel.hpp>  
#include <boost/compute/type_traits/is_device_iterator.hpp>  
  
namespace boost {  
namespace compute {  
  
// forward declaration for strided_iterator  
template<class Iterator>  
class strided_iterator;  
  
namespace detail {  
  
// helper class which defines the iterator_adaptor super-class  
// type for strided_iterator<Iterator>  
template<class Iterator>  
class strided_iterator_base  
{  
public:  
    typedef ::boost::iterator_adaptor<  
        ::boost::compute::strided_iterator<Iterator>,  
        Iterator,  
        typename std::iterator_traits<Iterator>::value_type,  
        typename std::iterator_traits<Iterator>::iterator_category,  
        typename std::iterator_traits<Iterator>::value_type  
    > type;  
};  
  
template<class IndexExpr, class Stride>  
struct stride_expr  
{  
    stride_expr(const IndexExpr &expr, Stride stride)  
        : m_index_expr(expr),  
          m_stride(stride)  
    {  
    }  
  
    IndexExpr m_index_expr;  
    Stride m_stride;  
};  
  
template<class IndexExpr, class Stride>  
inline stride_expr<IndexExpr, Stride> make_stride_expr(const IndexExpr &expr, Stride stride)  
{  
    return stride_expr<IndexExpr, Stride>(expr, stride);  
}  
  
template<class IndexExpr, class Stride>  
inline meta_kernel& operator<<(meta_kernel &kernel, const stride_expr<IndexExpr, Stride> &expr)  
{  
    return kernel << "(" << kernel.lit<uint_>(expr.m_stride) << "*" << expr.m_index_expr << ")";  
}  
  
template<class Iterator, class IndexExpr>  
struct strided_iterator_index_expr  
{  
    typedef typename std::iterator_traits<Iterator>::value_type result_type;  
    typedef typename std::iterator_traits<Iterator>::difference_type difference_type;  
  
    strided_iterator_index_expr(const Iterator &input_iter,  
                                difference_type stride,  
                                const IndexExpr &index_expr)  
        : m_input_iter(input_iter),  
          m_stride(stride),  
          m_index_expr(index_expr)  
    {  
    }  
  
    Iterator m_input_iter;  
    difference_type m_stride;  
    IndexExpr m_index_expr;  
};  
  
template<class Iterator, class IndexExpr>  
inline meta_kernel& operator<<(meta_kernel &kernel,  
                               const strided_iterator_index_expr<Iterator, IndexExpr> &expr)  
{  
    return kernel << expr.m_input_iter[make_stride_expr(expr.m_index_expr, expr.m_stride)];  
}  
  
} // end detail namespace  
  
/// \class strided_iterator  
/// \brief A strided iterator adaptor.  
///  
/// The strided_iterator adaptor   
///  
/// \see buffer_iterator, transform_iterator  
template<class Iterator>  
class strided_iterator : public detail::strided_iterator_base<Iterator>::type  
{  
public:  
    typedef typename detail::strided_iterator_base<Iterator>::type super_type;  
    typedef typename super_type::value_type value_type;  
    typedef typename super_type::reference reference;  
    typedef typename super_type::base_type base_type;  
    typedef typename super_type::difference_type difference_type;  
  
    strided_iterator(Iterator iterator, difference_type stride)  
        : super_type(iterator),  
          m_stride(stride)  
    {  
    }  
  
    strided_iterator(const strided_iterator &other)  
        : super_type(other.base()),  
          m_stride(other.m_stride)  
    {  
    }  
  
    strided_iterator& operator=(const strided_iterator &other)  
    {  
        if(this != &other){  
            super_type::operator=(other);  
  
            m_stride = other.m_stride;  
        }  
  
        return *this;  
    }  
  
    ~strided_iterator()  
    {  
    }  
  
    size_t get_index() const  
    {  
        return super_type::base().get_index();  
    }  
  
    template<class IndexExpr>  
    detail::strided_iterator_index_expr<Iterator, IndexExpr>  
    operator[](const IndexExpr &expr) const  
    {  
        return detail::strided_iterator_index_expr<Iterator, IndexExpr>(  
            super_type::base(), m_stride, expr  
        );  
    }  
  
private:  
    friend class ::boost::iterator_core_access;  
  
    bool equal(const strided_iterator& other) const  
    {  
        return this->base_reference() == other.base_reference() && m_stride == other.m_stride;  
    }  
  
    difference_type distance_to(const strided_iterator& other) const  
    {  
        return std::distance(this->base_reference(), other.base_reference()) / m_stride;  
    }  
  
    void increment()  
    {  
        std::advance(this->base_reference(), m_stride);  
    }  
  
    void decrement()  
    {  
        std::advance(this->base_reference(), -m_stride);  
    }  
  
    void advance(difference_type n)  
    {  
        std::advance(this->base_reference(), m_stride * n);  
    }  
  
private:  
    difference_type m_stride;  
};  
  
/// Returns a strided_iterator for \p iterator with \p stride.  
///  
/// \param iterator the underlying iterator  
/// \param stride the stride between adjacent elements  
///  
/// \return a \c strided_iterator for \p iterator with \p stride  
template<class Iterator>  
inline strided_iterator<Iterator>  
make_strided_iterator(Iterator iterator,  
                      typename std::iterator_traits<Iterator>::difference_type stride)  
{  
    return strided_iterator<Iterator>(iterator, stride);  
}  
  
/// \internal_ (is_device_iterator specialization for strided_iterator)  
template<class Iterator>  
struct is_device_iterator<strided_iterator<Iterator> > : boost::true_type {};  
  
} // end compute namespace  
} // end boost namespace  
  
#endif // BOOST_COMPUTE_ITERATOR_STRIDED_ITERATOR_HPP  
```  
- test_strided_iterator.cpp  
  
```  
#define BOOST_TEST_MODULE TestStridedIterator  
#include <boost/test/unit_test.hpp>  
  
#include <iterator>  
  
#include <boost/static_assert.hpp>  
#include <boost/type_traits.hpp>  
  
#include <boost/compute/types.hpp>  
#include <boost/compute/functional.hpp>  
#include <boost/compute/algorithm/copy.hpp>  
#include <boost/compute/algorithm/iota.hpp>  
#include <boost/compute/container/vector.hpp>  
#include <boost/compute/iterator/buffer_iterator.hpp>  
#include <boost/compute/iterator/strided_iterator.hpp>  
  
#include "check_macros.hpp"  
#include "context_setup.hpp"  
  
BOOST_AUTO_TEST_CASE(value_type)  
{  
    using boost::compute::float4_;  
  
    BOOST_STATIC_ASSERT((  
        boost::is_same<  
            boost::compute::strided_iterator<  
                boost::compute::buffer_iterator<float>  
            >::value_type,  
            float  
        >::value  
    ));  
}  
  
BOOST_AUTO_TEST_CASE(base_type)  
{  
    BOOST_STATIC_ASSERT((  
        boost::is_same<  
            boost::compute::strided_iterator<  
                boost::compute::buffer_iterator<int>  
            >::base_type,  
            boost::compute::buffer_iterator<int>  
        >::value  
    ));  
}  
  
BOOST_AUTO_TEST_CASE(copy)  
{  
    boost::compute::vector<int> input(10, context);  
    boost::compute::iota(input.begin(), input.end(), 0, queue);  
  
    boost::compute::vector<int> result(5, context);  
  
    // copy every second element from input to result  
    boost::compute::vector<int>::iterator last = boost::compute::copy(  
        boost::compute::make_strided_iterator(input.begin(), 2),  
        boost::compute::make_strided_iterator(input.end(), 2),  
        result.begin(),  
        queue  
    );  
  
    BOOST_CHECK_EQUAL(std::distance(result.begin(), last), 5);  
    CHECK_RANGE_EQUAL(int, 5, result, (0, 2, 4, 6, 8));  
}  
  
BOOST_AUTO_TEST_SUITE_END()  
```

---

## Comment 3

> Username: jszuppe  
> Created at: 2015-04-11 23:31:47 UTC  
> Url: https://github.com/boostorg/compute/issues/121#issuecomment-91945266  

Thanks!   
  
I have to say that your and my code look almost exactly the same! Except for name, using difference_type and the fact that yours includes elegant solution to "how include skip/stride into index expression" problem ;)  
  
> I decided to name it strided_iterator after the one found in the Boost.Range library (see >boost/range/adaptor/strided.hpp).  
  
I've seen this iterator, but somehow I'd not thought about using the same name. It is a good idea.   
  
Well, it's Sunday 1:30 am in Poland, so I will complete my code after I get some sleep.

---

## Comment 4

> Username: jszuppe  
> Created at: 2015-04-22 12:13:12 UTC  
> Url: https://github.com/boostorg/compute/issues/121#issuecomment-95152787  

I can be closed - see https://github.com/kylelutz/compute/pull/452.
