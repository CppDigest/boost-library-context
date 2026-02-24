# #562 - List of non BOOST-prefixed macros [Open]

> Username: ned14  
> Created at: 2019-12-16 13:53:44 UTC  
> Updated at: 2025-05-10 01:16:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/562  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines. I have logged the Phoenix problems separately at https://github.com/boostorg/phoenix/issues/90.  
  
```  
./libs/spirit/classic/doc/closures.html:        </span><span class="preprocessor style1">#define</span><span class="style1"> PHOENIX_LIMIT 10<br>  
./libs/spirit/classic/doc/select_parser.html:      </span><span class="style3">#define</span> PHOENIX_LIMIT 10<br>  
./libs/spirit/classic/example/intermediate/simple_xml/xml_g.hpp:#ifndef PHOENIX_LIMIT  
./libs/spirit/classic/example/intermediate/simple_xml/xml_g.hpp:#define PHOENIX_LIMIT   10  
./libs/spirit/classic/example/intermediate/simple_xml/xml_g.hpp:#if PHOENIX_LIMIT < BOOST_SPIRIT_CLOSURE_LIMIT  
./libs/spirit/classic/example/intermediate/simple_xml/xml_g.hpp:#undef PHOENIX_LIMIT  
./libs/spirit/classic/example/intermediate/simple_xml/xml_g.hpp:#define PHOENIX_LIMIT BOOST_SPIRIT_CLOSURE_LIMIT  
./libs/spirit/classic/phoenix/doc/lazy_construction.html:<img src="theme/bulb.gif"></img> The ultimate maximum number of actual parameters is limited by the preprocessor constant PHOENIX_CONSTRUCT_LIMIT. Note though, that this limit should not be greater than PHOENIX_LIMIT.    </td>  
./boost/spirit/home/classic/attribute.hpp:#define PHOENIX_LIMIT 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#ifndef PHOENIX_COMPOSITE_HPP  
./boost/spirit/home/classic/phoenix/composite.hpp:#define PHOENIX_COMPOSITE_HPP  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/composite.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/primitives.hpp:#ifndef PHOENIX_PRIMITIVES_HPP  
./boost/spirit/home/classic/phoenix/primitives.hpp:#define PHOENIX_PRIMITIVES_HPP  
./boost/spirit/home/classic/phoenix/primitives.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/primitives.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/primitives.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/primitives.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/operators.hpp:#ifndef PHOENIX_OPERATORS_HPP  
./boost/spirit/home/classic/phoenix/operators.hpp:#define PHOENIX_OPERATORS_HPP  
./boost/spirit/home/classic/phoenix/actor.hpp:#ifndef PHOENIX_ACTOR_HPP  
./boost/spirit/home/classic/phoenix/actor.hpp:#define PHOENIX_ACTOR_HPP  
./boost/spirit/home/classic/phoenix/actor.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/actor.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/actor.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/actor.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/actor.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/actor.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/actor.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/actor.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#ifndef PHOENIX_TUPLES_HPP  
./boost/spirit/home/classic/phoenix/tuples.hpp:#define PHOENIX_TUPLES_HPP  
./boost/spirit/home/classic/phoenix/tuples.hpp:#ifndef PHOENIX_LIMIT  
./boost/spirit/home/classic/phoenix/tuples.hpp:#define PHOENIX_LIMIT 3  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuples.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/closures.hpp:#ifdef PHOENIX_THREADSAFE  
./boost/spirit/home/classic/phoenix/closures.hpp:#ifndef PHOENIX_THREADSAFE  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/closures.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/closures.hpp:#ifdef PHOENIX_THREADSAFE  
./boost/spirit/home/classic/phoenix/closures.hpp:#ifdef PHOENIX_THREADSAFE  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#ifndef PHOENIX_TUPLEHELPERS_HPP  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#define PHOENIX_TUPLEHELPERS_HPP  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:    #if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:    #if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:    #if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:    #if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#endif // PHOENIX_TUPLEHELPERS_HPP  
./boost/spirit/home/classic/phoenix/functions.hpp:#ifndef PHOENIX_FUNCTIONS_HPP  
./boost/spirit/home/classic/phoenix/functions.hpp:#define PHOENIX_FUNCTIONS_HPP  
./boost/spirit/home/classic/phoenix/functions.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/functions.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/functions.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/functions.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/functions.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/functions.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/functions.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/functions.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/casts.hpp:#ifndef PHOENIX_CASTS_HPP  
./boost/spirit/home/classic/phoenix/casts.hpp:#define PHOENIX_CASTS_HPP  
./boost/spirit/home/classic/phoenix/casts.hpp://  PHOENIX_CONSTRUCT_LIMIT should NOT be greater than PHOENIX_LIMIT!  
./boost/spirit/home/classic/phoenix/casts.hpp:#define PHOENIX_CONSTRUCT_LIMIT PHOENIX_LIMIT  
./boost/spirit/home/classic/phoenix/casts.hpp:// ensure PHOENIX_CONSTRUCT_LIMIT <= PHOENIX_LIMIT  
./boost/spirit/home/classic/phoenix/casts.hpp:BOOST_STATIC_ASSERT(PHOENIX_CONSTRUCT_LIMIT <= PHOENIX_LIMIT);  
./boost/spirit/home/classic/phoenix/casts.hpp:// ensure PHOENIX_CONSTRUCT_LIMIT <= 15  
./boost/spirit/home/classic/phoenix/casts.hpp://      preprocessor constant PHOENIX_CONSTRUCT_LIMIT. Note though, that this  
./boost/spirit/home/classic/phoenix/casts.hpp://      limit should not be greater than PHOENIX_LIMIT.  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 3  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 6  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 9  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 12  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 3  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 6  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 9  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 12  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 3  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 6  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 9  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 12  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 3  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 6  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 9  
./boost/spirit/home/classic/phoenix/casts.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 12  
./boost/spirit/home/classic/phoenix/casts.hpp:#endif // PHOENIX_CASTS_HPP  
./boost/spirit/home/classic/phoenix/statements.hpp:#ifndef PHOENIX_STATEMENTS_HPP  
./boost/spirit/home/classic/phoenix/statements.hpp:#define PHOENIX_STATEMENTS_HPP  
./boost/spirit/home/classic/phoenix/new.hpp:#ifndef PHOENIX_NEW_HPP  
./boost/spirit/home/classic/phoenix/new.hpp:#define PHOENIX_NEW_HPP  
./boost/spirit/home/classic/phoenix/new.hpp://  PHOENIX_CONSTRUCT_LIMIT should NOT be greater than PHOENIX_LIMIT!  
./boost/spirit/home/classic/phoenix/new.hpp:#define PHOENIX_CONSTRUCT_LIMIT PHOENIX_LIMIT  
./boost/spirit/home/classic/phoenix/new.hpp:// ensure PHOENIX_CONSTRUCT_LIMIT <= PHOENIX_LIMIT  
./boost/spirit/home/classic/phoenix/new.hpp:BOOST_STATIC_ASSERT(PHOENIX_CONSTRUCT_LIMIT <= PHOENIX_LIMIT);  
./boost/spirit/home/classic/phoenix/new.hpp:// ensure PHOENIX_CONSTRUCT_LIMIT <= 15  
./boost/spirit/home/classic/phoenix/new.hpp://      preprocessor constant PHOENIX_CONSTRUCT_LIMIT. Note though, that this  
./boost/spirit/home/classic/phoenix/new.hpp://      limit should not be greater than PHOENIX_LIMIT.  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 3  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 6  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 9  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 12  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 3  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 6  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 9  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 12  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 3  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 6  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 9  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 12  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 3  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 6  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 9  
./boost/spirit/home/classic/phoenix/new.hpp:#if PHOENIX_CONSTRUCT_LIMIT > 12  
./boost/spirit/home/classic/phoenix/new.hpp:#endif // PHOENIX_NEW_HPP  
./boost/spirit/home/classic/phoenix/binders.hpp:#ifndef PHOENIX_BINDERS_HPP  
./boost/spirit/home/classic/phoenix/binders.hpp:#define PHOENIX_BINDERS_HPP  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 3  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 6  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 9  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/binders.hpp:#if PHOENIX_LIMIT > 12  
./boost/spirit/home/classic/phoenix/special_ops.hpp:#ifndef PHOENIX_SPECIAL_OPS_HPP  
./boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_SPECIAL_OPS_HPP  
./boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_SSTREAM strstream  
./boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_SSTREAM stringstream  
./boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_STD _STLP_STD  
./boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_NO_STD_NAMESPACE  
./boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_STD std  
./boost/spirit/home/classic/phoenix/special_ops.hpp:struct binary_operator<shift_r_op, PHOENIX_STD::istream, T1>  
./boost/spirit/home/classic/phoenix/special_ops.hpp:    typedef PHOENIX_STD::istream& result_type;  
./boost/spirit/home/classic/phoenix/special_ops.hpp:struct binary_operator<shift_l_op, PHOENIX_STD::ostream, T1>  
./boost/spirit/home/classic/phoenix/special_ops.hpp:    typedef PHOENIX_STD::ostream& result_type;  
./boost/spirit/home/classic/phoenix/special_ops.hpp:struct binary_operator<shift_r_op, PHOENIX_STD::PHOENIX_SSTREAM, T1>  
./boost/spirit/home/classic/phoenix/special_ops.hpp:    typedef PHOENIX_STD::istream& result_type;  
./boost/spirit/home/classic/phoenix/special_ops.hpp:struct binary_operator<shift_l_op, PHOENIX_STD::PHOENIX_SSTREAM, T1>  
./boost/spirit/home/classic/phoenix/special_ops.hpp:    typedef PHOENIX_STD::ostream& result_type;  
./boost/spirit/home/classic/phoenix/special_ops.hpp:typedef PHOENIX_STD::ios_base&  (*iomanip_t)(PHOENIX_STD::ios_base&);  
./boost/spirit/home/classic/phoenix/special_ops.hpp:typedef PHOENIX_STD::istream&   (*imanip_t)(PHOENIX_STD::istream&);  
./boost/spirit/home/classic/phoenix/special_ops.hpp:typedef PHOENIX_STD::ostream&   (*omanip_t)(PHOENIX_STD::ostream&);  
./boost/spirit/home/classic/phoenix/special_ops.hpp:#undef PHOENIX_SSTREAM  
./boost/spirit/home/classic/phoenix/special_ops.hpp:#undef PHOENIX_STD  
./boost/spirit/home/classic/dynamic/select.hpp://  It should NOT be greater than PHOENIX_LIMIT!  
./boost/spirit/home/classic/dynamic/select.hpp:#define BOOST_SPIRIT_SELECT_LIMIT PHOENIX_LIMIT  
./boost/spirit/home/classic/dynamic/select.hpp:// ensure   BOOST_SPIRIT_SELECT_LIMIT <= PHOENIX_LIMIT and  
./boost/spirit/home/classic/dynamic/select.hpp:BOOST_STATIC_ASSERT(BOOST_SPIRIT_SELECT_LIMIT <= PHOENIX_LIMIT);  
./boost/spirit/home/classic/dynamic/select.hpp:    //  limit defined by the PHOENIX_LIMIT pp constant.  
./boost/spirit/home/classic/attribute/closure_fwd.hpp:#   define BOOST_SPIRIT_CLOSURE_LIMIT PHOENIX_LIMIT  
./boost/spirit/home/classic/attribute/closure.hpp://  It should NOT be greater than PHOENIX_LIMIT!  
./boost/spirit/home/classic/attribute/closure.hpp:#define BOOST_SPIRIT_CLOSURE_LIMIT PHOENIX_LIMIT  
./boost/spirit/home/classic/attribute/closure.hpp:// ensure BOOST_SPIRIT_CLOSURE_LIMIT <= PHOENIX_LIMIT and SPIRIT_CLOSURE_LIMIT <= 15  
./boost/spirit/home/classic/attribute/closure.hpp:BOOST_STATIC_ASSERT(BOOST_SPIRIT_CLOSURE_LIMIT <= PHOENIX_LIMIT);  
./boost/spirit/home/classic/utility/grammar_def.hpp://  It should NOT be greater than PHOENIX_LIMIT!  
./boost/spirit/home/classic/utility/grammar_def.hpp:#define BOOST_SPIRIT_GRAMMAR_STARTRULE_TYPE_LIMIT PHOENIX_LIMIT  
./boost/spirit/home/classic/utility/grammar_def.hpp:// ensure BOOST_SPIRIT_GRAMMAR_STARTRULE_TYPE_LIMIT <= PHOENIX_LIMIT and  
./boost/spirit/home/classic/utility/grammar_def.hpp:BOOST_STATIC_ASSERT(BOOST_SPIRIT_GRAMMAR_STARTRULE_TYPE_LIMIT <= PHOENIX_LIMIT);  
./boost/spirit/home/classic/utility/grammar_def_fwd.hpp:#define BOOST_SPIRIT_GRAMMAR_STARTRULE_TYPE_LIMIT PHOENIX_LIMIT  
./boost/spirit/home/classic/tree/parse_tree_utils.hpp:#if !defined(PARSE_TREE_UTILS_HPP)  
./boost/spirit/home/classic/tree/parse_tree_utils.hpp:#define PARSE_TREE_UTILS_HPP  
./boost/spirit/home/classic/tree/parse_tree_utils.hpp:#endif // !defined(PARSE_TREE_UTILS_HPP)  
./boost/spirit/home/classic/tree/tree_to_xml.hpp:#if !defined(TREE_TO_XML_HPP)  
./boost/spirit/home/classic/tree/tree_to_xml.hpp:#define TREE_TO_XML_HPP  
./boost/spirit/home/classic/tree/tree_to_xml.hpp:#endif // !defined(TREE_TO_XML_HPP)  
./boost/spirit/home/support/detail/lexer/generate_re2c.hpp:#ifndef LEXERTL_GENERATE_RE2C_HPP  
./boost/spirit/home/support/detail/lexer/generate_re2c.hpp:#define LEXERTL_GENERATE_RE2C_HPP  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-12-16 14:04:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-566074685  

Correct me if I am wrong. Only definitions without `BOOST_` prefix is against the policy, right? So, what is wrong with `#ifndef PHOENIX_LIMIT` or `#define BOOST_SPIRIT_CLOSURE_LIMIT PHOENIX_LIMIT`? I do not know how to fix these otherwise, because those will be breaking changes for 20 years old library.

