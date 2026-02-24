# #395 - Naive questions about hana::map [Closed]

> Username: Char-Aznable  
> Created at: 2018-05-02 05:53:09 UTC  
> Updated at: 2018-05-29 22:16:10 UTC  
> Closed at: 2018-05-29 22:16:10 UTC  
> Url: https://github.com/boostorg/hana/issues/395  

Hi, I'm considering using `hana::map` to implement a container that zips together a list of objects of different types and allows the user to access the objects by the "tag" of these types in an expressive way. Here's what I've come up so far:  
```  
#include<iostream>  
#include <typeinfo>  
#include <boost/hana.hpp>  
#include <string>  
#include <utility>  
namespace hana = boost::hana;  
  
using namespace std;  
  
struct Name {  
  using value_type = std::string;  
};  
  
auto name = hana::type_c<Name>;  
  
struct Age {  
  using value_type = int;  
};  
  
auto age = hana::type_c<Age>;  
  
template < class ... Args >  
auto create_person(Args&&...args) {  
  return hana::make_map(  
      hana::make_pair(  
        std::forward<Args>(args),   
        typename std::decay_t<Args>::type::value_type())  
      ...  
  );  
}  
  
int main(int argc, char* argv[]) {  
  auto person = create_person(name,age);  
  person[name] = "John";   
  person[age] = 30;   
  cout << person[name] << ", " << person[age] << endl;   
}  
```  
My questions are:  
1) Is there any runtime overhead in looking the value by key in the map object in general? i.e., ` person[name] = "John"; ` should be equivalent to simply mutating a `std::string` object in terms of runtime performance. I assume the answer is no but I want to make sure that's true in general even if `Name::value_type` or `Age::value_type` is more complicated object.  
2) Should perfect forwarding be avoided when passing the type constant to hana's metafunctions as in the above `create_person` example?  
3) Is this the best way of doing it? I'm totally new to this library and suggestions are welcome.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2018-05-02 17:08:09 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386050503  

Hello!  
  
