# #90 - Policy for returning references and copies [Closed]

> Username: ldionne  
> Created at: 2015-06-12 15:14:25 UTC  
> Updated at: 2016-02-06 06:36:25 UTC  
> Closed at: 2016-02-06 06:36:25 UTC  
> Url: https://github.com/boostorg/hana/issues/90  

Currently, Hana does not make any guarantee about what is returned from methods like `at` or `at_key`. Indeed, the documentation for `at_key` just says   
  
> Returns the value associated to the given key in a structure, or fail.  
  
This makes no guarantee that a reference to the value in the map should be returned. Indeed, the following code currently fails because `at_key` returns a copy of the value, which is a temporary `int`:  
  
``` c++  
#include <boost/hana.hpp>  
using namespace boost::hana;  
  
int main() {  
    auto map = make_map(  
        make_pair(int_<0>, 0)  
    );  
    map[int_<0>] = 3;  
}  
```  
  
The error is:  
  
```  
error: expression is not assignable  
    map[int_<0>] = 3;  
    ~~~~~~~~~~~~ ^  
```  
  
This causes serious usability problems. On the other hands, if we had a special `Iterable` generating values on the fly (for example an infinite `Iterable` generating the fibonacci sequence), we wouldn't be able to return a reference. Hence, the resolution is not exactly clear.  
  
Another related problem is to determine a policy for when to return a container of references. For example, consider the following code, which was submitted to me in a private email:  
  
``` c++  
#include <boost/hana.hpp>  
#include <iostream>  
using namespace boost::hana;  
using namespace boost::hana::literals;  
  
struct Person {  
    BOOST_HANA_DEFINE_STRUCT(Person,  
        (int, age)  
    );  
};  
  
int main() {  
 Person john{30};  
 members(john)[0_c] = 0;  
 // fails because the expression is not assignable  
}  
```  
  
This is because `members` returns a container holding copies of the members of the struct, not references to it. While it seems that it would be more useful to return a sequence of references, it would also create lifetime issues when the `Person` is a temporary.

---

## Comment 1

> Username: Manu343726  
> Created at: 2015-06-12 23:54:14 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-111644054  

A solution could be to differentiate between Iterables that are just the  
result of an on the fly computation (so supposed to be read only), and  
Iterables that will be mutated later. While playing with Niebler's range, I  
found the range vs view vs action namespaces he use to differentiate the  
different "sides" of the library pretty straightforward.  
  
I'm not sure if partitioning Hana algorithms into different categories (via  
namespaces) is the right way to go, but being able to "tag" algorithm  
results as read-only vs writeable is a possible solution to this dilemma.  
So if you pick the mutable version of the algorithm (or, using the tag  
approach, tag it as mutable_result) it returns an iterable of writeable  
elements, references, proxies, whatever. It you pick the read only one, it  
returns an iterable of values. This should be taken carefully, defining  
some coercing rules: while doing composition, taking a write-only from a  
read only is OK, but not vice versa.  
  
Personally, I expect any entity/iterable/sequence to be mutable in some  
way, since I'm used to the good old imperative/oo C++. Maybe the right  
solution is to encourage people to think functional, to stop thinking on  
state machines and focus on data processing. But I'm afraid many potential  
Hana users wouldn't be happy with this.  
  
El vie, 12 de jun de 2015 17:14, Louis Dionne notifications@github.com  
escribió:  
  