---

## Comment 2

> Username: ned14  
> Created at: 2019-12-16 14:57:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-566096160  

I've asked the Boost leadership for guidance. @pdimov

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-12-16 15:09:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-566101182  

IIUC the things that actually has to be fixed are `grep -R #\s*define include | grep -Ev define\s+BOOST_`  
  
```  
include/boost/spirit/home/classic/attribute.hpp:#define PHOENIX_LIMIT 6  
include/boost/spirit/home/classic/iterator/fixed_size_queue.hpp:#define FIXED_SIZE_QUEUE  
include/boost/spirit/home/classic/iterator/impl/position_iterator.ipp:#define POSITION_ITERATOR_IPP  
include/boost/spirit/home/classic/namespace.hpp:#define SPIRIT_CLASSIC_NAMESPACE_HPP  
include/boost/spirit/home/classic/phoenix/actor.hpp:#define PHOENIX_ACTOR_HPP  
include/boost/spirit/home/classic/phoenix/binders.hpp:#define PHOENIX_BINDERS_HPP  
include/boost/spirit/home/classic/phoenix/casts.hpp:#define PHOENIX_CASTS_HPP  
include/boost/spirit/home/classic/phoenix/casts.hpp:#define PHOENIX_CONSTRUCT_LIMIT PHOENIX_LIMIT  
include/boost/spirit/home/classic/phoenix/closures.hpp:#define CLASSIC_PHOENIX_CLOSURES_HPP  
include/boost/spirit/home/classic/phoenix/composite.hpp:#define PHOENIX_COMPOSITE_HPP  
include/boost/spirit/home/classic/phoenix/functions.hpp:#define PHOENIX_FUNCTIONS_HPP  
include/boost/spirit/home/classic/phoenix/new.hpp:#define PHOENIX_NEW_HPP  
include/boost/spirit/home/classic/phoenix/new.hpp:#define PHOENIX_CONSTRUCT_LIMIT PHOENIX_LIMIT  
include/boost/spirit/home/classic/phoenix/operators.hpp:#define PHOENIX_OPERATORS_HPP  
include/boost/spirit/home/classic/phoenix/operators.hpp:#define CREF const&  
include/boost/spirit/home/classic/phoenix/operators.hpp:#define CREF  
include/boost/spirit/home/classic/phoenix/primitives.hpp:#define PHOENIX_PRIMITIVES_HPP  
include/boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_SPECIAL_OPS_HPP  
include/boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_SSTREAM strstream  
include/boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_SSTREAM stringstream  
include/boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_STD _STLP_STD  
include/boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_NO_STD_NAMESPACE  
include/boost/spirit/home/classic/phoenix/special_ops.hpp:#define PHOENIX_STD std  
include/boost/spirit/home/classic/phoenix/statements.hpp:#define PHOENIX_STATEMENTS_HPP  
include/boost/spirit/home/classic/phoenix/tuples.hpp:#define PHOENIX_TUPLES_HPP  
include/boost/spirit/home/classic/phoenix/tuples.hpp:#define PHOENIX_LIMIT 3  
include/boost/spirit/home/classic/phoenix/tuple_helpers.hpp:#define PHOENIX_TUPLEHELPERS_HPP  
include/boost/spirit/home/classic/tree/impl/parse_tree_utils.ipp:#define PARSE_TREE_UTILS_IPP  
include/boost/spirit/home/classic/tree/impl/tree_to_xml.ipp:#define TREE_TO_XML_IPP  
include/boost/spirit/home/classic/tree/parse_tree_utils.hpp:#define PARSE_TREE_UTILS_HPP  
include/boost/spirit/home/classic/tree/tree_to_xml.hpp:#define TREE_TO_XML_HPP  
include/boost/spirit/home/classic/version.hpp:#define SPIRIT_CLASSIC_VERSION_HPP  
include/boost/spirit/home/classic/version.hpp:#define SPIRIT_VERSION 0x1811  
include/boost/spirit/home/classic/version.hpp:#define SPIRIT_PIZZA_VERSION SPIRIT_MEGA_VEGGI  // :-)  
include/boost/spirit/home/karma/auxiliary/attr_cast.hpp:#define SPIRIT_KARMA_ATTR_CAST_SEP_26_2009_0348PM  
include/boost/spirit/home/karma/detail/alternative_function.hpp:#define SPIRIT_KARMA_ALTERNATIVE_MAR_01_2007_1124AM  
include/boost/spirit/home/karma/detail/attributes.hpp:#define SPIRIT_KARMA_DETAIL_ATTRIBUTES_APR_18_2010_0453PM  
include/boost/spirit/home/karma/detail/fail_function.hpp:#define SPIRIT_KARMA_SEQUENCE_FEB_28_2007_0249PM  
include/boost/spirit/home/karma/detail/indirect_iterator.hpp:#define SPIRIT_KARMA_INDIRECT_ITERATOR_JAN_19_2011_0814PM  
include/boost/spirit/home/karma/detail/pass_container.hpp:#define SPIRIT_PASS_CONTAINER_MAR_15_2009_0114PM  
include/boost/spirit/home/karma/directive/as.hpp:#define SPIRIT_KARMA_AS_DEC_18_0510PM  
include/boost/spirit/home/karma/directive/buffer.hpp:#define SPIRIT_KARMA_BUFFER_AUG_03_2009_0949AM  
include/boost/spirit/home/karma/directive/duplicate.hpp:#define SPIRIT_KARMA_DUPLICATE_JUL_11_2010_0954AM  
include/boost/spirit/home/karma/directive/encoding.hpp:#define SPIRIT_KARMA_ENCODING_MARCH_05_2010_0550PM  
include/boost/spirit/home/karma/directive/omit.hpp:#define SPIRIT_KARMA_OMIT_JUL_20_2009_1008AM  
include/boost/spirit/home/karma/directive/repeat.hpp:#define SPIRIT_KARMA_REPEAT_MAY_18_2009_0926AM  
include/boost/spirit/home/karma/directive/strict_relaxed.hpp:#define SPIRIT_STRICT_RELAXED_APR_22_2010_0959AM  
include/boost/spirit/home/karma/directive/upper_lower_case.hpp:#define SPIRIT_UPPER_LOWER_CASE_JANUARY_19_2009_1142AM  
include/boost/spirit/home/karma/generate_attr.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/karma/nonterminal/detail/fcall.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/karma/operator/alternative.hpp:#define SPIRIT_KARMA_ALTERNATIVE_MAR_01_2007_1117AM  
include/boost/spirit/home/karma/operator/and_predicate.hpp:#define SPIRIT_KARMA_AND_PREDICATE_MAR_22_2009_0412PM  
include/boost/spirit/home/karma/operator/list.hpp:#define SPIRIT_KARMA_LIST_MAY_01_2007_0229PM  
include/boost/spirit/home/karma/operator/not_predicate.hpp:#define SPIRIT_KARMA_NOT_PREDICATE_MAR_21_2009_1132AM  
include/boost/spirit/home/karma/operator/optional.hpp:#define SPIRIT_KARMA_OPTIONAL_MARCH_31_2007_0852AM  
include/boost/spirit/home/karma/operator/sequence.hpp:#define SPIRIT_KARMA_SEQUENCE_FEB_28_2007_0247PM  
include/boost/spirit/home/karma/stream/format_manip_attr.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/karma.hpp:#define SPIRIT_KARMA_CORE_MARCH_06_2007_0833PM  
include/boost/spirit/home/lex/lexer/action.hpp:#define SPIRIT_LEX_ACTION_NOV_18_2007_0743PM  
include/boost/spirit/home/lex/lexer/lexertl/static_version.hpp:#define SPIRIT_STATIC_LEXER_VERSION 0x010000  
include/boost/spirit/home/lex/lexer/sequence.hpp:#define SPIRIT_LEX_SEQUENCE_MAR_28_2007_0610PM  
include/boost/spirit/home/lex/lexer/support_functions.hpp:#define SPIRIT_LEX_SUPPORT_FUNCTIONS_JUN_08_2009_0211PM  
include/boost/spirit/home/lex/lexer/support_functions_expression.hpp:#define SPIRIT_LEX_SUPPORT_FUNCTIONS_EXPRESSION_MAR_22_2011_0711PM  
include/boost/spirit/home/lex/tokenize_and_parse_attr.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/lex/tokenize_and_parse_attr_cxx11.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/qi/action/action.hpp:#define SPIRIT_ACTION_JANUARY_07_2007_1128AM  
include/boost/spirit/home/qi/action.hpp:#define SPIRIT_ACTION_JANUARY_07_2007_1233PM  
include/boost/spirit/home/qi/auxiliary/attr_cast.hpp:#define SPIRIT_QI_ATTR_CAST_SEP_26_2009_0735PM  
include/boost/spirit/home/qi/detail/alternative_function.hpp:#define SPIRIT_ALTERNATIVE_FUNCTION_APRIL_23_2007_1046AM  
include/boost/spirit/home/qi/detail/attributes.hpp:#define SPIRIT_QI_DETAIL_ATTRIBUTES_APR_18_2010_0458PM  
include/boost/spirit/home/qi/detail/expectation_failure.hpp:#define SPIRIT_EXPECTATION_FAILURE_JULY_19_2016  
include/boost/spirit/home/qi/detail/expect_function.hpp:#define SPIRIT_EXPECT_FUNCTION_APR_29_2007_0558PM  
include/boost/spirit/home/qi/detail/fail_function.hpp:#define SPIRIT_FAIL_FUNCTION_APRIL_22_2006_0159PM  
include/boost/spirit/home/qi/detail/pass_container.hpp:#define SPIRIT_PASS_CONTAINER_JANUARY_06_2009_0802PM  
include/boost/spirit/home/qi/detail/pass_function.hpp:#define SPIRIT_PASS_FUNCTION_FEBRUARY_05_2007_1138AM  
include/boost/spirit/home/qi/detail/permute_function.hpp:#define SPIRIT_PERMUTE_FUNCTION_MARCH_13_2007_1129AM  
include/boost/spirit/home/qi/directive/as.hpp:#define SPIRIT_AS_DECEMBER_6_2010_1013AM  
include/boost/spirit/home/qi/directive/encoding.hpp:#define SPIRIT_ENCODING_MARCH_05_2010_0528PM  
include/boost/spirit/home/qi/directive/expect.hpp:#define SPIRIT_EXPECT_JULY_13_2016  
include/boost/spirit/home/qi/directive/hold.hpp:#define SPIRIT_HOLD_FEBRUARY_6_2010_0917AM  
include/boost/spirit/home/qi/directive/lexeme.hpp:#define SPIRIT_LEXEME_MARCH_24_2007_0802AM  
include/boost/spirit/home/qi/directive/matches.hpp:#define SPIRIT_MATCHES_JAN_07_2010_0745PM  
include/boost/spirit/home/qi/directive/no_case.hpp:#define SPIRIT_NO_CASE_OCTOBER_25_2008_0424PM  
include/boost/spirit/home/qi/directive/no_skip.hpp:#define SPIRIT_NO_SKIP_JAN_16_2010_0802PM  
include/boost/spirit/home/qi/directive/omit.hpp:#define SPIRIT_OMIT_MARCH_24_2007_0802AM  
include/boost/spirit/home/qi/directive/raw.hpp:#define SPIRIT_RAW_APRIL_9_2007_0912AM  
include/boost/spirit/home/qi/directive/repeat.hpp:#define SPIRIT_REPEAT_NOVEMBER_14_2008_1148AM  
include/boost/spirit/home/qi/directive/skip.hpp:#define SPIRIT_SKIP_JANUARY_26_2008_0422PM  
include/boost/spirit/home/qi/nonterminal/detail/fcall.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/qi/numeric/bool.hpp:#define SPIRIT_QI_BOOL_SEP_29_2009_0709AM  
include/boost/spirit/home/qi/numeric/bool_policies.hpp:#define SPIRIT_QI_BOOL_POLICIES_SEP_29_2009_0710AM  
include/boost/spirit/home/qi/numeric/detail/numeric_utils.hpp:#define SPIRIT_NUMERIC_UTILS_APRIL_17_2006_0816AM  
include/boost/spirit/home/qi/numeric/detail/numeric_utils.hpp:#define SPIRIT_NUMERIC_INNER_LOOP(z, x, data)                                 \  
include/boost/spirit/home/qi/numeric/detail/numeric_utils.hpp:#define SPIRIT_NUMERIC_INNER_LOOP(z, x, data)                                 \  
include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:#define SPIRIT_REAL_IMPL_APRIL_18_2006_0901AM  
include/boost/spirit/home/qi/numeric/real_policies.hpp:#define SPIRIT_REAL_POLICIES_APRIL_17_2006_1158PM  
include/boost/spirit/home/qi/numeric/uint.hpp:#define SPIRIT_UINT_APR_17_2006_0901AM  
include/boost/spirit/home/qi/operator/alternative.hpp:#define SPIRIT_ALTERNATIVE_FEBRUARY_05_2007_1153AM  
include/boost/spirit/home/qi/operator/and_predicate.hpp:#define SPIRIT_AND_PREDICATE_MARCH_23_2007_0617PM  
include/boost/spirit/home/qi/operator/difference.hpp:#define SPIRIT_DIFFERENCE_FEBRUARY_11_2007_1250PM  
include/boost/spirit/home/qi/operator/expect.hpp:#define SPIRIT_EXPECT_APRIL_29_2007_0445PM  
include/boost/spirit/home/qi/operator/kleene.hpp:#define SPIRIT_KLEENE_JANUARY_07_2007_0818AM  
include/boost/spirit/home/qi/operator/list.hpp:#define SPIRIT_LIST_MARCH_24_2007_1031AM  
include/boost/spirit/home/qi/operator/not_predicate.hpp:#define SPIRIT_NOT_PREDICATE_MARCH_23_2007_0618PM  
include/boost/spirit/home/qi/operator/optional.hpp:#define SPIRIT_OPTIONAL_MARCH_23_2007_1117PM  
include/boost/spirit/home/qi/operator/permutation.hpp:#define SPIRIT_PERMUTATION_OR_MARCH_13_2007_1145PM  
include/boost/spirit/home/qi/operator/plus.hpp:#define SPIRIT_PLUS_MARCH_13_2007_0127PM  
include/boost/spirit/home/qi/operator/sequence.hpp:#define SPIRIT_SEQUENCE_APR_22_2006_0811AM  
include/boost/spirit/home/qi/operator/sequence_base.hpp:#define SPIRIT_SEQUENCE_BASE_APRIL_22_2006_0811AM  
include/boost/spirit/home/qi/operator/sequential_or.hpp:#define SPIRIT_SEQUENTIAL_OR_MARCH_12_2007_1130PM  
include/boost/spirit/home/qi/parse_attr.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/qi/parse_attr_cxx11.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/qi/stream/match_manip_attr.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/support/argument.hpp:#define SPIRIT_DECLARE_ARG(z, n, data)                                        \  
include/boost/spirit/home/support/argument.hpp:#define SPIRIT_USING_ARGUMENT(z, n, data)                                     \  
include/boost/spirit/home/support/argument.hpp:#define SPIRIT_DECLARE_ARG(z, n, data)                                        \  
include/boost/spirit/home/support/argument.hpp:#define SPIRIT_USING_ARGUMENT(z, n, data)                                     \  
include/boost/spirit/home/support/auxiliary/attr_cast.hpp:#define SPIRIT_SUPPORT_ATTR_CAST_OCT_06_2009_00535PM  
include/boost/spirit/home/support/context.hpp:#define SPIRIT_DECLARE_ATTRIBUTE(z, n, data)                                   \  
include/boost/spirit/home/support/context.hpp:#define SPIRIT_USING_ATTRIBUTE(z, n, data)                                     \  
include/boost/spirit/home/support/context.hpp:#define SPIRIT_DECLARE_ATTRIBUTE(z, n, data)                                   \  
include/boost/spirit/home/support/context.hpp:#define SPIRIT_USING_ATTRIBUTE(z, n, data)                                     \  
include/boost/spirit/home/support/detail/as_variant.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/support/detail/endian.hpp:#define SPIRIT_ENDIAN_MAR_21_2009_0349PM  
include/boost/spirit/home/support/detail/function.hpp:#define SPIRIT_WHAT_FUNCTION_APRIL_22_2007_0236PM  
include/boost/spirit/home/support/detail/lexer/generate_cpp.hpp:    os_ << "#define " << upper_name_ + '\n';  
include/boost/spirit/home/support/detail/lexer/generate_re2c.hpp:#define LEXERTL_GENERATE_RE2C_HPP  
include/boost/spirit/home/support/detail/lexer/generate_re2c.hpp:    os_ << "#define " << upper_name_ + '\n';  
include/boost/spirit/home/support/detail/make_vector.hpp:#define SPIRIT_MAKE_VECTOR_07162005_0243  
include/boost/spirit/home/support/detail/make_vector.hpp:#define N BOOST_PP_ITERATION()  
include/boost/spirit/home/support/detail/make_vector.hpp:        #define TEXT(z, n, text) , text  
include/boost/spirit/home/support/detail/pow10.hpp:#define SPIRIT_POW10_DECEMBER_26_2008_1118AM  
include/boost/spirit/home/support/detail/sign.hpp:#define SPIRIT_SIGN_MAR_11_2009_0734PM  
include/boost/spirit/home/support/detail/what_function.hpp:#define SPIRIT_WHAT_FUNCTION_APRIL_22_2007_0236PM  
include/boost/spirit/home/support/sequence_base_id.hpp:#define SPIRIT_SEQUENCE_BASE_ID_MAR_15_2009_1243PM  
include/boost/spirit/home/x3/directive/raw.hpp:#define SPIRIT_X3_RAW_APRIL_9_2007_0912AM  
include/boost/spirit/home/x3/directive/repeat.hpp:#define SPIRIT_X3_REPEAT_APRIL_16_2014_0848AM  
include/boost/spirit/home/x3/directive/with.hpp:#define SPIRIT_X3_WITH_MAY_02_2014_0749AM  
include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp:#define SPIRIT_X3_DETAIL_ATTRIBUTES_APR_18_2010_0458PM  
include/boost/spirit/home/x3/numeric/bool.hpp:#define SPIRIT_X3_BOOL_SEP_29_2009_0709AM  
include/boost/spirit/home/x3/support/numeric_utils/detail/extract_int.hpp:#define SPIRIT_NUMERIC_INNER_LOOP(z, x, data)                                   \  
include/boost/spirit/home/x3/support/numeric_utils/detail/extract_int.hpp:#define SPIRIT_NUMERIC_INNER_LOOP(z, x, data)                                   \  
include/boost/spirit/home/x3/version.hpp:#define SPIRIT_X3_VERSION_INCLUDED  
include/boost/spirit/home/x3/version.hpp:#define SPIRIT_X3_VERSION 0x3005  
include/boost/spirit/include/version.hpp:#define SPIRIT_VERSION_INCLUDED  
include/boost/spirit/include/version.hpp:#define SPIRIT_VERSION 0x2058  
include/boost/spirit/include/version.hpp:#define SPIRIT_PIZZA_VERSION SUPER_HOT_SPANISH_SARDINES  // :-O  
include/boost/spirit/repository/home/karma/directive.hpp:#define SPIRIT_REPOSITORY_KARMA_DIRECTIVE_APR_28_2009_1258PM  
include/boost/spirit/repository/home/karma/nonterminal/subrule.hpp:#define SUBRULE_MODULUS_ASSIGN_OPERATOR(lhs_ref, rhs_ref)                     \  
include/boost/spirit/repository/home/karma/nonterminal.hpp:#define SPIRIT_REPOSITORY_KARMA_NONTERMINAL_AUG_12_2009_0807PM  
include/boost/spirit/repository/home/karma.hpp:#define SPIRIT_REPOSITORY_KARMA_APR_28_2009_1259PM  
include/boost/spirit/repository/home/qi/directive/distinct.hpp:#define SPIRIT_REPOSITORY_QI_DISTINCT_MAY_20_2009_0825M  
include/boost/spirit/repository/home/qi/directive/kwd.hpp:#define SPIRIT_KWD_NOVEMBER_14_2008_1148AM  
include/boost/spirit/repository/home/qi/directive.hpp:#define SPIRIT_REPOSITORY_QI_DIRECTIVE_APR_28_2009_1258PM  
include/boost/spirit/repository/home/qi/nonterminal/subrule.hpp:#define SUBRULE_MODULUS_ASSIGN_OPERATOR(lhs_ref, rhs_ref)                     \  
include/boost/spirit/repository/home/qi/nonterminal.hpp:#define SPIRIT_REPOSITORY_QI_NONTERMINAL_AUG_12_2009_1140AM  
include/boost/spirit/repository/home/qi/operator/detail/keywords.hpp:#define SPIRIT_KEYWORDS_DETAIL_MARCH_13_2007_1145PM  
include/boost/spirit/repository/home/qi/operator/keywords.hpp:#define SPIRIT_KEYWORDS_OR_MARCH_13_2007_1145PM  
include/boost/spirit/repository/home/qi/operator.hpp:#define SPIRIT_REPOSITORY_QI_OPERATOR_OCT_20_2010_1258PM  
include/boost/spirit/repository/home/qi/primitive.hpp:#define SPIRIT_REPOSITORY_QI_PRIMITIVE_APR_28_2009_1258PM  
include/boost/spirit/repository/home/qi.hpp:#define SPIRIT_REPOSITORY_QI_APR_28_2009_1258PM  
include/boost/spirit/version.hpp:#define SPIRIT_VERSION_NOVEMBER_13_2008_0834AM  
```  
  