1. No there should not be any run-time overhead in a `hana::map` lookup as it relies on type information only. It is the same as looking up a value in a tuple.  
2. If you know your args will always be stateless as is the case with `hana::type`, then you do not need to use perfect forwarding. You can then receive `args` by value and not bother with decaying `Args`.  
3. For the most part this seems fine. You could take it a step further and make your own key types that allow the user to provide an initial value. Your `create_person` function would then take pairs. It could use the `operator=`. something like this:  
```cpp  
create_person(name = "John", age  = "30");  
```  
[Dyno](https://github.com/ldionne/dyno) does something like this with compile-time strings.  
  
Also here is an example from my own library: [FullDuplex]( https://github.com/ricejasonf/full_duplex/blob/7d13ff4f9906cfcc8f96e23dd4900de1d733d198/include/full_duplex/event.hpp)  
  
Yours would be unique in that it has the type of the value embedded in the key which is interesting.

---

## Comment 2

> Username: Char-Aznable  
> Created at: 2018-05-02 19:46:15 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386098019  

@ricejasonf Thanks for the suggestion! The `operator=` overloading in your FullDuplex example is exactly what I want. I steal the idea to overload `operator<<` instead because `operator=` needs to be overloaded within each of my tag classes.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2018-05-02 19:52:31 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386099918  

The `operator=` trick makes it look like a python dictionary which is why I like it.

---

## Comment 4

> Username: Char-Aznable  
> Created at: 2018-05-02 21:27:13 UTC  
> Updated at: 2018-05-02 21:48:18 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386126914  

@ricejasonf I do like the idea of `operator=` but I might need some macro trick to declare `operator=` for each of Name, Age,... unless I'm missing something. I'm sticking with `operator<<` for now.   
  
Actually I have more imminent questions: if I want to do   
```  
  auto people = create_people(  
    create_person(name << "Joe", age << 27),  
    create_person(name << "Mike", age << 24)  
  );  
```  
with `create_people` currently being:  
```  
template < class ... Person >  
auto create_people(Person... person) {  
  return hana::make_map( //how do I loop over the keys here?  
    hana::make_pair(name, decltype(name)::type::vector_type{person[name]...}),   
    hana::make_pair(age, decltype(age)::type::vector_type{person[age]...})  
  );  
}  
```  
and the `::vector_type` is just `std::vector<::value_type>`. How do I loop over the keys in the `person` map (which is key -> value_type) without referring to them explicitly and create a new map: key -> vector_type as in the above `create_people` example? Even if this is doable somehow,would be runtime copying involved going from the map of `create_person` to the map of `create_people`? It would be ideal if the copying is optimized out. Basically, I'm faking a "Struct of Arrays" as a "Array of Structs" for some computationally intensive code and I want the user to be ignorant of the underlying "Struct of Arrays" but stick with the more intuitive "Array of Structs" interface.   
  
Here's my complete example:  
```  
#include<iostream>  
#include <typeinfo>  
#include <boost/hana.hpp>  
#include <string>  
#include <utility>  
#include <vector>  
#include <algorithm>  
namespace hana = boost::hana;  
  
using namespace std;  
  
struct Name {  
  using value_type = std::string;  
  using vector_type = std::vector<value_type>;  
};  
  
auto name = hana::type_c<Name>;  
  
struct Age {  
  using value_type = int;  
  using vector_type = std::vector<value_type>;  
};  
  
auto age = hana::type_c<Age>;  
  
template < class T >  
constexpr auto operator<<(T&& t, const typename std::decay_t<T>::type::value_type& v) {  
  return hana::make_pair(std::forward<T>(t), v);   
}  
  
template < class ... Pairs >  
auto create_person(Pairs&&...pairs) {  
  return hana::make_map(std::forward<Pairs>(pairs)...);  
}  
  
template < class ... Person >  
auto create_people(Person... person) {  
  return hana::make_map(  //how do I loop over the keys here?  
    hana::make_pair(name, decltype(name)::type::vector_type{person[name]...}),  
    hana::make_pair(age, decltype(age)::type::vector_type{person[age]...})  
  );  
}  
  
int main(int argc, char* argv[]) {  
  auto people = create_people(  
    create_person(name << "Joe", age << 27),  
    create_person(name << "Mike", age << 24)  
  );  
  cout << typeid(people).name() << endl;  
  hana::for_each(people, [](auto pair) {  
    for(const auto& s : hana::second(pair)) {  
      cout << s << ", ";  
    }  
    cout << endl;  
    });  
}  
```

---

## Comment 5

> Username: ricejasonf  
> Created at: 2018-05-02 22:13:29 UTC  
> Updated at: 2018-05-02 22:14:56 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386137946  

>  I do like the idea of operator= but I might need some macro trick to declare operator= for each of Name, Age,... unless I'm missing something. I'm sticking with operator<< for now.  
  
You would have the `operator=` as a member of a custom type that would be the key. You would have to implement `equal` and `hash` to make it work in a `hana::map`. (like the event type in FullDuplex)  
  
Then your keys would be defined something like: `constexpr auto age = key<Age>{};`  
  
  
> How do I loop over the keys in the person map (which is key -> value_type) without referring to them explicitly and create a new map: key -> vector_type as in the above create_people example?  
  
You would have to make your own `group_by` function. I don't think Hana provides it. It could start out by getting a set containing each unique key. Which could look roughly like:  
```cpp  
fold(make_tuple(person...),  
     make_set(),   
     [](auto state, auto x) { return union_(state, to_set(keys(x))); });  
```  
  
I think  
  
Then you could unpack the keys to make your map:  
```cpp  
unpack(my_keys, [&](auto ...key) {  
    return make_map(make_pair(key, make_vector(key, people)));  
});  
```  
  
This is a bit rough, but I think you'll get the idea. Good luck!

---

## Comment 6

> Username: Char-Aznable  
> Created at: 2018-05-03 01:36:20 UTC  
> Updated at: 2018-05-03 01:36:44 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386170143  

@ricejasonf These are great ideas. It turns out I need to unpack twice in order to make it work:  
```  
template < class Key, class PeopleTuple >  
auto groupBy(Key key, PeopleTuple&& tupleOfPeople) {  
  return hana::unpack(std::forward<PeopleTuple>(tupleOfPeople),   
    [&](auto&...person) {  
      return hana::make_pair(key, typename Key::type::vector_type{person[key]...});  
    });  
}  
  
template < class ... Person >  
auto create_people(Person&&... person) {  
  auto tupleOfPeople = hana::make_tuple(std::forward<Person>(person)...);  
  auto keys = hana::fold(tupleOfPeople, hana::make_set(),   
      [](auto s, auto p) {   
        return hana::union_(s,hana::to_set(hana::keys(p))); });  
  return hana::unpack(keys, [&](auto...key) {  
    return hana::make_map(groupBy(key, tupleOfPeople)...);  
    });  
```  
Can you tell me if hana apply move construction in  
```   
auto people = create_people(  
    create_person(name << "Joe", age << 27),  
    create_person(name << "Mike", age << 24)  
  );  
```  
with the above implementation? i.e., elements in the `vector_type` objects are moved from the respective `person` map

---

## Comment 7

> Username: ricejasonf  
> Created at: 2018-05-03 01:50:51 UTC  
> Updated at: 2018-05-03 01:52:23 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386171876  

All of Boost.Hana's algorithms and data structures are generic and use perfect-forwarding. Since your created map is a temporary it would move construct the values.  
  
Most if not all of that would get optimized out anyway.

---

## Comment 8

> Username: Char-Aznable  
> Created at: 2018-05-03 06:01:27 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386198432  

@ricejasonf On a second thought, I want to replace the 2nd unpack with a  `hana::for_each()` loop which is more intuitive:  
```  
template < class ... Person >  
auto create_people(Person&&... person) {  
  auto tupleOfPeople = hana::make_tuple(std::forward<Person>(person)...);  
  auto keys = hana::fold(tupleOfPeople, hana::make_set(),   
      [](auto s, auto p) {   
        return hana::union_(s,hana::to_set(hana::keys(p))); });  
  auto people = hana::make_map();  
  hana::for_each(keys, [&](auto& key) {   
    hana::insert(people, groupBy(key, tupleOfPeople)); } );  
  return people;  
}  
```  
But the returned map is actually empty. What am I doing wrong here?

---

## Comment 9

> Username: ricejasonf  
> Created at: 2018-05-03 15:09:22 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386329033  

`hana::insert` is a pure function so it returns a new value and does no side affects.  
`hana::for_each` exists only for side-effects and has no return value.  
  
Consider that the return value of `insert` will return a map with a new type containing a new key/value.

---

## Comment 10

> Username: Char-Aznable  
> Created at: 2018-05-03 16:53:32 UTC  
> Updated at: 2018-05-03 16:54:27 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386362576  

@ricejasonf OK. I tried   
```  
...  
  auto people = hana::make_map();  
  hana::for_each(keys, [&](auto& key) {   
    people = hana::insert(people, groupBy(key, tupleOfPeople)); } );  
...  
```  
but this gives compilation error:  
```  
error: no match for ‘operator=’ (operand types are ‘boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >’ and ‘boost::hana::detail::map_impl<boost::hana::detail::hash_table<boost::hana::detail::bucket<Name, 0ul> >, boost::hana::basic_tuple<boost::hana::pair<boost::hana::type_impl<Name>::_, std::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > > >’)  
     people = hana::insert(people, groupBy(key, tupleOfPeople)); } );  
     ~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
I guess this approach of initializing an empty map and then inserting element to it doesn't really work?

---

## Comment 11

> Username: ricejasonf  
> Created at: 2018-05-03 17:03:14 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386365549  

It doesn't work because it has a completely new type. I would suggest using `hana::unpack`.  
  
You'll have to think in terms of pure functional programming because you can not change the type of an object in place.

---

## Comment 12

> Username: Char-Aznable  
> Created at: 2018-05-03 17:19:50 UTC  
> Updated at: 2018-05-03 17:41:09 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-386370614  

@ricejasonf Thanks. That makes sense now.

---

## Comment 13

> Username: ricejasonf  
> Created at: 2018-05-29 22:16:10 UTC  
> Url: https://github.com/boostorg/hana/issues/395#issuecomment-392964840  

This appears to be resolved.
