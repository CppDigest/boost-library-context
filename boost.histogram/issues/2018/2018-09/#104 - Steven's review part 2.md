# #104 - Steven's review part 2 [Closed]

> Username: HDembinski  
> Created at: 2018-09-25 07:42:23 UTC  
> Updated at: 2019-02-04 23:27:50 UTC  
> Closed at: 2019-02-04 23:27:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/104  

arithmetic_operators.hpp:  
  
16: Returning an rvalue reference is tempting, but it  
 can cause issues in some cases because it prevents  
 the lifetime of the temporary from being extended in  
 certain cases.  See temporary-lifetime.cpp.  
  
42: histogram<A, S>&& operator*(histogram<A, S>&& a, const double x)  
 histogram::operator*= uses scale_type.  I don't really  
 care whether you use scale_type or double, but please  
 be consistent.  If you're hard-coding double here, then  
 there's no point to making the internals more flexible.  
  
histogram.hpp:  
  
36: What are the requirements on the Axes parameter?  
   I'm deducing that it must be either a std::tuple  
   or a std::vector containing Axes, but I didn't  
   see this explicitly stated anywhere.  
  
60: histogram& operator=(const histogram<A, S>& rhs)  
 This isn't exception safe unless axes_assign is  
 nothrow--which it isn't.  
  
107: std::size_t dim() const noexcept  
 This can probably be constexpr as well.  
  
207: // precondition: argument sequence must be strictly ascending axis  
indices  
 This is exactly the sort of information that needs to  
 appear in the documentation.  Use \pre.  
 Also it would be nice if this weren't a requirement,  
 and reduce_to could shuffle the axes.  
 Does/should it work in the degenerate case of  
 reducing a histogram to itself?  
  