But I still not sure if renaming of `SPIRIT_VERSION` or `SPIRIT_X3_VERSION` is desirable.

---

## Comment 4

> Username: ned14  
> Created at: 2019-12-16 15:10:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-566101796  

I should be able to supply a complete list of regexs for the entire of all issues in Boost in the next few days. Slack told me to go report to each library individually.

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-12-16 21:44:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-566257595  

I am afraid of renaming the next macros:  
```  
PHOENIX_LIMIT  
PHOENIX_CONSTRUCT_LIMIT  
SPIRIT_VERSION  
SPIRIT_STATIC_LEXER_VERSION  
SPIRIT_DECLARE_ARG  
SPIRIT_USING_ARGUMENT  
SPIRIT_DECLARE_ARG  
SPIRIT_USING_ARGUMENT  
SPIRIT_X3_VERSION  
SPIRIT_VERSION  
```

---

## Comment 6

> Username: djowel  
> Created at: 2019-12-16 23:10:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-566290151  

> I am afraid of renaming the next macros:  
  
That will break a lot of user code.

---

## Comment 7

> Username: Kojoley  
> Created at: 2019-12-16 23:58:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-566304901  

@ned14 What is your interest in this? Do you want to automatically rename macros to be able to use multiple Boost versions in the same codebase?  
  
