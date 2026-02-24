# #603 - Composite rules of x3::string("..."), if assigned an action, cannot be used in phrase_parse. [Closed]

> Username: mahlersand  
> Created at: 2020-05-29 22:11:14 UTC  
> Updated at: 2020-05-31 11:48:45 UTC  
> Closed at: 2020-05-31 11:44:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/603  

This is really weird and I cannot explain what's happening here. Possibly I'm stupid and not seeing something obvious, but I don't think that's the case. Here's an example of what I'm trying to do:  
  
```cpp  
    namespace x3 = boost::spirit::x3;  
    auto print_attr_type = [=](auto& ctx) {  
        std::cout << typeid (x3::_attr(ctx)).name() << std::endl;  
    };  
  
    std::string s("\"Text\"");  
    auto parser1 = x3::lexeme['"' >> *(x3::char_('\\') >> x3::char_('"') | x3::char_ - '"') >> '"'][print_attr_type];  
    auto parser2 = x3::lexeme['"' >> *(x3::string("\\\"") | x3::char_ - '"') >> '"'][print_attr_type];  
    x3::phrase_parse(s.begin(),  
                     s.end(),  
                     parser1,  
                     x3::space);  
```  
  
Now, this compiles all fine, but if I change the parser in the parsing call to parser2, I get the weirdest compiler errors. The one where it seems to break down is  
  
```  
/usr/include/boost/spirit/home/x3/support/traits/move_to.hpp:201:24: error: no matching function for call to ‘move_to(std::remove_reference<char&>::type, std::vector<boost::variant<boost::detail::variant::over_sequence<boost::mpl::l_item<mpl_::long_<2>, std::__cxx11::basic_string<char>, boost::mpl::l_item<mpl_::long_<1>, char, boost::mpl::l_end> > > >, std::allocator<boost::variant<boost::detail::variant::over_sequence<boost::mpl::l_item<mpl_::long_<2>, std::__cxx11::basic_string<char>, boost::mpl::l_item<mpl_::long_<1>, char, boost::mpl::l_end> > > > > >&, boost::mpl::identity<boost::spirit::x3::traits::container_attribute>::type)’  
  201 |         detail::move_to(std::move(src), dest  
      |         ~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~  
  202 |           , typename attribute_category<Dest>::type());  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
Assigning an action to `x3::string("\\\"")` alone works absolutely fine, and the definition of rule2 itself also seems to work fine, but using rule2 fails. I would be very happy if someone could help me out here, since rule2 looks nicer in code than rule1 (and should, in theory, result in a std::string attribute instead of a `deque<char,char>` one).

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-05-30 01:06:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/603#issuecomment-636251910  

This is a bug in Spirit, and most likely is the same as #434. A shorter reproducer `(*(*x3::int_ | x3::int_))[([]{})]`. The culprit is:  
```cpp  
using X = boost::variant<std::string, char>;  
static_assert(!boost::spirit::x3::traits::variant_has_substitute<  
    X, std::vector<X>  
>::value, "why?!");  
```  
  
I am afraid I out of motivation to dig deeper, because the `is_substitute` traits it relies on was a reason I have not done/fixed many other things, and removing strange specializations seemed to me like it would break the world.

---

## Comment 2

> Username: Kojoley  
> Created at: 2020-05-30 01:23:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/603#issuecomment-636253880  

For further researchers, start with trying to remove `parse_into_container_impl<alternative<Left, Right>, ...` specialization, it seems completely wrong.

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-05-31 11:48:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/603#issuecomment-636460331  

The issue should be fixed now, thanks for the report.