> Currently, Hana does not make any guarantee about what is returned from  
> methods like at or at_key. Indeed, the documentation for at_key just says  
>   
> Returns the value associated to the given key in a structure, or fail.  
>   
> This makes no guarantee that a reference to the value in the map should be  
> returned. Indeed, the following code currently fails because at_key  
> returns a copy of the value, which is a temporary int:  
>   
> #include <boost/hana.hpp>using namespace boost::hana;  
> int main() {  
>     auto map = make_map(  
>         make_pair(int_<0>, 0)  
>     );  
>     map[int_<0>] = 3;  
> }  
>   
> The error is:  
>   
> error: expression is not assignable  
>     map[int_<0>] = 3;  
>     ~~~~~~~~~~~~ ^  
>   
> This causes serious usability problems. On the other hands, if we had a  
> special Iterable generating values on the fly (for example an infinite  
> Iterable generating the fibonacci sequence), we wouldn't be able to  
> return a reference. Hence, the resolution is not exactly clear.  
>   
> Another related problem is to determine a policy for when to return a  
> container of references. For example, consider the following code, which  
> was submitted to me in a private email:  
>   
> #include <boost/hana.hpp>  
> #include <iostream>using namespace boost::hana;using namespace boost::hana::literals;  
> struct Person {  
>     BOOST_HANA_DEFINE_STRUCT(Person,  
>         (int, age)  
>     );  
> };  
> int main() {  
>  Person john{30};  
>  members(john)[0_c] = 0;  
>  // fails because the expression is not assignable  
> }  
>   
> This is because members returns a container holding copies of the members  
> of the struct, not references to it. While it seems that it would be more  
> useful to return a sequence of references, it would also create lifetime  
> issues when the Person is a temporary.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/ldionne/hana/issues/90.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-25 21:11:46 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-115399469  

One problem I have with returning references is the fact that it can create dangling references. Indeed, if `members` returns a tuple of references to the members of the struct, consider the following code:  
  
``` c++  
struct Person {  
    BOOST_HANA_DEFINE_STRUCT(Person,  
        (int, age)  
    );  
};  
  
int main() {  
    auto m = members(Person{30});  
    // m is a Tuple holding a dangling reference to Person{30}.age  
}  
```  
  
This is annoying, because from using `auto m = ...`, it would seem like you did a copy.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-09-09 16:09:28 UTC  
> Updated at: 2016-02-05 15:08:05 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-138958963  

My concern in the above comment will be handled in #175. Going back to the original issue of returning references from functions like `at_key` or `at`, the functions that need a resolution are:  
- [x] `at`  
- [x] `at_key`  
- [x] `front`  
- [x] `back`  
- [x] `first`  
- [x] `second`  
  
@Manu343726, I think a variant of what you suggest is the right way to go. Seriously, we can't possibly force these functions to return values and expect C++ programmers to use the library. I also think it is mostly a matter of documenting the contract of those functions properly, since they already return references in most cases (I think all of them do except `at_key`).  
  
While I wouldn't introduce another namespace, it would be possible to say something along the lines of: "This function must return a reference to the element whenever the object it is called on is a container with actual storage." This is not very precise mathematically, but everybody will understand. Also, this is mostly directed to users since we don't make assumptions about the value category of the things returned by those functions in Hana AFAICT. So while precision is nice, it will not actually undermine the internal consistency of Hana if this is not mathematically precise.  
  
However, there is still the issue of what should we return from a temporary. For example, what should `at_c` return when called on a temporary `tuple`?  
  
``` c++  
auto x = hana::at_c<0>(hana::make_tuple(1, 2, 3));  
```  
  
If we do like they do for `std::tuple` and `std::get`, we should return an `T&&` (in this case a dangling `int&&`). I don't like this. Another option would be to move from the temporary and return a `T`.  
  
It seems to me like returning a  `T&&` is completely crazy, but it probably isn't. I'll try to find the rationale for this choice in the standard, and that will probably determine what we'll do for Hana too.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-09-09 20:12:06 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-139032607  

