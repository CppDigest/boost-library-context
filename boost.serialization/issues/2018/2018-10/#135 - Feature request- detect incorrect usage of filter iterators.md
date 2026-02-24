# #135 - Feature request: detect incorrect usage of filter iterators [Closed]

> Username: apolukhin  
> Created at: 2018-10-30 19:53:58 UTC  
> Updated at: 2019-02-24 18:53:05 UTC  
> Closed at: 2019-02-24 18:53:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/135  

Consider the following example:  
```  
#include <string>  
  
#include <boost/archive/iterators/binary_from_base64.hpp>  
#include <boost/archive/iterators/transform_width.hpp>  
#include <boost/iterator/filter_iterator.hpp>  
  
struct cleanup_base64_input {  
  bool operator()(unsigned char t) {  
    return !(t == ' ' || t == '\n' || t == '\t' || t == '=');  
  }  
};  
  
typedef boost::archive::iterators::transform_width<  
  boost::archive::iterators::binary_from_base64<  
    boost::filter_iterator<  
      cleanup_base64_input, std::string::const_iterator  
    >  
  >,  
  8, 6  
> decoder;  
  
std::string decode(const std::string& data) {  
  return std::string(decoder(data.begin()), decoder(data.end()));  
}  
  
int main() {  
  try {  
    const int res = static_cast<int>(decode("gq5E5CI4e5uAjheO1AX4W").size());  
    return res; // Must not get here!  
  } catch(...) {  
    return 0;  
  }  
}  
```  
  
Building that example with -fsanitize=address and running it gives the following output:  
```  
==24139==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x60300000eff6 at pc 0x0000004028ff bp 0x7ffcb165e450 sp 0x7ffcb165e440  
READ of size 1 at 0x60300000eff6 thread T0  
    #0 0x4028fe in boost::iterators::filter_iterator<cleanup_base64_input, __gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::satisfy_predicate() ../../../boost/iterator/filter_iterator.hpp:100  
    #1 0x403f43 in boost::iterators::filter_iterator<cleanup_base64_input, __gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >::increment() ../../../boost/iterator/filter_iterator.hpp:90  
    #2 0x403e7d in void boost::iterators::iterator_core_access::increment<boost::iterators::filter_iterator<cleanup_base64_input, __gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >(boost::iterators::filter_iterator<cleanup_base64_input, __gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > >&) ../../../boost/iterator/iterator_facade.hpp:556  
```  
  
That's because the filter iterator is meant to be initialized with two iterators - to the begin and to the end of the range. Which does not happen here, and the iterator is only initilized with begin iterator.  
  
We'd appreciate a way to initialize the filtering iterator with additional parameters, or it's automatical initialization with additional iterator, or some kind of  static_assert on incorrect usage (if that's possible).

---

## Comment 1

> Username: robertramey  
> Created at: 2018-10-30 19:57:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/135#issuecomment-434446925  

Hmmm - wouldn't this better be directed to the boost iterator library?

---

## Comment 2

> Username: robertramey  
> Created at: 2018-10-30 20:16:06 UTC  
> Url: https://github.com/boostorg/serialization/issues/135#issuecomment-434453206  

I looked at this more carefully.  
  
I see the filter iterator is initialized with two iterators.  But the second one is optional and the end iterator is used as the default.  In this case we're invoking the constructor with only one iterator. Seems like the sanitizer is complaining about what looks to me to be perfectly legal and correct code.  Am I incorrect here?    
  
But looking at the sanitizer message more carefully, I'm thinking that it's a different problem.  When transforming width - there's a problem if the number of input characters does not translate to an integral number of output characters. So, if there are only  2 bits left - so when iterator tries to grab 6 bits it runs out of bits and accesses unallocated memory.  I've looked at this many years ago and failed to come up with a good fix.  So I make sure that the the input string is padded out to a number of bits which is divisible by 6 so it doesn't happen inside the serialization library.  But it's a problem when trying to used the data flow iterator on it's own.