```cpp  
#define BOOST_SPIRIT_VERSION 123456  
#ifndef SPIRIT_VERSION  
#define SPIRIT_VERSION BOOST_SPIRIT_VERSION  
#endif  
```  
Will the code above work for you?  
  
I am sorry, but removing of definitions listed in my previous message will take years of deprecation period.

---

## Comment 8

> Username: ned14  
> Created at: 2019-12-17 13:27:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-566540768  

The current suggestion on boost-dev for how to handle these is that for the next three to six releases of Boost, a deprecation warning be given to code which sets or optionally uses these unprefixed macros. Once `BOOST_VERSION` tips past a fixed limit however, it becomes a compile time error to use the unprefixed macros.  
  
You may not be aware of https://github.com/boostorg/boost/pull/353, which if accepted will cause any Boost library which sets an unprefixed macro to fail its build.

---

## Comment 9

> Username: Kojoley  
> Created at: 2019-12-17 13:41:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-566545984  

> The current suggestion on boost-dev for how to handle these is that for the next three to six releases of Boost, a deprecation warning be given to code which sets or optionally uses these unprefixed macros. Once `BOOST_VERSION` tips past a fixed limit however, it becomes a compile time error to use the unprefixed macros.  
  
I did not see that on mail list, could you give me a link please? I would also prefer it as a policy document and not as someones opinion, because in your interpretation I can no longer use even `__cplusplus` macro in Boost code.

