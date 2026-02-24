# #27 - warning: 'this' pointer null [-Wnonnull] (enabled with -Wall) [Closed]

> Username: Kojoley  
> Created at: 2020-07-16 18:34:36 UTC  
> Updated at: 2021-10-06 19:02:34 UTC  
> Closed at: 2021-05-14 16:14:20 UTC  
> Url: https://github.com/boostorg/concept_check/issues/27  

GCC trunk has a new diagnostic https://wandbox.org/permlink/5YWcuQ9L3ezmXANI  
```  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::Assignable<const char*> >]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:145:5:   required from 'struct boost::Assignable<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3:   required from 'struct boost_concepts::ReadableIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3:   required from 'struct boost_concepts::ReadableIteratorConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ReadableIteratorConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ReadableIteratorConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:30:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:5: note: in a call to non-static member function 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::Assignable<const char*>]'  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |     ^  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::CopyConstructible<const char*> >]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:167:5:   required from 'struct boost::CopyConstructible<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3:   required from 'struct boost_concepts::ReadableIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3:   required from 'struct boost_concepts::ReadableIteratorConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ReadableIteratorConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ReadableIteratorConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:30:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:5: note: in a call to non-static member function 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::CopyConstructible<const char*>]'  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |     ^  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost_concepts::ReadableIterator<const char*> >]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:48:7:   required from 'struct boost_concepts::ReadableIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3:   required from 'struct boost_concepts::ReadableIteratorConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ReadableIteratorConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ReadableIteratorConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:30:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:5: note: in a call to non-static member function 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost_concepts::ReadableIterator<const char*>]'  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |     ^  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost_concepts::ReadableIteratorConcept<const char*>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:30:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/mpl/aux_/integral_wrapper.hpp:22,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/mpl/int.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/mpl/lambda_fwd.hpp:23,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/mpl/aux_/na_spec.hpp:18,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/mpl/identity.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/context.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parser.hpp:18,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:17: note: in a call to non-static member function 'boost_concepts::ReadableIteratorConcept<const char*>::~ReadableIteratorConcept()'  
   40 |   BOOST_concept(ReadableIterator,(Iterator))  
      |                 ^~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/preprocessor/cat.hpp:29:34: note: in definition of macro 'BOOST_PP_CAT_I'  
   29 | #    define BOOST_PP_CAT_I(a, b) a ## b  
      |                                  ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/concept_def.hpp:23:12: note: in expansion of macro 'BOOST_PP_CAT'  
   23 |     struct BOOST_PP_CAT(name,Concept)                                           \  
      |            ^~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3: note: in expansion of macro 'BOOST_concept'  
   40 |   BOOST_concept(ReadableIterator,(Iterator))  
      |   ^~~~~~~~~~~~~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag> >]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5:   required from 'struct boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:114:7:   [ skipping 5 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:5: note: in a call to non-static member function 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag>]'  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |     ^  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:114:7:   required from 'struct boost_concepts::IncrementableIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:129:3:   required from 'struct boost_concepts::SinglePassIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversal<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversalConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ForwardTraversalConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost::Convertible<X, Y>::~Convertible() [with X = boost::iterators::random_access_traversal_tag; Y = boost::iterators::incrementable_traversal_tag]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  208 |     BOOST_CONCEPT_USAGE(Convertible) {  
      |     ^~~~~~~~~~~~~~~~~~~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost_concepts::IncrementableIterator<const char*> >]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:120:7:   required from 'struct boost_concepts::IncrementableIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:129:3:   required from 'struct boost_concepts::SinglePassIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversal<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversalConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ForwardTraversalConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:5: note: in a call to non-static member function 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost_concepts::IncrementableIterator<const char*>]'  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |     ^  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::EqualityComparable<const char*> >]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:233:5:   required from 'struct boost::EqualityComparable<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:129:3:   required from 'struct boost_concepts::SinglePassIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversal<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversalConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ForwardTraversalConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:5: note: in a call to non-static member function 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::EqualityComparable<const char*>]'  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |     ^  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag> >]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5:   required from 'struct boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:134:7:   [ skipping 4 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:5: note: in a call to non-static member function 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag>]'  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |     ^  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:134:7:   required from 'struct boost_concepts::SinglePassIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversal<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversalConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ForwardTraversalConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost::Convertible<X, Y>::~Convertible() [with X = boost::iterators::random_access_traversal_tag; Y = boost::iterators::single_pass_traversal_tag]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  208 |     BOOST_CONCEPT_USAGE(Convertible) {  
      |     ^~~~~~~~~~~~~~~~~~~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::DefaultConstructible<const char*> >]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:137:5:   required from 'struct boost::DefaultConstructible<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversal<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversalConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ForwardTraversalConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:5: note: in a call to non-static member function 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::DefaultConstructible<const char*>]'  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |     ^  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag> >]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5:   required from 'struct boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:150:7:   [ skipping 3 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:5: note: in a call to non-static member function 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag>]'  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |     ^  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:150:7:   required from 'struct boost_concepts::ForwardTraversal<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversalConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ForwardTraversalConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost::Convertible<X, Y>::~Convertible() [with X = boost::iterators::random_access_traversal_tag; Y = boost::iterators::forward_traversal_tag]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  208 |     BOOST_CONCEPT_USAGE(Convertible) {  
      |     ^~~~~~~~~~~~~~~~~~~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/assert.hpp:35,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp: In instantiation of 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost_concepts::ForwardTraversalConcept<const char*>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47: warning: 'this' pointer null [-Wnonnull]  
   39 |     static void failed() { ((Model*)0)->~Model(); }  
      |                            ~~~~~~~~~~~~~~~~~~~^~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/mpl/aux_/integral_wrapper.hpp:22,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/mpl/int.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/mpl/lambda_fwd.hpp:23,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/mpl/aux_/na_spec.hpp:18,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/mpl/identity.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/context.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parser.hpp:18,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:17: note: in a call to non-static member function 'boost_concepts::ForwardTraversalConcept<const char*>::~ForwardTraversalConcept()'  
  141 |   BOOST_concept(ForwardTraversal,(Iterator))  
      |                 ^~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/preprocessor/cat.hpp:29:34: note: in definition of macro 'BOOST_PP_CAT_I'  
   29 | #    define BOOST_PP_CAT_I(a, b) a ## b  
      |                                  ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/concept_def.hpp:23:12: note: in expansion of macro 'BOOST_PP_CAT'  
   23 |     struct BOOST_PP_CAT(name,Concept)                                           \  
      |            ^~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3: note: in expansion of macro 'BOOST_concept'  
  141 |   BOOST_concept(ForwardTraversal,(Iterator))  
      |   ^~~~~~~~~~~~~  
In file included from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:31,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/concepts.hpp:19,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size_type.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/size.hpp:21,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/functions.hpp:20,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range_core.hpp:38,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/range/iterator_range.hpp:13,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/is_range.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/attribute_category.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/support/traits/move_to.hpp:12,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary/any_parser.hpp:17,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/auxiliary.hpp:11,  
                 from /opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3.hpp:14,  
                 from prog.cc:2:  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp: In instantiation of 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::Assignable<const char*>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47:   required from 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::Assignable<const char*> >]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:145:5:   required from 'struct boost::Assignable<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3:   required from 'struct boost_concepts::ReadableIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3:   required from 'struct boost_concepts::ReadableIteratorConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ReadableIteratorConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ReadableIteratorConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:30:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:48: warning: 'this' pointer null [-Wnonnull]  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |                             ~~~~~~~~~~~~~~~~~~~^~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost::Assignable<TT>::~Assignable() [with TT = const char*]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:145:5: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  145 |     BOOST_CONCEPT_USAGE(Assignable) {  
      |     ^~~~~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp: In instantiation of 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::CopyConstructible<const char*>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47:   required from 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::CopyConstructible<const char*> >]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:167:5:   required from 'struct boost::CopyConstructible<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3:   required from 'struct boost_concepts::ReadableIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3:   required from 'struct boost_concepts::ReadableIteratorConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ReadableIteratorConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ReadableIteratorConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:30:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:48: warning: 'this' pointer null [-Wnonnull]  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |                             ~~~~~~~~~~~~~~~~~~~^~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost::CopyConstructible<TT>::~CopyConstructible() [with TT = const char*]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:167:5: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  167 |     BOOST_CONCEPT_USAGE(CopyConstructible) {  
      |     ^~~~~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp: In instantiation of 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost_concepts::ReadableIterator<const char*>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47:   required from 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost_concepts::ReadableIterator<const char*> >]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:48:7:   required from 'struct boost_concepts::ReadableIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:40:3:   required from 'struct boost_concepts::ReadableIteratorConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ReadableIteratorConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ReadableIteratorConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ReadableIteratorConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:30:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:48: warning: 'this' pointer null [-Wnonnull]  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |                             ~~~~~~~~~~~~~~~~~~~^~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost_concepts::ReadableIterator<Iterator>::~ReadableIterator() [with Iterator = const char*]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:48:7: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
   48 |       BOOST_CONCEPT_USAGE(ReadableIterator)  
      |       ^~~~~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp: In instantiation of 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47:   required from 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag> >]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5:   required from 'struct boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::incrementable_traversal_tag> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   [ skipping 6 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:48: warning: 'this' pointer null [-Wnonnull]  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |                             ~~~~~~~~~~~~~~~~~~~^~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost::Convertible<X, Y>::~Convertible() [with X = boost::iterators::random_access_traversal_tag; Y = boost::iterators::incrementable_traversal_tag]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  208 |     BOOST_CONCEPT_USAGE(Convertible) {  
      |     ^~~~~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp: In instantiation of 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost_concepts::IncrementableIterator<const char*>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47:   required from 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost_concepts::IncrementableIterator<const char*> >]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:120:7:   required from 'struct boost_concepts::IncrementableIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:129:3:   required from 'struct boost_concepts::SinglePassIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversal<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   [ skipping 2 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:48: warning: 'this' pointer null [-Wnonnull]  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |                             ~~~~~~~~~~~~~~~~~~~^~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost_concepts::IncrementableIterator<Iterator>::~IncrementableIterator() [with Iterator = const char*]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:120:7: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  120 |       BOOST_CONCEPT_USAGE(IncrementableIterator)  
      |       ^~~~~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp: In instantiation of 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::EqualityComparable<const char*>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47:   required from 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::EqualityComparable<const char*> >]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:233:5:   required from 'struct boost::EqualityComparable<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:129:3:   required from 'struct boost_concepts::SinglePassIterator<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversal<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   [ skipping 2 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:48: warning: 'this' pointer null [-Wnonnull]  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |                             ~~~~~~~~~~~~~~~~~~~^~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost::EqualityComparable<TT>::~EqualityComparable() [with TT = const char*]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:233:5: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  233 |     BOOST_CONCEPT_USAGE(EqualityComparable) {  
      |     ^~~~~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp: In instantiation of 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47:   required from 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag> >]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5:   required from 'struct boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::single_pass_traversal_tag> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   [ skipping 5 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:48: warning: 'this' pointer null [-Wnonnull]  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |                             ~~~~~~~~~~~~~~~~~~~^~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost::Convertible<X, Y>::~Convertible() [with X = boost::iterators::random_access_traversal_tag; Y = boost::iterators::single_pass_traversal_tag]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  208 |     BOOST_CONCEPT_USAGE(Convertible) {  
      |     ^~~~~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp: In instantiation of 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::DefaultConstructible<const char*>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47:   required from 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::DefaultConstructible<const char*> >]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:137:5:   required from 'struct boost::DefaultConstructible<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversal<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/iterator/iterator_concepts.hpp:141:3:   required from 'struct boost_concepts::ForwardTraversalConcept<const char*>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost_concepts::ForwardTraversalConcept<const char*>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:48: warning: 'this' pointer null [-Wnonnull]  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |                             ~~~~~~~~~~~~~~~~~~~^~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost::DefaultConstructible<TT>::~DefaultConstructible() [with TT = const char*]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:137:5: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  137 |     BOOST_CONCEPT_USAGE(DefaultConstructible) {  
      |     ^~~~~~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp: In instantiation of 'boost::concepts::usage_requirements<Model>::~usage_requirements() [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag>]':  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:39:47:   required from 'static void boost::concepts::requirement<boost::concepts::failed************ Model::************>::failed() [with Model = boost::concepts::usage_requirements<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag> >]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5:   required from 'struct boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of 'template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model::constraints)>*) [with Model = boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag>]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost::Convertible<boost::iterators::random_access_traversal_tag, boost::iterators::forward_traversal_tag> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   [ skipping 4 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:42:5:   required from 'const bool boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >::value'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/has_constraints.hpp:45:51:   required from 'struct boost::concepts::not_satisfied<boost_concepts::ForwardTraversalConcept<const char*> >'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/detail/general.hpp:51:8:   required from 'struct boost::concepts::requirement_<void (*)(boost_concepts::ForwardTraversalConcept<const char*>)>'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:31:9:   required from 'bool boost::spirit::x3::parse_main(Iterator&, Iterator, const Parser&, Attribute&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser; Attribute = const boost::spirit::x3::unused_type]'  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/spirit/home/x3/core/parse.hpp:72:26:   required from 'bool boost::spirit::x3::parse(Iterator&, Iterator, const Parser&) [with Iterator = const char*; Parser = boost::spirit::x3::eps_parser]'  
prog.cc:7:39:   required from here  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:16:48: warning: 'this' pointer null [-Wnonnull]  
   16 |     ~usage_requirements() { ((Model*)0)->~Model(); }  
      |                             ~~~~~~~~~~~~~~~~~~~^~  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept/usage.hpp:30:7: note: in a call to non-static member function 'boost::Convertible<X, Y>::~Convertible() [with X = boost::iterators::random_access_traversal_tag; Y = boost::iterators::forward_traversal_tag]'  
   30 |       ~model()  
      |       ^  
/opt/wandbox/boost-1.73.0/gcc-head/include/boost/concept_check.hpp:208:5: note: in expansion of macro 'BOOST_CONCEPT_USAGE'  
  208 |     BOOST_CONCEPT_USAGE(Convertible) {  
      |     ^~~~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created at: 2020-07-24 14:02:59 UTC  
> Url: https://github.com/boostorg/concept_check/issues/27#issuecomment-663554328  

Hi,  
  
While this new gcc does has currently some annoying false positives, this case here is actually reporting a real case. I guess it is on purpose that we have some code derefenrencing zero, so that it core dumps and effectively show requirement violation ?  
  
Shall we add some #pragma gcc diagnostic ignore Wnonnull in these files ?  
  
Cheers,  
Romain

---

## Comment 2

> Username: Kojoley  
> Created at: 2020-07-24 15:37:57 UTC  
> Url: https://github.com/boostorg/concept_check/issues/27#issuecomment-663599898  

> I guess it is on purpose that we have some code derefenrencing zero, so that it core dumps and effectively show requirement violation ?  
  
It is cheap and dirty `declval<Model*>()`.

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created at: 2020-07-24 15:44:25 UTC  
> Url: https://github.com/boostorg/concept_check/issues/27#issuecomment-663602856  

Yeah but the the goal is to terminate the process by segfaulting explicitly ? Like calling std::terminate or abort ? What's the purpose of doing that exactly ?  
  
Anyway, I did try to use some pragma diagnostics, warning is now ignored, but not the note detailing the call stack. See https://gcc.gnu.org/bugzilla/show_bug.cgi?id=96310

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created at: 2021-05-14 14:55:03 UTC  
> Url: https://github.com/boostorg/concept_check/issues/27#issuecomment-841294928  

Issue is now fixed as #PR 28 was merged. I guess it can be closed.

---

## Comment 5

> Username: jwakely  
> Created at: 2021-10-06 19:01:33 UTC  
> Updated at: 2021-10-06 19:02:34 UTC  
> Url: https://github.com/boostorg/concept_check/issues/27#issuecomment-936916221  

> Yeah but the the goal is to terminate the process by segfaulting explicitly ? Like calling std::terminate or abort ? What's the purpose of doing that exactly ?  
  
It's never executed at runtime, it's only trying to instantiate the template entity, which will check that the template argument satisfies the operations required by the "concept".
