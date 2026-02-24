# #402 - empty_value change does not compile on Mac [Closed]

> Username: yresk  
> Created at: 2022-08-14 09:45:48 UTC  
> Updated at: 2022-08-14 20:16:14 UTC  
> Closed at: 2022-08-14 20:16:14 UTC  
> Url: https://github.com/boostorg/url/issues/402  

The empty_value change 438d74c7fb85c604d9d8bec1c0a2c23aabd8710a fails to compile on Mac while including boost/url/src.hpp  
  
(I'm using boost 1.80.0 b1)  
  
  
In file included from .../XXX.cpp:5:  
In file included from .../boost_url_source/include/boost/url/src.hpp:39:  
In file included from .../boost_url_source/include/boost/url/detail/impl/segments_encoded_iterator_impl.ipp:15:  
.../boost_url_source/include/boost/url/rfc/detail/path_rules.hpp:88:16: error: constexpr variable cannot have non-literal type 'const boost::urls::grammar::sequence_rule_t<boost::urls::grammar::detail::squelch_rule_t<boost::urls::grammar::ch_delim_rule>, boost::urls::pct_encoded_rule_t<boost::urls::grammar::detail::charset_ref<boost::urls::grammar::lut_chars>>>'  
constexpr auto slash_segment_rule =  
               ^  
.../boost_url_source/include/boost/url/grammar/tuple_rule.hpp:90:7: note: 'sequence_rule_t<boost::urls::grammar::detail::squelch_rule_t<boost::urls::grammar::ch_delim_rule>, boost::urls::pct_encoded_rule_t<boost::urls::grammar::detail::charset_ref<boost::urls::grammar::lut_chars>>>' is not literal because it has base class 'empty_value<detail::tuple<squelch_rule_t<ch_delim_rule>, pct_encoded_rule_t<charset_ref<lut_chars>>>>' of non-literal type  
    : empty_value<  
      ^~~~~~~~~~~~  
In file included from .../XXX.cpp:5:  
In file included from .../boost_url_source/include/boost/url/src.hpp:39:  
In file included from .../boost_url_source/include/boost/url/detail/impl/segments_encoded_iterator_impl.ipp:15:  
.../boost_url_source/include/boost/url/rfc/detail/path_rules.hpp:132:16: error: constexpr variable cannot have non-literal type 'const boost::urls::grammar::range_rule_t<boost::urls::grammar::sequence_rule_t<boost::urls::grammar::detail::squelch_rule_t<boost::urls::grammar::ch_delim_rule>, boost::urls::pct_encoded_rule_t<boost::urls::grammar::detail::charset_ref<boost::urls::grammar::lut_chars>>>>'  
constexpr auto path_abempty_rule =  
               ^  
.../boost_url_source/include/boost/url/grammar/range_rule.hpp:330:13: note: 'range_rule_t<boost::urls::grammar::sequence_rule_t<boost::urls::grammar::detail::squelch_rule_t<boost::urls::grammar::ch_delim_rule>, boost::urls::pct_encoded_rule_t<boost::urls::grammar::detail::charset_ref<boost::urls::grammar::lut_chars>>>>' is not literal because it has data member 'next_' of non-literal type 'const boost::urls::grammar::sequence_rule_t<boost::urls::grammar::detail::squelch_rule_t<boost::urls::grammar::ch_delim_rule>, boost::urls::pct_encoded_rule_t<boost::urls::grammar::detail::charset_ref<boost::urls::grammar::lut_chars>>>'  
    R const next_;  
            ^  
In file included from .../XXX.cpp:5:  
In file included from .../boost_url_source/include/boost/url/src.hpp:61:  
In file included from .../boost_url_source/include/boost/url/impl/url_base.ipp:24:  
.../boost_url_source/include/boost/url/rfc/detail/fragment_rule.hpp:50:16: error: constexpr variable cannot have non-literal type 'const boost::urls::grammar::optional_rule_t<boost::urls::grammar::sequence_rule_t<boost::urls::grammar::detail::squelch_rule_t<boost::urls::grammar::ch_delim_rule>, boost::urls::pct_encoded_rule_t<boost::urls::grammar::detail::charset_ref<boost::urls::grammar::lut_chars>>>>'  
constexpr auto fragment_part_rule =  
               ^  
.../boost_url_source/include/boost/url/grammar/optional_rule.hpp:90:10: note: 'optional_rule_t<boost::urls::grammar::sequence_rule_t<boost::urls::grammar::detail::squelch_rule_t<boost::urls::grammar::ch_delim_rule>, boost::urls::pct_encoded_rule_t<boost::urls::grammar::detail::charset_ref<boost::urls::grammar::lut_chars>>>>' is not literal because it has data member 'r_' of non-literal type 'boost::urls::grammar::sequence_rule_t<boost::urls::grammar::detail::squelch_rule_t<boost::urls::grammar::ch_delim_rule>, boost::urls::pct_encoded_rule_t<boost::urls::grammar::detail::charset_ref<boost::urls::grammar::lut_chars>>>'  
    Rule r_;  
         ^  
3 errors generated.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-08-14 10:13:17 UTC  
> Url: https://github.com/boostorg/url/issues/402#issuecomment-1214327143  

Both Boost.Core and this project run CI tests on Mac, so you have to be more specific than that. Which Mac, which compiler, which compiler version, what compilation flags were in effect?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-14 13:47:21 UTC  
> Url: https://github.com/boostorg/url/issues/402#issuecomment-1214381610  

You need the latest Boost.Core from the *master* branch:  
https://github.com/boostorg/core/commits/master

---

## Comment 3

> Username: yresk  
> Created at: 2022-08-14 20:16:14 UTC  
> Url: https://github.com/boostorg/url/issues/402#issuecomment-1214442071  

I missed the boost 1.80.0 release, all ok with that version.