234:  BOOST_ASSERT_MSG(begin == end ||  
 Will reduce_to even work if begin == end?  
 If nothing else, I think it will fail in the do/while on L247  
 Also, you're only checking the end, but the code seems  
 to permit signed integers.  (Please specify the restrictions  
 on the iterator's value_type clearly).  
  
261:  friend class python_access;  
 I don't see a forward declaration of this.  Please note  
 that the semantics of this can be exceedingly strange  
 when there is no prior declaration. See friend.cpp  
  
321: auto axes = typename H::axes_type(s.get_allocator());  
    axes.reserve(std::distance(begin, end));  
  
    while (begin != end) axes.emplace_back(*begin++);  
 This can all be reduced to one line:  
    auto axes = typename H::axes_type(begin, end, s.get_allocator())  
  
histogram_fwd.hpp:  
  
N/A  
  
iterator.hpp:  
  
27: iterator_over(const iterator_over& o)  
 It's probably worth a comment that the cache is not copied,  
 as I almost suggested defaulting this.  
  
66: void advance(int n)  
 The difference_type is ptrdiff_t.  
  
- ForwardIterators and higher must be default constructable.  
  
- distance_to is not implemented.  
  
literals.hpp:  
  
- The standard guarantees that the digits are consecutive,  
 so char2int can be just (C - '0').  
  
ostream_operators.hpp:  
  
- You don't actually need ostream.  iosfwd is enough,  
 especially since you're only #including histogram_fwd.hpp,  
 and not histogram.hpp.  
  
- It looks like the format includes a trailing comma, which  
 seems a little odd when you go to the trouble of not adding  
 a new line for an empty histogram.  
  
serialization.hpp:  
  
77: if (Archive::is_loading::value) { S::apply(typename S::destroyer(),  
s.buffer); }  
 This isn't exception-safe, as creating a new buffer can  
 throw leaving a dangling pointer behind.  
  
128: if (Archive::is_loading::value) { this->~variable(); }  
 Don't call the destructor like this.  
  
- This does not support XML archives.  
  
- serialize for user defined axis types seems  
 error prone, because labelled_base::serialize  
 is inherited.  If the user forgets to add serialize,  
 the code will compile and silently slice the object.  
  
weight.hpp:  
  
12: namespace detail {  
 Be careful about exposing types from namespace detail  
 in any way.  It allows ADL in client code to look inside  
 your detail namespace.  
  
26: detail::weight_type<T> weight(T&& t) {  
     return {t};  
 No forwarding?  The same goes for sample.  
  
axis/any.hpp:  
  
242: explicit operator const base&() const {  
 The name `base` is potentially confusing as there is also  
 a base_type.  I suggest qualifying it as `axis::base`  
  
axis/base.hpp:  
  
- I only see operator==, but not operator!=.  
  
axis/interval_view.hpp:  
  
33: return axis_.lower(idx_ + 1);  
 The requirement that an axis needs to provide lower to  
 work with interval_view is not documented.  Neither  
 is the exact meaning of lower.  In particular, it needs  
 to work with a past-the-end index.  The actual implementations  
 seem to work for any index, but I'm not sure that the  
 result is actually meaningful if the index is out-of-bounds.  
  
39: operator==  
 Should two interval_views be considered to be the same or  
 different if they refer to distinct, but equal axes?  
  
- Actually, I find interval_view somewhat unconvincing.  
 Why can't it just be a pure value that stores the upper  
 and lower bounds?  The value_type is usually going to  
 be a built-in type anyway, not to mention that you  
 haven't documented interval_view sufficiently for  
 it to be used by user-defined axes.  
  
axis/iterator.hpp:  
  
- You're using the default difference_type = ptrdiff_t,  
 but advance and distance_to use int.  
  
- Does a separate reverse_iterator_over have any benefit over  
 std::reverse_iterator?  
  
- It seems inconsistent for axes to have rbegin/rend, when  
 histogram itself doesn't.  
  
axis/ostream_operators.hpp:  
  
22: Calling the function to_string is somewhat misleading,  
 because what you're actually getting is a string formatted  
 for appending to something else.  
  
26: escape_string doesn't escape `\`.  
  
138: Should this specialize for basic_string rather than  
 just string?  
  
axis/types.hpp:  
  
- The interface for a Transform isn't documented.  
  
236: static_cast<int>(std::floor(z * base_type::size()))  
 The cast to int might overflow for large values of z.  
  
332,350: this->~variable();  
 Don't call the destructor in the assignment operator.  
  
435: if (i < 0) { return -std::numeric_limits<value_type>::max(); }  
 Did you mean min?  
  
- Is there a reason that you're not just using std::vector  
 for variable and category?  
  
429: const int z = x - min_;  
 Both x and min_ have type value_type, not int.  
  
511+529: this->~category();  
 Just no.  
  
568: mutable int last_ = 0;  
 mutable is dangerous as it means that non-mutating  
 functions cannot safely be run concurrently without  
 syncronization.  (The other place that uses mutable,  
 iterator_over, is safer because iterators should  
 usually not be shared in the first place.)  
  
axis/value_view.hpp:  
  
- As with interval_view, I don't see any need to keep  
 a reference to the axis.  
  
detail/axes.hpp:  
  
- Don't use the unnamed namespace in headers.  
  
41: equal &= (tp && *tp == std::get<N::value>(t));  
 This doesn't short-circuit on equal.  
  
63: std::get<N::value>(t) = static_cast<const T&>(v[N::value]);  
 Using an explicit conversion operator may not be the best choice  
 here.  My first reaction was to flag this as potential UB  
 because of the unchecked cast.  I had to look through  
 the definition of any to realize that it is in fact  
 checked and can throw.  
  
122: t.resize(sizeof...(Us));  
 reserve+push_back is slightly better than resize+assign.  
  
220: value *= t.shape();  
 It might be wise to check for integer overflow here.  
  
521: optional_index call_impl(Tag, const std::tuple<T>& axes,  
 It seems that this specialization prevents using a single  
 element container with a single element static histogram,  
 which seems inconsistent with the documentation and the  
 behavior for dynamic histograms.  
  
detail/buffer.hpp:  
  
42: You're assuming that the iterator operations themselves  
 do not throw, which isn't guaranteed.  
  
86: std::is_nothrow_constructible<T, U>()  
 This doesn't match the constructor call.  
  
detail/cat.hpp:  
  
- unnamed namespace again.  
  
... Skip a few files with no comments. ...  
  
storage/adaptive_storage.hpp:  
  
299: b.set(b.template create<T>(optr));  
 This isn't exception-safe (increaser does it right).  
  
storage/operators.hpp:  
  
- These generic comparison operators seem really  
 dangerous unless you make detail::requires_storage  
 much more precise.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-09-25 07:44:17 UTC  
> Url: https://github.com/boostorg/histogram/issues/104#issuecomment-424240400  

Additional code from Steven.  
  
friend.cpp  
```  
enum python_access;  
#include <boost/histogram.hpp>  
```  
  
one-element.cpp  
```  
#include <boost/histogram.hpp>  
  
int main() {  
    using namespace boost::histogram;  
    auto h = make_static_histogram(axis::regular<>(4, 0, 1));  
    h(std::make_tuple(0.5));  
}  
```  
  
temporary-lifetime.cpp  
```  
#include <boost/histogram.hpp>  
#include <iostream>  
  
int main() {  
    using namespace boost::histogram;  
    auto h = make_static_histogram(axis::regular<>(4, 0, 1));  
    h(0.5);  
    for(auto x : h + h + h) {  
        std::cout << x.value() << std::endl;  
    }  
}  
```

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-02-04 01:08:35 UTC  
> Url: https://github.com/boostorg/histogram/issues/104#issuecomment-460106531  

Only remaining: fix or get rid of buffer.hpp

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-02-04 23:27:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/104#issuecomment-460454920  

all addressed in develop