From [this Stackoverflow](http://stackoverflow.com/a/30096052/627587) answer, there does not seem to be a clear cut best solution. It is clearly acknowledged that returning an rvalue reference can lead to dangling reference problems, yet the standard still does it. I'm not sure what is the best stance to adopt here.

---

## Comment 5

> Username: badair  
> Created at: 2016-02-02 05:10:16 UTC  
> Updated at: 2016-02-02 05:20:45 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-178378560  

> It seems to me like returning a T&& is completely crazy, but it probably isn't. I'll try to find the rationale for this choice in the standard, and that will probably determine what we'll do for Hana too.  
  
For a temporary `tuple`, I _really_ don't see the point in returning an rvalue reference to an element. I would love to read the standard's rationale there, because it would probably teach me something new. Did you find anything on that? If not, I'll add it to my list.

---

## Comment 6

> Username: ldionne  
> Created at: 2016-02-02 12:59:07 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-178562248  

No, I have not found anything. If you do, let me know. I think the simplest thing would be to ask Howard Hinnant directly.

---

## Comment 7

> Username: ricejasonf  
> Created at: 2016-02-02 23:23:55 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-178883051  

Sorry if this is a little late in the conversation. In the OP example with the struct, I think it would be beneficial to change the usage to be more like an asynchronous callback interface that you would expect in something like javascript. That way the reference would only be available within the context of the callback.   
(I couldn't get the Struct to unpack references so I used a `tuple` for an example.)  
  
``` cpp  
#include <boost/hana.hpp>  
namespace hana = boost::hana;  
using namespace boost::hana::literals;  
  
struct at_t {  
  template <typename S, typename I, typename Fn>  
  constexpr auto operator()(S&& s, I, Fn&& fn) const {  
    return hana::unpack(std::forward<S>(s),  
      [&](auto&&... x) {  
        return std::forward<Fn>(fn)(  
          hana::arg<I::value + 1>(std::forward<decltype(x)>(x)...)  
        );  
      }  
    );  
  }  
};  
constexpr at_t at{};  
  
int main() {  
  auto john = hana::make_tuple(30);  
  at(john, 0_c, [](auto& arg) { arg = 35; });  
  BOOST_HANA_RUNTIME_ASSERT(john[0_c] == 35);  
}  
  
```  
  
This sort of interface would also make wrapping a reference in an `optional` a lot less scary. ☕

---

## Comment 8

> Username: badair  
> Created at: 2016-02-03 03:16:08 UTC  
> Updated at: 2016-02-03 07:06:08 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-178978971  

@ldionne I know you probably realize this, but I'd like to make a clarification on your example from earlier in this thread:  
  
``` cpp  
auto x = hana::at_c<0>(hana::make_tuple(1, 2, 3));  
```  
  
As far as I know, this statement does not _actually_ involve any reference dangling. `x` is move-constructed from a valid rvalue reference to the temporary tuple's `1` (well, an `int` move is really just a copy, but you know what I mean). The temporary tuple only becomes invalid _after_ the semicolon, which means this statement is still well-defined. `x` itself would dangle only if it were declared using `auto&&` or `const auto&`, or the explicitly-typed equivalent. As always, please correct me if I'm mistaken.  
  
I bring this up not to be pedantic, but because your comment about the "dangling `int&&`" inspired me to experiment.  
  
Before drafting a question for Howard Hinnant, I wanted to toy around in Wandbox to fill any holes in my understanding of rvalue references, as well as `std::tuple`'s usage thereof. While doing this, I discovered what seems to be a discrepancy in Hana's implementation of `at_c` when comparing it to `std::get`. You may already be aware of this, and it may very well be an improvement over `std::get` regardless, but I thought I'd draw your attention to it, just in case it's a symptom of a bug. I believe it's caused by a move of a `hana::tuple` object somewhere in `hana::at_c`, although I haven't yet checked the implementation to confirm this.  
  
This little program's output depends on a colloquial expectation of what might happen when a dangling reference is accessed within the same call frame as the referenced object's destruction - in other words, "don't try this at home, kids!" There's no legitimate reason why this program doesn't segfault (or worse), and it very well may if you run it. For the Hana discrepancy, see case 2 (Wandbox link [here](http://melpon.org/wandbox/permlink/oG9rb3Ksw8pyvSk1)):  
  
``` cpp  
  
#include <boost/hana.hpp>  
#include <iostream>  
#include <string>  
#include <type_traits>  
#include <utility>  
#include <tuple>  
  
namespace hana = boost::hana;  
  
const char* const undefined = "Undefined behavior! You're lucky to read this!";  
const char* const dflt = "default";  
const char* const copied = "copied";  
const char* const moved = "moved";  
  
struct foo {  
  
    static int default_count;  
    static int copy_count;  
    static int move_count;  
  
    const char* value;  
  
    foo()  
        : value{ dflt } {  
        default_count++;  
    }  
  
    foo(const foo& f)  
        : value{ f.value == undefined? undefined : copied } {  
        copy_count++;  
    }  
  
    foo(foo&& f)  
        : value{ f.value == undefined? undefined : moved } {  
        move_count++;  
    }  
  
    ~foo(){  
        value = undefined;  
    }  
};  
  
int foo::default_count = 0;  
int foo::copy_count = 0;  
int foo::move_count = 0;  
  
void reset_counts() {  
    foo::default_count = 0;  
    foo::copy_count = 0;  
    foo::move_count = 0;  
}  
  
void print_result(const char* test_case, const char* s) {  
    std::cout << test_case << std::endl;  
    std::cout << s << std::endl;  
    std::cout << "defaults: " << foo::default_count << std::endl;  
    std::cout << "copies: " <<  foo::copy_count << std::endl;  
    std::cout << "moves: " << foo::move_count << std::endl;  
    std::cout << std::endl << std::endl;  
    reset_counts();  
}  
  
auto&& get_hana(){  
    return hana::at_c<0>(hana::make_tuple(foo{}));  
}  
  
auto&& get_std(){  
    return std::get<0>(std::make_tuple(foo{}));  
}  
  
// pls forgive  
#define GET_HANA hana::at_c<0>(hana::make_tuple(foo{}))  
#define GET_STD std::get<0>(std::make_tuple(foo{}))  
  
void case_1() {  
  
    std::cout << std::endl << "CASE 1" << std::endl << std::endl;  
  
    // The tuple temporary is destroyed at the semicolon in both  
    // get_hana and get_std (and anywhere else in C++). Reference  
    // forwarding can't save us from that. (Neither does inlining,  
    // which is probably obvious to those reading, but perhaps  
    // worth noting)  
    {  
        auto dangle = get_std().value;  
        print_result("STD TEMPORARY DANGLE", dangle);  
  
        // value is accessed before the end of the expression, and  
        // therefore before the tuple temporary is destructed as well  
        auto no_dangle = GET_STD.value;  
        print_result("STD TEMPORARY NO DANGLE", no_dangle);  
    }  
  
    {  
        // Hana has the same behavior as the standard libary here  
        auto dangle = get_hana().value;  
        print_result("HANA TEMPORARY DANGLE", dangle);  
  
        auto no_dangle = GET_HANA.value;  
        print_result("HANA TEMPORARY NO DANGLE", no_dangle);  
    }  
}  
  
void case_2() {  
  
    std::cout << std::endl << "CASE 2" << std::endl << std::endl;  
  
    {  
        // Move-constructing a foo object named move_dangle. Since the  
        // tuple temporary destruction is sequenced before the function  
        // return, the rvalue reference passed to the move constructor  
        // of foo is invalid. The ?: expression in the move constructor  
        // informs us, colloquially, of the undefined behavior we induce here  
        auto move_dangle = get_std();  
        print_result("STD MOVE DANGLE", move_dangle.value);  
  
        auto move_no_dangle = GET_STD;  
        print_result("STD MOVE NO DANGLE", move_no_dangle.value);  
    }  
  
    {  
        // Hana does not have the same behavior as the standard library here  
        std::cout << "*** This doesn't actually dangle... "  
                     "Why does the foo move constructor receive " << std::endl  
                  << " a valid rvalue reference here? ***" << std::endl;  
  
        auto move_dangle = get_hana();  
        print_result("HANA MOVE DANGLE", move_dangle.value);  
  
        auto move_no_dangle = GET_HANA;  
        print_result("HANA MOVE NO DANGLE", move_no_dangle.value);  
    }  
}  
  
void case_3() {  
  
    std::cout << std::endl << "CASE 3" << std::endl << std::endl;  
  
    // these are obvious, but lets include them for fun :)  
    {  
        auto&& double_dangle = get_std();  
        print_result("STD DOUBLE DANGLE", double_dangle.value);  
  
        auto&& single_dangle = GET_STD;  
        print_result("STD SINGLE DANGLE", single_dangle.value);  
    }  
  
    {  
        auto&& double_dangle = get_hana();  
        print_result("HANA DOUBLE DANGLE", double_dangle.value);  
  
        auto&& single_dangle = GET_HANA;  
        print_result("HANA SINGLE DANGLE", single_dangle.value);  
    }  
  
}  
  
int main() {  
  
    case_1();  
    case_2();  
    case_3();  
  
    return 0;     
}  
  
```  
  
Please correct me if the code above indicates a misunderstanding on my part.  
  
The things I learned here tell me that the fact that `std::get` invoked with an `std::tuple<...>&&` returns an rvalue to the element is not particularly problematic after all. In fact, I think it's the _right_ _choice_. The only "gotcha" I can find here is if one tries to juggle the returned reference to a _temporary_ tuple's element, which I'd imagine to be a distant edge case. In all other cases I can imagine, I think it's better to return an rvalue reference to the element, as the standard already does.   
  
It seems to me the standard's rationale for the behavior of `std::get` invoked with a temporary `std::tuple` is to allow an element to call "MAYDAY" from aboard a sinking `tuple` ship, without risking an undesired constructor call. You can toss the element a life preserver (pass it to a move constructor), or you can just let it drown (reach the statement termination).  
  
I have not yet written a question to Howard Hinnant, since I've unconvinced myself of the idea that there is anything wrong with the standard implementation.  
  
We could try to ping him at @ ... HowardHinnant.  
  
@ricejasonf That would certainly work. My gut tells me that requiring a lambda argument asks a lot of boilerplate from the user. If we're going to allow the user to determine the value category of the return type, I'd be more inclined to take a tag as a template argument - something like `hana::at_c<0, hana::r_ref>(my_tup)` simply for the sake of brevity. However, we raise the question of whether we should allow that level of customization on return types. Hana currently seems to err on the side of "take whatever I give you" as opposed to "tell me what you want", in terms of value categories of return types, an approach that I tend to like.  
  
I think the real problem here is that `operator[]` can return an unexpected temporary. If it returned an lvalue reference, we could then still cast it to an rvalue reference if desired (`std::move`), and assign it if it isn't const etc. This brings us to the reservation here from @ldionne :  
  
> if we had a special Iterable generating values on the fly (for example an infinite Iterable generating the fibonacci sequence), we wouldn't be able to return a reference. Hence, the resolution is not exactly clear.  
  
 Can we `std::conditional` the return type of `operator[]` (or SFINAE the entire implementation) to return an rvalue reference for temporaries and an lvalue reference for lvalues? I'm speaking off the cuff here, since (once again) I haven't yet looked at the implementation.  
  
**Edit:** I realize now that my code comments referred to the destruction of the _foo_ temporary, which was somewhat misleading because the original foo temporary is moved into the tuple, which means the original foo's destruction is inconsequential. It's really the destruction of the _tuple_ temporary, and its elements along with it, that affects the example code. I updated the code comments in this thread, but I left the Wandbox code alone. The meaning/purpose of both remains unchanged.  
  
**Edit 2:** I believe the culprit of the [possible] issue in case 2 can be narrowed down to the tag dispatch result that handles [this apply call](https://github.com/boostorg/hana/blob/master/include/boost/hana/at.hpp#L41). I'm not familiar enough with BOOST_HANA_DISPATCH_IF to continue mentally debugging this tonight.

---

## Comment 9

> Username: ldionne  
> Created at: 2016-02-04 20:13:06 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-180028525  

First of all, I don't yet understand why the behaviour of `hana::at_c` and `std::get` differ in the CASE 2 of the example you give above. Given the actual implementation of `hana::at_c` for `hana::tuple`, I would expect it to behave exactly like `std::tuple`'s `std::get`. This is something that I will need to investigate, because I'm not even sure which behaviour should be expected.  
  
Coming back to this:  
  
``` c++  
auto x = hana::at_c<0>(hana::make_tuple(1, 2, 3));  
```  
  
As far as _I_ understand, `hana::at_c<0>` returns a rvalue-reference to the temporary `int` inside the temporary `hana::tuple`. However, by the end of the expression (`hana::at_c<0>(hana::make_tuple(1, 2, 3))`), that temporary tuple is destructed, and the returned reference is thus left dangling. This dangling reference is then used to move-construct `x`. In other words, I think the temporary `int` goes out of scope at the end of the `hana::at_c<0>(...)` expression, not the semi-colon. At least, this is what I believed when I wrote my initial comment.  
  
Otherwise, it is also possible that the expression must be interpreted as being   
  
``` c++  
int i;  
new (&i) int(hana::at_c<0>(hana::make_tuple(1, 2, 3))); // <-- in-place new expression (sort of?)  
```  
  
In which case the temporary `int` would go out of scope at the end of the expression, which is indeed **after** `i` has been constructed. If that is the case, then my initial reserve about returning an rvalue-reference from a temporary tuple does not apply anymore.  
  
@ricejasonf Regarding the use of callbacks to access elements; I think this is way too restrictive and would lead to hard-to-read code in many cases. I think we can settle on a good-enough solution that will not require going through all these hoops just for basic functionality like getting the n-th element of a sequence.

---

## Comment 10

> Username: ldionne  
> Created at: 2016-02-04 20:21:39 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-180032277  

@HowardHinnant  
  
Would you happen to know why the Standard Committee decided to have `std::get` return an rvalue-reference when called on a temporary tuple? To me, it seems like it would have been safer to return a value in this case, and it would only incur the cost of a move-construction. What is the rationale for not doing this?  
  
Also, am I right to think that   
  
``` c++  
std::string s = std::get<0>(std::make_tuple(std::string{"abcdef"}));  
```  
  
causes `s` to be move-constructed from a dangling rvalue-reference to the now-destructed `std::string{"abcdef"}` temporary? Or is `std::string{"abcdef"}` just destructed after the semi-colon?

---

## Comment 11

> Username: HowardHinnant  
> Created at: 2016-02-04 20:41:37 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-180040719  

Here are links to the papers.  They are light on motivation and don't discuss the possibility of returning by value:  
http://cplusplus.github.io/LWG/lwg-defects.html#1191  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2009/n2975.pdf  
  
And no, the temporary `string` won't be destructed until the sequence point which is indicated by the `;`.  
  
http://melpon.org/wandbox/permlink/9DfLBShsv0GueCTN

---

## Comment 12

> Username: ldionne  
> Created at: 2016-02-04 21:15:23 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-180054791  

Thanks a lot Howard. Out of curiosity, I will try to reach out to Alisdair to inquire about the rationale for `std::get`'s signature. Nevertheless, it seems like we almost have a resolution for this issue.  
  
Indeed, from my updated understanding, I think the only case in which it would be safer to return a `T` instead of a `T&&` from `std::get` on a temporary tuple is if one tries to bind the result to a `T const&`. Indeed, if `std::get` returned a `T`, binding it to a `T const&` would extend its lifetime and everything would be fine. However, returning a `T&&` and binding it to a `T const&` would make the `T const&` a dangling reference to the temporary destructed after the next semi-colon.  
  
If this is correct, and if this is about the only case in which it is safer to return a `T` than a `T&&` from `std::get`, then I think it is not a sufficient concern to justify an additional move operation. It would seem that the right thing to do is to go the standard's way and return a `T&&`, while documenting the potential for a dangling reference if not careful.  
  
Does that make sense?

---

## Comment 13

> Username: ldionne  
> Created at: 2016-02-04 21:19:51 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-180057218  

Since it seems like everybody is on GitHub these days, let's ask Alisdair right here :-)  
  
@AlisdairM, would you happen to know the rationale for returning a rvalue-reference from `std::get` when called on a temporary tuple? It seems like a valid choice would have been to return a temporary value instead. This would have incurred an additional move-construction, but it might have been safer in some cases. Was this considered?

---

## Comment 14

> Username: HowardHinnant  
> Created at: 2016-02-04 21:52:57 UTC  
> Updated at: 2016-02-04 21:54:16 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-180067503  

I went through a similar design decision with rvalue-string + lvalue-string.  It originally returned an rvalue reference to the left argument.  I later changed it to return by value, moving from the modified left argument because of the dangling reference possibility.  In this case I had the advantage that I _knew_ the move would be cheap.  In the case of `get<I>(tuple)` we don't know if the move is going to be cheap or not.  So the client needs to be careful not to dangle.

---

## Comment 15

> Username: badair  
> Created at: 2016-02-05 00:35:35 UTC  
> Updated at: 2016-02-05 01:11:09 UTC  
> Url: https://github.com/boostorg/hana/issues/90#issuecomment-180124756  

> Does that make sense?  
  
@ldionne Yes, and I agree with your conclusion.  
  
I applied Howard's state machine to the case 2 example. It looks like Hana is dangling after all, like the standard ([Wandbox](http://melpon.org/wandbox/permlink/Au9rvPY3HSEUI8rq)). Hana's result here is usually garbage memory, which leads me to believe the original output was caused by the wrath of the UB gods. I apologize for the confusion.  
  
@HowardHinnant Thank you for the links and example.
