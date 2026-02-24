# #314 - Question: Attribute Annotations [Open]

> Username: felixguendling  
> Created at: 2016-12-30 12:00:30 UTC  
> Updated at: 2018-06-21 15:09:07 UTC  
> Url: https://github.com/boostorg/hana/issues/314  

I'm interested in the "struct reflection" features of Boost.Hana. I think that this part of Boost.Hana is especially suited for all kinds of serialization/marshalling + deserialization/unmarshalling (file reading/writing, network/API protocols, database access, etc.).  
  
Other languages offer a functionality to "annotate" member variables:  
- Python and Java have "@" (used for Hibernate, Jackson, etc.)  
- In Go you can use something like `json:"var_name"` (example: https://play.golang.org/p/BMeR8p1cKf)  
- etc.  
  
Use cases include JSON/XML attribute names (API naming conventions can be different to variable naming), parser information (i.e. CSV column names, XPath for XML to struct parsing), optional/required/versioning information for formats like Protocol Buffers or Flatbuffers, or database information ("PRIMARY KEY", "UNIQUE", "NOT NULL", etc. - this way SELECT, CREATE TABLE statements, etc. could be automatically generated from the struct definition at compile time - see https://github.com/rbock/sqlpp11).  
  
Is there already a way to do something like this (i.e. generic additional attribute "tags") or (if that's not the case) are there any plans to extend Boost.Hana?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-12-30 16:02:13 UTC  
> Url: https://github.com/boostorg/hana/issues/314#issuecomment-269787952  

I've been using Hana to do this sort of thing. Check this out:  
  
https://github.com/ricejasonf/nbdl/blob/master/example/pipes/main.cpp#L94  
  
Instead of `hana::Struct` though I have tuples of  'pointer to member' wrappers. I would use the wrapper as a tag for dispatching validation rules and what not.

---

## Comment 2

> Username: daminetreg  
> Created at: 2017-01-03 11:27:17 UTC  
> Updated at: 2017-01-03 14:47:26 UTC  
> Url: https://github.com/boostorg/hana/issues/314#issuecomment-270095486  

Hi @felixguendling,  
Just my 2 cents : I made an example to show that basically with what Hana or Fusion provides and a bit of macro hacking you can make member annotations really easily.   
  
It's really far from complete, finished nor perfect, but I made an example that I'll make into a reusable library : https://github.com/daminetreg/annotate/blob/master/member_annotate.cpp#L147 ,   
  
The only thing you need is to be able to retrieve and identify each member. In my example I use the resulting BOOST_METAPARSE_STRING to identify the member uniquely with a type. I think with what Hana generates from it's ADAPT* macros it should be possible to avoid the use I do from BOOST_METAPARSE_STRING.   
  
I made it this way because I also need to annotate bitfields, and bitfields members cannot be pointed to (even with member reference pointers), therefore I needed another way to uniquely address the field, without changing the size of the struct.

---

## Comment 3

> Username: ldionne  
> Created at: 2017-01-05 05:36:37 UTC  
> Url: https://github.com/boostorg/hana/issues/314#issuecomment-270567930  

I believe that might be a useful feature. It's unclear to me whether Hana is the right place to put this feature, or whether a separate library (as discussed above) would be better, but it does seem useful to have.  
  
I'll try to play around with a few ideas I have and I'll post any development here.

---

## Comment 4

> Username: danielchen0  
> Created at: 2018-06-20 16:29:28 UTC  
> Updated at: 2018-06-20 16:40:32 UTC  
> Url: https://github.com/boostorg/hana/issues/314#issuecomment-398813272  

@ldionne Have there been any developments on this sort of feature? Right now, I am trying to write bitfields, like so:  
  
     #include <boost\hana.hpp>  
  
     struct Bits {  
     	uint32_t opCode : 5;  
     	uint32_t header : 3;  
     	uint32_t source : 5;  
     	uint32_t destination : 5;  
     	uint32_t offset : 13;  
     };  
  
     BOOST_HANA_ADAPT_STRUCT(Bits, opCode, header, source, destination, offset);  
  
And it is giving me error E0139 "taking the address of a bit field is not allowed".  
  
@daminetreg Would your reusable library work for this?

---

## Comment 5

> Username: daminetreg  
> Created at: 2018-06-20 19:16:22 UTC  
> Url: https://github.com/boostorg/hana/issues/314#issuecomment-398865014  

@d223chen what do you plan to do with the adapted struct ?  
  
The idea I mentioned possibly makes something different than what you explicitly want. But indeed it's possible to implement adapted structs for bitfields.  
  
In the proposed idea, and as in BOOST_FUSION_ADAPT_STRUCT we don't take reference or address to the fields, then it works for bitfields too.   
  
The principle ( *i.e.* generating accessors instead of taking field address ) with which I implemented the example could be generalized to HANA_ADAPT_STRUCT as well I imagine and would work for bitfields.   
  
From what I know from my work by Fusion, you might be able to adapt it for HANA you can build on top of something like this :   
  
```cpp  
#include <type_traits>  
#include <functional>  
#include <cstdint>  
#include <iostream>  
  
#include <boost/fusion/include/adapt_struct.hpp>   
  
#include <boost/fusion/sequence/intrinsic/begin.hpp>  
#include <boost/fusion/sequence/intrinsic/end.hpp>  
#include <boost/fusion/sequence/intrinsic/front.hpp>  
#include <boost/fusion/iterator/equal_to.hpp>  
#include <boost/fusion/iterator/next.hpp>  
#include <boost/fusion/iterator/deref.hpp>  
#include <boost/fusion/iterator/distance.hpp>  
#include <boost/fusion/support/category_of.hpp>  
#include <boost/fusion/include/tag_of.hpp>  
#include <boost/mpl/bool.hpp>  
  
  
  template <typename First, typename Last, typename F, typename Sequence>  
  inline void  
  for_each_member_linear(First const first,   
      Last const last,   
      F const& f,  
      Sequence&,  
      boost::mpl::true_) {}  
  
  template <typename First, typename Last, typename F, typename Sequence,  
  typename std::enable_if<   
    std::is_same<  
      typename boost::fusion::traits::tag_of<Sequence>::type,  
      boost::fusion::struct_tag  
    >::value  
  >::type* = nullptr>  
  inline void  
  for_each_member_linear(First first,   
      Last const last,  
      F const& f,  
      Sequence& seq,  
      boost::mpl::false_) {  
  
      f(  
				boost::fusion::extension::struct_member_name<  
					typename std::remove_cv< typename First::seq_type>::type , First::index::value  
				>::call()  
			);  
  
      for_each_member_linear(  
          next(first),   
          last,  
          f,  
          seq,  
          boost::fusion::result_of::equal_to< typename boost::fusion::result_of::next<First>::type, Last>()  
      );  
  }  
  
  
 template <typename First, typename Last, typename F, typename Sequence,  
  typename std::enable_if<   
    std::is_same<  
      typename boost::fusion::traits::tag_of<Sequence>::type,  
      boost::fusion::boost_tuple_tag  
    >::value  
  >::type* = nullptr>  
  inline void  
  for_each_member_linear(First first,   
      Last const last,  
      F const& f,  
      Sequence& seq,  
      boost::mpl::false_) {  
  
      auto index = boost::fusion::result_of::distance<  
                     typename boost::fusion::result_of::begin<Sequence>::type,   
                     First  
                   >::type::value;   
      std::string name =  typeid(decltype(*first)).name();  
  
      f(  
				 (name + std::string("_") + std::to_string(index)).data()  
			);  
  
      for_each_member_linear(  
          next(first),   
          last,  
          f,  
          seq,  
          boost::fusion::result_of::equal_to< typename boost::fusion::result_of::next<First>::type, Last>()  
      );  
  }  
  
  template <typename Sequence, typename F>  
  inline void  
  for_each_member(Sequence& seq, F const& f) {  
  
    detail::for_each_member_linear(  
      boost::fusion::begin(seq),   
      boost::fusion::end(seq),   
      f,   
      seq,  
      boost::fusion::result_of::equal_to<  
        typename boost::fusion::result_of::begin<Sequence>::type,   
        typename boost::fusion::result_of::end<Sequence>::type>()  
    );  
  }  
  
}  
  
  template <typename Sequence, typename F>  
  inline void  
  for_each_member(Sequence& seq, F f) {  
    detail::for_each_member(seq, f);  
  }  
   
}}  
  
  
 struct Bits {  
 	uint32_t opCode : 5;  
 	uint32_t header : 3;  
 	uint32_t source : 5;  
 	uint32_t destination : 5;  
 	uint32_t offset : 13;  
 };  
  
 BOOST_FUSION_ADAPT_STRUCT(Bits, opCode, header, source, destination, offset);  
  
  
  
 int main(int argc, char** argv) {  
  
   Bits b;  
   for_each_member(b, [](const char* name) { std::cout << "name : " << name << std::endl; } );  
      
   return 0;  
 }  
  
```  
  
Will print :   
  
```  
daminetreg@daminetreg-precision5510:~/workspace/experiments$ g++-7 -std=c++17 fusionbitfields.cpp -o fusionbitfields  
daminetreg@daminetreg-precision5510:~/workspace/experiments$ ./fusionbitfields   
name : opCode  
name : header  
name : source  
name : destination  
name : offset  
```

---

## Comment 6

> Username: danielchen0  
> Created at: 2018-06-21 15:08:49 UTC  
> Updated at: 2018-06-21 15:09:07 UTC  
> Url: https://github.com/boostorg/hana/issues/314#issuecomment-399137747  

Hi @daminetreg. Thanks for the detailed response. My goal is to be able to get things like:  
  
1. All member names as std::string list ex. "opCode", "header", etc...  
2. The underlying typename as a std::string ex. "uint32_t"  
3. The number of bits for each field as a std::int list ex. 5,3,5,5,13  
  
Your code helps me to achieve point 1.   
  
I have decided to make a [StackOverflow post](https://stackoverflow.com/questions/50971872/c-reflection-for-bit-field-structs) for this as I think there will be a broader audience there. I cite this thread in that post.   
  
Thanks again!
