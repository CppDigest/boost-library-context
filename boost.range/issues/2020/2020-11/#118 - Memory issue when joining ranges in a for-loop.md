# #118 - Memory issue when joining ranges in a for-loop [Open]

> Username: mathbagu  
> Created at: 2020-11-25 10:26:53 UTC  
> Updated at: 2020-11-27 15:34:34 UTC  
> Url: https://github.com/boostorg/range/issues/118  

We are facing a memory issue (process is killed by the OOMKiller) when we join ranges inside a for loop. The idea here, is to flatten a list of ranges before returning the new range.  
  
```  
#include <vector>  
#include <boost/range/any_range.hpp>  
#include <boost/range/join.hpp>  
  
struct A {  
};  
  
using Range = typename boost::any_range<A, boost::forward_traversal_tag, A&, std::ptrdiff_t>;  
  
struct B {  
    std::vector<A> v{10, A()};  
};  
  
struct C {  
    std::vector<B> v{30, B()};  
  
    Range getAll() const {  
        Range all;  
        for (const auto& b : v) {  
            all = boost::range::join(all, b.v);  
        }  
        return all;  
    }  
}  
  
int main(int argc, char** argv) {  
    C c;  
    for (const A& a : c.getAll()) {  
        // Do something with a  
    }  
      
    return 0;  
}  
```

---

## Comment 1

> Username: mathbagu  
> Created at: 2020-11-27 15:32:48 UTC  
> Updated at: 2020-11-27 15:34:34 UTC  
> Url: https://github.com/boostorg/range/issues/118#issuecomment-734887346  

Finally I solve my issue creating a FlattenIterator based on [this implementation](https://stackoverflow.com/questions/3623082/flattening-iterator):  
  
```  
template <typename Iterator>  
class FlatteningIterator :  
    public boost::iterator_adaptor<  
        FlatteningIterator<Iterator>,  
        Iterator,  
        typename Iterator::value_type::iterator::value_type,  
        boost::forward_traversal_tag,  
        typename Iterator::value_type::iterator::value_type> {  
public:  
    using OuterIterator = Iterator;  
    using InnerIterator = typename Iterator::value_type::iterator;  
    using Value = typename Iterator::value_type::iterator::value_type;  
  
    using base = boost::iterator_adaptor<FlatteningIterator<Iterator>, Iterator, Value, boost::forward_traversal_tag, Value>;  
  
public:  
    FlatteningIterator() = default;  
  
    FlatteningIterator(Iterator it) :  
        base(it),  
        m_outer_end(it),  
        m_inner_begin(),  
        m_inner_end() {  
    }  
  
    FlatteningIterator(Iterator begin, Iterator end) :  
        base(begin),  
        m_outer_end(end) {  
  
        if (begin != end) {  
            m_inner_begin = (*begin).begin(),  
            m_inner_end = (*begin).end();  
            skipEmptyRanges();  
        }  
    }  
  
private:  
    void increment() {  
        if (this->base_reference() == m_outer_end) {  
            return;  
        }  
  
        ++m_inner_begin;  
        skipEmptyRanges();  
    }  
  
    Value dereference() const {  
        return *m_inner_begin;  
    }  
  
    void skipEmptyRanges() {  
        while ((this->base_reference() != m_outer_end) && (m_inner_begin == m_inner_end)) {  
            ++this->base_reference();  
            if (this->base_reference() != m_outer_end) {  
                m_inner_begin = (*this->base_reference()).begin();  
                m_inner_end = (*this->base_reference()).end();  
            }  
        }  
    }  
  
    friend class boost::iterator_core_access;  
  
private:  
    OuterIterator m_outer_end;  
    InnerIterator m_inner_begin;  
    InnerIterator m_inner_end;  
};  
  
struct FlattenForwarder {};  
  
namespace {  
  
const FlattenForwarder flattened;  
  
}  // namespace  
  
template <typename SinglePassRange>  
using FlattenRange = boost::iterator_range<FlatteningIterator<typename boost::range_iterator<SinglePassRange>::type>>;  
  
template <typename SinglePassRange>  
FlattenRange<SinglePassRange> operator|(SinglePassRange& range, FlattenForwarder) {  
    using Iterator = typename boost::range_iterator<SinglePassRange>::type;  
  
    return boost::make_iterator_range(FlatteningIterator<Iterator>(boost::begin(range), boost::end(range)), FlatteningIterator<Iterator>(boost::end(range)));  
}  
  
template <typename SinglePassRange>  
FlattenRange<SinglePassRange> operator|(const SinglePassRange& range, FlattenForwarder) {  
    using Iterator = typename boost::range_iterator<const SinglePassRange>::type;  
  
    return boost::make_iterator_range(FlatteningIterator<Iterator>(boost::begin(range), boost::end(range)), FlatteningIterator<Iterator>(boost::end(range)));  
}  
```  
  
It would be nice if you can integrate this feature