---

## Comment 10

> Username: djowel  
> Created at: 2019-12-18 00:27:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-566810835  

> I would also prefer it as a policy document and not as someones opinion  
  
I second that.

---

## Comment 11

> Username: saki7  
> Created at: 2025-05-10 01:04:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-2868173425  

I don't think we need to fix any implementation details unless Boost organization's core developers explicitly say that we MUST remove specific macros as per organization's policy. As far as I know, no consensus was made, and it seems that the PRs listed above are abandoned in boostorg/boost.  
  
We tend to try to not pollute global namespaces with unprefixed macros, but still some of them exists for historical reasons. Without a concrete goal, we can't *fix* things right away.  
  
Closing as not achievable. Feel free to reopen if you have objections.

---

## Comment 12

> Username: saki7  
> Created at: 2025-05-10 01:13:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/562#issuecomment-2868179235  

OK so I found this guideline: <https://www.boost.org/development/requirements.html>  
  
> Design and Programming  
> [snip]  
> Macro (gasp!) names all uppercase and begin with BOOST_.  
  
But the guideline says this too:  
  
> Please use these guidelines as a checklist for preparing the content a library submission. Not every guideline applies to every library, but a reasonable effort to comply is expected.  
  
So it's basically like a house rule, we do have a rationale to follow it but I think it's not an urgent matter.
