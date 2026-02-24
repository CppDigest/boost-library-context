# #349 - Internal compiler error with a simple hana example [Closed]

> Username: Peregring-lk  
> Created at: 2017-05-24 17:13:05 UTC  
> Updated at: 2017-05-25 09:22:57 UTC  
> Closed at: 2017-05-25 09:19:56 UTC  
> Url: https://github.com/boostorg/hana/issues/349  

I don't know how to make a bug report, so, I put the issue here. The code sample is:  
  
```c++  
// main.cpp  
  
#include <boost/hana.hpp>  
#include <iostream>  
#include <type_traits>  
  
struct my_tag {};  
  
namespace boost {  
    namespace hana {  
        template<class T>  
        struct tag_of<T, when<std::is_same<T, int>{}> >  
        {  
            using type = my_tag;  
        };  
    }  
}  
  
int main()  
{  
    std::cout << std::is_same<my_tag, boost::hana::tag_of<int>::type>::value  
              << std::endl;  
}  
```  
  
The compiler error is (my gcc version is 6.3.0):  
  
 ```bash  
$> g++ -std=c++14 -Werror -Wall -pedantic -pedantic-errors -Wextra main.cpp  
  
In file included from /usr/local/include/boost/hana/concept/struct.hpp:18:0,  
                 from /usr/local/include/boost/hana/accessors.hpp:15,  
                 from /usr/local/include/boost/hana.hpp:58,  
                 from main.cpp:1:  
/usr/local/include/boost/hana/core/tag_of.hpp: In instantiation of ‘struct boost::hana::tag_of<int>’:  
main.cpp:27:63:   required from here  
/usr/local/include/boost/hana/core/tag_of.hpp:22:12: internal compiler error: in unify, at cp/pt.c:20188  
     struct tag_of : tag_of<T, when<true>> { };  
            ^~~~~~  
0x61cda8 unify  
	../../src/gcc/cp/pt.c:20188  
0x61d094 unify  
	../../src/gcc/cp/pt.c:19980  
0x61cadf unify  
	../../src/gcc/cp/pt.c:20059  
0x61d094 unify  
	../../src/gcc/cp/pt.c:19980  
0x61de46 get_partial_spec_bindings  
	../../src/gcc/cp/pt.c:20749  
0x61a69c most_specialized_partial_spec  
	../../src/gcc/cp/pt.c:21021  
0x625123 instantiate_class_template_1  
	../../src/gcc/cp/pt.c:9812  
0x625123 instantiate_class_template(tree_node*)  
	../../src/gcc/cp/pt.c:10377  
0x686c8d complete_type(tree_node*)  
	../../src/gcc/cp/typeck.c:131  
0x686d2f complete_type_or_maybe_complain(tree_node*, tree_node*, int)  
	../../src/gcc/cp/typeck.c:143  
0x5ef539 xref_basetypes(tree_node*, tree_node*)  
	../../src/gcc/cp/decl.c:12933  
0x6253f5 instantiate_class_template_1  
	../../src/gcc/cp/pt.c:9966  
0x6253f5 instantiate_class_template(tree_node*)  
	../../src/gcc/cp/pt.c:10377  
0x686c8d complete_type(tree_node*)  
	../../src/gcc/cp/typeck.c:131  
0x665463 cp_parser_nested_name_specifier_opt  
	../../src/gcc/cp/parser.c:6104  
0x663401 cp_parser_simple_type_specifier  
	../../src/gcc/cp/parser.c:16255  
0x65fcad cp_parser_type_specifier  
	../../src/gcc/cp/parser.c:15936  
0x661f52 cp_parser_type_specifier_seq  
	../../src/gcc/cp/parser.c:20059  
0x66a662 cp_parser_type_id_1  
	../../src/gcc/cp/parser.c:19921  
0x66a77e cp_parser_template_type_arg  
	../../src/gcc/cp/parser.c:19980  
Please submit a full bug report,  
with preprocessed source if appropriate.  
Please include the complete backtrace with any bug report.  
See <file:///usr/share/doc/gcc-6/README.Bugs> for instructions.  
```  
My hana version is 1.1.0.  
  
However, if I replace the specialization line by:  
  
        template<class T>  
        struct tag_of<T, when<std::is_same<T, int>{}()> >  
        {  
            using type = my_tag;  
        };  
  
or by  
  
        template<class T>  
        struct tag_of<T, when<std::is_same<T, int>::value> >  
        {  
            using type = my_tag;  
        };  
  
it works, but not for the boolean implicit conversion case, as shown in the tutorials:  
  
```c++  
struct BoostFusionVector;  
namespace boost { namespace hana {  
    template <typename T>  
    struct tag_of<T, when<  
        std::is_same<  
            typename fusion::traits::tag_of<T>::type,  
            fusion::traits::tag_of<fusion::vector<>>::type  
        >{}  
    >> {  
        using type = BoostFusionVector;  
    };  
}}  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2017-05-25 09:19:56 UTC  
> Url: https://github.com/boostorg/hana/issues/349#issuecomment-303963919  

This bug is not Hana-specific, it is a GCC problem. Here's a reproduction not involving Hana:  
  
```c++  
#include <type_traits>  
  
template <typename T, bool = true>  
struct tag_of { };  
  
template <typename T>  
struct tag_of<T, std::is_same<T, int>{}> { };  
  
template struct tag_of<int>;  
  
int main() { }  
```  
  
[Live example](https://wandbox.org/permlink/4Ugy4cJTjNGQeAfb). You can report the bug at https://gcc.gnu.org/bugzilla, but I think I might remember reporting it already at some point (I've fixed a bunch of places that used to use this problematic pattern in Hana when I ported to GCC 6). Closing as invalid (for Hana).

---

## Comment 2

> Username: ldionne  
> Created at: 2017-05-25 09:22:57 UTC  
> Url: https://github.com/boostorg/hana/issues/349#issuecomment-303964506  

> it works, but not for the boolean implicit conversion case, as shown in the tutorials  
  
Oh, and I'll change the tutorial to avoid future confusion. Thanks for that.
