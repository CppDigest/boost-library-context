# #110 - #98 breaks assignment in Boost.Fusion [Open]

> Username: Jannik2099  
> Created at: 2024-03-06 19:38:59 UTC  
> Updated at: 2024-04-17 12:13:07 UTC  
> Url: https://github.com/boostorg/variant/issues/110  

The fix in #98 breaks usage in `boost::fusion::deque`.  
Commenting out the `#if[...]#endif` around the operator definition make the code below compile again.  
  
Sadly I encountered this when using Boost.Spirit, so the cvise-reduced case is still quite icky.  
Unsure if this is a bug in Boost.Variant, or in how Boost.Fusion uses it.  
  
Godbolt: https://godbolt.org/z/s8EfxT8r4  
  
```  
In file included from <source>:2:  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/spirit/home/x3.hpp:62:  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/spirit/home/x3/auxiliary.hpp:11:  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/spirit/home/x3/auxiliary/any_parser.hpp:17:  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/spirit/home/x3/support/traits/move_to.hpp:12:  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/spirit/home/x3/support/traits/attribute_category.hpp:16:  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/spirit/home/x3/support/traits/is_variant.hpp:11:  
In file included from /opt/compiler-explorer/libs/boost_1_84_0/boost/variant.hpp:17:  
/opt/compiler-explorer/libs/boost_1_84_0/boost/variant/variant.hpp:620:13: error: object of type 'boost::fusion::deque<boost::optional<Slot>, bool>' cannot be assigned because its copy assignment operator is implicitly deleted  
  620 |         lhs = rhs_;  
      |             ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/variant/variant.hpp:1018:16: note: in instantiation of member function 'boost::detail::variant::direct_assigner<boost::fusion::deque<boost::optional<Slot>, bool>>::operator()' requested here  
 1018 |         return visitor_(operand);  
      |                ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/variant/detail/visitation_impl.hpp:112:20: note: in instantiation of function template specialization 'boost::detail::variant::invoke_visitor<boost::detail::variant::direct_assigner<boost::fusion::deque<boost::optional<Slot>, bool>>, false>::internal_visit<boost::fusion::deque<boost::optional<Slot>, bool> &>' requested here  
  112 |     return visitor.internal_visit(  
      |                    ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/variant/detail/visitation_impl.hpp:152:13: note: in instantiation of function template specialization 'boost::detail::variant::visitation_impl_invoke_impl<boost::detail::variant::invoke_visitor<boost::detail::variant::direct_assigner<boost::fusion::deque<boost::optional<Slot>, bool>>, false>, void *, boost::fusion::deque<boost::optional<Slot>, bool>>' requested here  
  152 |     return (visitation_impl_invoke_impl)(  
      |             ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/variant/detail/visitation_impl.hpp:235:11: note: in instantiation of function template specialization 'boost::detail::variant::visitation_impl_invoke<boost::detail::variant::invoke_visitor<boost::detail::variant::direct_assigner<boost::fusion::deque<boost::optional<Slot>, bool>>, false>, void *, boost::fusion::deque<boost::optional<Slot>, bool>, boost::variant<char, boost::fusion::deque<boost::optional<Slot>, bool>>::has_fallback_type_>' requested here  
  235 |         , BOOST_VARIANT_AUX_APPLY_VISITOR_STEP_CASE  
      |           ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/preprocessor/repetition/repeat.hpp:30:26: note: expanded from macro 'BOOST_PP_REPEAT'  
   30 | # define BOOST_PP_REPEAT BOOST_PP_CAT(BOOST_PP_REPEAT_, BOOST_PP_AUTO_REC(BOOST_PP_REPEAT_P, 4))  
      |                          ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
   22 | #    define BOOST_PP_CAT(a, b) BOOST_PP_CAT_I(a, b)  
      |                                ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
   29 | #    define BOOST_PP_CAT_I(a, b) a ## b  
      |                                  ^  
<scratch space>:148:1: note: expanded from here  
  148 | BOOST_PP_REPEAT_1  
      | ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/variant/variant.hpp:2282:33: note: in instantiation of function template specialization 'boost::detail::variant::visitation_impl<mpl_::int_<0>, boost::detail::variant::visitation_impl_step<boost::mpl::l_iter<boost::mpl::l_item<mpl_::long_<2>, char, boost::mpl::l_item<mpl_::long_<1>, boost::fusion::deque<boost::optional<Slot>, bool>, boost::mpl::l_end>>>, boost::mpl::l_iter<l_end>>, boost::detail::variant::invoke_visitor<boost::detail::variant::direct_assigner<boost::fusion::deque<boost::optional<Slot>, bool>>, false>, void *, boost::variant<char, boost::fusion::deque<boost::optional<Slot>, bool>>::has_fallback_type_>' requested here  
 2282 |         return detail::variant::visitation_impl(  
      |                                 ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/variant/variant.hpp:2294:16: note: (skipping 12 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
 2294 |         return internal_apply_visitor_impl(  
      |                ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/spirit/home/x3/nonterminal/detail/rule.hpp:209:26: note: in instantiation of function template specialization 'boost::spirit::x3::action<boost::spirit::x3::alternative<boost::spirit::x3::any_char<boost::spirit::char_encoding::standard>, boost::spirit::x3::sequence<boost::spirit::x3::optional<boost::spirit::x3::rule<slot_struc, Slot>>, boost::spirit::x3::matches_directive<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>>, (lambda at <source>:9:25)>::parse<__gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::context<boost::spirit::x3::rule<atomSlotExpr>, const boost::spirit::x3::action<boost::spirit::x3::alternative<boost::spirit::x3::any_char<boost::spirit::char_encoding::standard>, boost::spirit::x3::sequence<boost::spirit::x3::optional<boost::spirit::x3::rule<slot_struc, Slot>>, boost::spirit::x3::matches_directive<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>>, (lambda at <source>:9:25)>>, boost::spirit::x3::unused_type>' requested here  
  209 |             bool r = rhs.parse(  
      |                          ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/spirit/home/x3/nonterminal/detail/rule.hpp:265:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::spirit::x3::rule<atomSlotExpr>, boost::spirit::x3::rule<atomSlotExpr>>::parse_rhs_main<boost::spirit::x3::action<boost::spirit::x3::alternative<boost::spirit::x3::any_char<boost::spirit::char_encoding::standard>, boost::spirit::x3::sequence<boost::spirit::x3::optional<boost::spirit::x3::rule<slot_struc, Slot>>, boost::spirit::x3::matches_directive<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>>, (lambda at <source>:9:25)>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::unused_type, boost::spirit::x3::unused_type, const boost::spirit::x3::unused_type>' requested here  
  265 |             return parse_rhs_main(  
      |                    ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/spirit/home/x3/nonterminal/detail/rule.hpp:290:20: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::spirit::x3::rule<atomSlotExpr>, boost::spirit::x3::rule<atomSlotExpr>>::parse_rhs_main<boost::spirit::x3::action<boost::spirit::x3::alternative<boost::spirit::x3::any_char<boost::spirit::char_encoding::standard>, boost::spirit::x3::sequence<boost::spirit::x3::optional<boost::spirit::x3::rule<slot_struc, Slot>>, boost::spirit::x3::matches_directive<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>>, (lambda at <source>:9:25)>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::unused_type, boost::spirit::x3::unused_type, const boost::spirit::x3::unused_type>' requested here  
  290 |             return parse_rhs_main(rhs, first, last, context, rcontext, unused);  
      |                    ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/spirit/home/x3/nonterminal/detail/rule.hpp:328:28: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::spirit::x3::rule<atomSlotExpr>, boost::spirit::x3::rule<atomSlotExpr>>::parse_rhs<boost::spirit::x3::action<boost::spirit::x3::alternative<boost::spirit::x3::any_char<boost::spirit::char_encoding::standard>, boost::spirit::x3::sequence<boost::spirit::x3::optional<boost::spirit::x3::rule<slot_struc, Slot>>, boost::spirit::x3::matches_directive<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>>, (lambda at <source>:9:25)>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::unused_type, boost::spirit::x3::unused_type, boost::spirit::x3::unused_type>' requested here  
  328 |                 ok_parse = parse_rhs(rhs, first, last, context, attr_, attr_  
      |                            ^  
<source>:19:45: note: in instantiation of function template specialization 'boost::spirit::x3::detail::rule_parser<boost::spirit::x3::rule<atomSlotExpr>, boost::spirit::x3::rule<atomSlotExpr>>::call_rule_definition<boost::spirit::x3::action<boost::spirit::x3::alternative<boost::spirit::x3::any_char<boost::spirit::char_encoding::standard>, boost::spirit::x3::sequence<boost::spirit::x3::optional<boost::spirit::x3::rule<slot_struc, Slot>>, boost::spirit::x3::matches_directive<boost::spirit::x3::literal_string<const char *, boost::spirit::char_encoding::standard, boost::spirit::x3::unused_type>>>>, (lambda at <source>:9:25)>, __gnu_cxx::__normal_iterator<char *, std::basic_string<char>>, boost::spirit::x3::unused_type, boost::spirit::x3::unused_type, mpl_::bool_<false>>' requested here  
   19 |   x3::detail ::rule_parser<rule_t, rule_t>::call_rule_definition(  
      |                                             ^  
/opt/compiler-explorer/libs/boost_1_84_0/boost/fusion/container/deque/deque.hpp:121:9: note: copy assignment operator is implicitly deleted because 'deque<boost::optional<Slot>, bool>' has a user-declared move constructor  
  121 |         deque(deque&& seq)  
      |         ^  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-16 00:04:47 UTC  
> Url: https://github.com/boostorg/variant/issues/110#issuecomment-2058010046  

@ecatmur

---

## Comment 2

> Username: Jannik2099  
> Created at: 2024-04-17 11:06:45 UTC  
> Url: https://github.com/boostorg/variant/issues/110#issuecomment-2061004267  

Aiui the original issue is due to https://github.com/llvm/llvm-project/issues/59966 which won't be fixed anytime soon.  
  
I guess the simplest solution is to just define the copy operators aswell - however, I wonder if that could be an API change in itself? For example, do you know if boost or any other big consumer has stuff like `has_defaulted_operator<T>` ?  
  
Considering that you got no such complaint when the move ctor got added, I would guess no.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-04-17 12:13:07 UTC  
> Url: https://github.com/boostorg/variant/issues/110#issuecomment-2061118242  

> I guess the simplest solution is to just define the copy operators aswell  
  
Not sure what you mean here; do you mean to `#ifdef` out `operator=(T const&)` as well, in addition to `operator=(T&&)`?  
  
If so, you could do a PR with this change and we'll see what CI says about it.
