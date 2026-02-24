# #55 Add missing const to adjacent_filtered_range [Closed]

> Username: tonyelewis  
> Created at: 2017-09-09 20:52:36 UTC  
> Updated at: 2017-09-14 19:14:58 UTC  
> Closed at: 2017-09-14 19:09:42 UTC  
> Url: https://github.com/boostorg/range/pull/55  

I'm not sure what `adjacent_filtered_excl` does as it's undocumented and isn't referred to in other code. However, if it's going to persist in the code base, I think it should have this `const` added.  
  
This motivating example:  
  
~~~~~.cpp  
#include <boost/range.hpp>  
#include <boost/range/adaptor/adjacent_filtered.hpp>  
  
#include <iostream>  
#include <vector>  
  
int main() {  
	const std::vector<int> a = { 0, 1, 2, 3, 2, 1, 0 };  
  
	a | boost::adaptors::adjacent_filtered_excl(  
		[] (const int &x, const int &y) {  
			return x > y;  
		}  
	);  
}  
~~~~~  
  
…currently fails to compile with these GCC errors:  
  
~~~~~  
In file included from a.cpp:2:0:  
/home/lewis/range/include/boost/range/adaptor/adjacent_filtered.hpp: In instantiation of ‘boost::range_detail::adjacent_filtered_range<BinPredicate, ForwardRng, false> boost::range_detail::operator|(const ForwardRng&, const boost::range_detail::adjacent_excl_holder<BinPredicate>&) [with ForwardRng = std::vector<int>; BinPredicate = main()::<lambda(const int&, const int&)>]’:  
a.cpp:14:2:   required from here  
/home/lewis/range/include/boost/range/adaptor/adjacent_filtered.hpp:190:79: error: could not convert ‘boost::range_detail::adjacent_filtered_range<main()::<lambda(const int&, const int&)>, const std::vector<int>, false>((& f)->boost::range_detail::adjacent_excl_holder<main()::<lambda(const int&, const int&)> >::<anonymous>.boost::range_detail::holder<main()::<lambda(const int&, const int&)> >::val, (* & r))’ from ‘boost::range_detail::adjacent_filtered_range<main()::<lambda(const int&, const int&)>, const std::vector<int>, false>’ to ‘boost::range_detail::adjacent_filtered_range<main()::<lambda(const int&, const int&)>, std::vector<int>, false>’  
                                            const ForwardRng, false>( f.val, r );  
                                                                               ^  
~~~~~  
  
…or these Clang errors:  
  
~~~~~  
In file included from a.cpp:2:  
/home/lewis/range/include/boost/range/adaptor/adjacent_filtered.hpp:189:20: error: no viable conversion from returned value of type 'adjacent_filtered_range<[...], const vector<[2 * ...]>, [...]>' to function return type 'adjacent_filtered_range<[...], vector<[2 * ...]>, [...]>'  
            return adjacent_filtered_range<BinPredicate,  
                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
a.cpp:10:4: note: in instantiation of function template specialization 'boost::range_detail::operator|<std::__1::vector<int, std::__1::allocator<int> >, (lambda at a.cpp:11:3)>' requested here  
        a | boost::adaptors::adjacent_filtered_excl(  
          ^  
/home/lewis/range/include/boost/range/adaptor/adjacent_filtered.hpp:110:16: note: candidate constructor (the implicit copy constructor) not viable: no known conversion from 'adjacent_filtered_range<(lambda at a.cpp:11:3), const std::__1::vector<int, std::__1::allocator<int> >, false>' to 'const boost::range_detail::adjacent_filtered_range<(lambda at a.cpp:11:3), std::__1::vector<int, std::__1::allocator<int> >, false> &' for 1st argument  
        struct adjacent_filtered_range  
               ^  
/home/lewis/range/include/boost/range/adaptor/adjacent_filtered.hpp:110:16: note: candidate constructor (the implicit move constructor) not viable: no known conversion from 'adjacent_filtered_range<(lambda at a.cpp:11:3), const std::__1::vector<int, std::__1::allocator<int> >, false>' to 'boost::range_detail::adjacent_filtered_range<(lambda at a.cpp:11:3), std::__1::vector<int, std::__1::allocator<int> >, false> &&' for 1st argument  
        struct adjacent_filtered_range  
               ^  
/home/lewis/range/include/boost/range/iterator_range_core.hpp:236:5: note: candidate function  
    operator unspecified_bool_type() const  
    ^  
1 error generated.  
~~~~~  
  
…but compiles correctly with the `const` added.

---

## Comment 1

> Username: tonyelewis  
> Created_at: 2017-09-14 19:14:58 UTC  
> Url: https://github.com/boostorg/range/pull/55#issuecomment-329581642  

I've closed this and created #56 to put the changes on a separate branch.

---
