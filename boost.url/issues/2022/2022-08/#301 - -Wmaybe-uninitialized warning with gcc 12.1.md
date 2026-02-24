# #301 - -Wmaybe-uninitialized warning with gcc 12.1 [Closed]

> Username: pavel-odintsov  
> Created at: 2022-08-02 19:05:58 UTC  
> Updated at: 2022-08-05 01:16:38 UTC  
> Closed at: 2022-08-05 01:16:38 UTC  
> Url: https://github.com/boostorg/url/issues/301  

Hello!  
  
Thank you for very nice library.   
  
I did my best to check for similar error. Sorry in case of duplicate report.  
  
Error text:  
```  
[ 65%] Building CXX object CMakeFiles/gobgp_action.dir/actions/gobgp_action.cpp.o  
In file included from /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/sequence_rule.hpp:137,  
                 from /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar.hpp:30,  
                 from /opt/fastnetmon/libraries/url_224cca0/include/boost/url.hpp:13,  
                 from /opt/fastnetmon/libraries/url_224cca0/include/boost/url/src.hpp:28,  
                 from /home/odintsov/fastnetmon/src/fast_library.cpp:51:  
In member function ‘void boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&, const boost::mp11::mp_true&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’,  
    inlined from ‘typename std::enable_if<(Ir < (1 + sizeof... (Rn)))>::type boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:252:14,  
    inlined from ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:276:12,  
    inlined from ‘boost::urls::result<typename T::value_type> boost::urls::grammar::parse(const char*&, const char*, const Rule&) [with Rule = sequence_rule_t<char_rule, boost::urls::detail::segment_ns_rule_t>]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/parse.hpp:34:19,  
    inlined from ‘boost::urls::result<T> boost::urls::grammar::range<T>::impl2<R0, R1, <anonymous> >::first(const char*&, const char*) const [with R0 = boost::urls::grammar::sequence_rule_t<boost::urls::grammar::char_rule, boost::urls::detail::segment_ns_rule_t>; R1 = boost::urls::grammar::sequence_rule_t<boost::urls::grammar::char_rule, boost::urls::pct_encoded_ref_rule_t<boost::urls::grammar::lut_chars> >; bool Small = true; T = boost::urls::pct_encoded_view]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/range_rule.hpp:235:30:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:13: warning: ‘((unsigned char*)((char*)&<unnamed> + offsetof(boost::system::result<boost::urls::pct_encoded_view, boost::system::error_code>,boost::system::result<boost::urls::pct_encoded_view, boost::system::error_code>::v_.boost::variant2::variant<boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ma_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_mc_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ca_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_cc_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::st_)))[24]’ may be used uninitialized [-Wmaybe-uninitialized]  
  212 |             v = rv.error();  
      |             ^  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp: In member function ‘boost::urls::result<T> boost::urls::grammar::range<T>::impl2<R0, R1, <anonymous> >::first(const char*&, const char*) const [with R0 = boost::urls::grammar::sequence_rule_t<boost::urls::grammar::char_rule, boost::urls::detail::segment_ns_rule_t>; R1 = boost::urls::grammar::sequence_rule_t<boost::urls::grammar::char_rule, boost::urls::pct_encoded_ref_rule_t<boost::urls::grammar::lut_chars> >; bool Small = true; T = boost::urls::pct_encoded_view]’:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:15: note: ‘<anonymous>’ declared here  
  212 |             v = rv.error();  
      |             ~~^~~~~~~~~~~~  
[ 66%] Linking CXX static library libattack_detection.a  
[ 66%] Built target attack_detection  
In member function ‘void boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&, const boost::mp11::mp_true&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’,  
    inlined from ‘typename std::enable_if<(Ir < (1 + sizeof... (Rn)))>::type boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:252:14,  
    inlined from ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:276:12:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:13: warning: ‘((unsigned char*)((char*)&<unnamed> + offsetof(boost::system::result<boost::urls::pct_encoded_view, boost::system::error_code>,boost::system::result<boost::urls::pct_encoded_view, boost::system::error_code>::v_.boost::variant2::variant<boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ma_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_mc_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ca_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_cc_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::st_)))[24]’ may be used uninitialized [-Wmaybe-uninitialized]  
  212 |             v = rv.error();  
      |             ^  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp: In member function ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:15: note: ‘<anonymous>’ declared here  
  212 |             v = rv.error();  
      |             ~~^~~~~~~~~~~~  
  
```  
  
I include it this way:  
```  
#include <boost/url/src.hpp>  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-02 19:07:47 UTC  
> Updated at: 2022-08-02 19:08:06 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1203108014  

I don't mind duplicate reports, it lets me close multiple issues at once :)  
But this is not a duplicate. Is gcc 12.1 an "official" release or is it a beta or something?

---

## Comment 2

> Username: pavel-odintsov  
> Created at: 2022-08-02 19:10:44 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1203110611  

Hello! Thank you for fast reply. Definitely stable one: https://gcc.gnu.org/gcc-12/changes.html

---

## Comment 3

> Username: pdimov  
> Created at: 2022-08-02 19:19:58 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1203118464  

Try changing https://github.com/boostorg/system/blob/f2e1db8021ac03bde88028a95b99581922bb664f/include/boost/system/result.hpp#L225 to `return v_.index() == 1;` and see if it clears the warning.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-08-02 19:24:17 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1203122314  

We might need to launder the buffers here:  
https://github.com/CPPAlliance/url/blob/224cca0dc962a73946b88a57b55615db0d6d2cae/include/boost/url/grammar/range_rule.hpp#L65  
https://github.com/CPPAlliance/url/blob/224cca0dc962a73946b88a57b55615db0d6d2cae/include/boost/url/grammar/impl/range_rule.hpp#L35

---

## Comment 5

> Username: pavel-odintsov  
> Created at: 2022-08-03 11:21:45 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1203816145  

> Try changing https://github.com/boostorg/system/blob/f2e1db8021ac03bde88028a95b99581922bb664f/include/boost/system/result.hpp#L225 to `return v_.index() == 1;` and see if it clears the warning.  
  
Hello!  
  
I've tried but without any significant changes.  
  
Current warning:  
```  
[ 67%] Built target attack_detection  
In file included from /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/sequence_rule.hpp:137,  
                 from /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar.hpp:30,  
                 from /opt/fastnetmon/libraries/url_224cca0/include/boost/url.hpp:13,  
                 from /opt/fastnetmon/libraries/url_224cca0/include/boost/url/src.hpp:28,  
                 from /home/odintsov/fastnetmon/src/fast_library.cpp:51:  
In member function ‘void boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&, const boost::mp11::mp_true&) [with long unsigned int Ir = 1; long unsigned int Iv = 1; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’,  
    inlined from ‘typename std::enable_if<(Ir < (1 + sizeof... (Rn)))>::type boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&) [with long unsigned int Ir = 1; long unsigned int Iv = 1; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:252:14,  
    inlined from ‘void boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&, const boost::mp11::mp_false&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:235:14,  
    inlined from ‘typename std::enable_if<(Ir < (1 + sizeof... (Rn)))>::type boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:252:14,  
    inlined from ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:276:12:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:13: warning: ‘((std::size_t*)((char*)&<unnamed> + offsetof(boost::system::result<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>,boost::system::result<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::v_.boost::variant2::variant<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ma_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_mc_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ca_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_cc_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::st_)))[3]’ may be used uninitialized [-Wmaybe-uninitialized]  
  212 |             v = rv.error();  
      |             ^  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp: In member function ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:15: note: ‘<anonymous>’ declared here  
  212 |             v = rv.error();  
      |             ~~^~~~~~~~~~~~  
In member function ‘void boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&, const boost::mp11::mp_true&) [with long unsigned int Ir = 1; long unsigned int Iv = 1; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’,  
    inlined from ‘typename std::enable_if<(Ir < (1 + sizeof... (Rn)))>::type boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&) [with long unsigned int Ir = 1; long unsigned int Iv = 1; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:252:14,  
    inlined from ‘void boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&, const boost::mp11::mp_false&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:235:14,  
    inlined from ‘typename std::enable_if<(Ir < (1 + sizeof... (Rn)))>::type boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:252:14,  
    inlined from ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:276:12:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:13: warning: ‘((const char**)((char*)&<unnamed> + offsetof(boost::system::result<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>,boost::system::result<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::v_.boost::variant2::variant<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ma_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_mc_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ca_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_cc_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::st_)))[4]’ may be used uninitialized [-Wmaybe-uninitialized]  
  212 |             v = rv.error();  
      |             ^  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp: In member function ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:15: note: ‘<anonymous>’ declared here  
  212 |             v = rv.error();  
      |             ~~^~~~~~~~~~~~  
In member function ‘void boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&, const boost::mp11::mp_true&) [with long unsigned int Ir = 1; long unsigned int Iv = 1; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’,  
    inlined from ‘typename std::enable_if<(Ir < (1 + sizeof... (Rn)))>::type boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&) [with long unsigned int Ir = 1; long unsigned int Iv = 1; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:252:14,  
    inlined from ‘void boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&, const boost::mp11::mp_false&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:235:14,  
    inlined from ‘typename std::enable_if<(Ir < (1 + sizeof... (Rn)))>::type boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:252:14,  
    inlined from ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:276:12:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:13: warning: ‘((std::size_t*)((char*)&<unnamed> + offsetof(boost::system::result<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>,boost::system::result<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::v_.boost::variant2::variant<boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ma_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_mc_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ca_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_cc_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_base_impl<true, true, boost::urls::detail::ipvfuture_rule_t::value_type, boost::system::error_code>::st_)))[5]’ may be used uninitialized [-Wmaybe-uninitialized]  
  212 |             v = rv.error();  
      |             ^  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp: In member function ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::detail::ipvfuture_rule_t; Rn = {boost::urls::grammar::char_rule}]’:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:15: note: ‘<anonymous>’ declared here  
  212 |             v = rv.error();  
      |             ~~^~~~~~~~~~~~  
In member function ‘void boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&, const boost::mp11::mp_true&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’,  
    inlined from ‘typename std::enable_if<(Ir < (1 + sizeof... (Rn)))>::type boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:252:14,  
    inlined from ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:276:12,  
    inlined from ‘boost::urls::result<typename T::value_type> boost::urls::grammar::parse(const char*&, const char*, const Rule&) [with Rule = sequence_rule_t<char_rule, boost::urls::detail::segment_ns_rule_t>]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/parse.hpp:34:19,  
    inlined from ‘boost::urls::result<T> boost::urls::grammar::range<T>::impl2<R0, R1, <anonymous> >::first(const char*&, const char*) const [with R0 = boost::urls::grammar::sequence_rule_t<boost::urls::grammar::char_rule, boost::urls::detail::segment_ns_rule_t>; R1 = boost::urls::grammar::sequence_rule_t<boost::urls::grammar::char_rule, boost::urls::pct_encoded_ref_rule_t<boost::urls::grammar::lut_chars> >; bool Small = true; T = boost::urls::pct_encoded_view]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/range_rule.hpp:235:30:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:13: warning: ‘((unsigned char*)((char*)&<unnamed> + offsetof(boost::system::result<boost::urls::pct_encoded_view, boost::system::error_code>,boost::system::result<boost::urls::pct_encoded_view, boost::system::error_code>::v_.boost::variant2::variant<boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ma_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_mc_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ca_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_cc_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::st_)))[24]’ may be used uninitialized [-Wmaybe-uninitialized]  
  212 |             v = rv.error();  
      |             ^  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp: In member function ‘boost::urls::result<T> boost::urls::grammar::range<T>::impl2<R0, R1, <anonymous> >::first(const char*&, const char*) const [with R0 = boost::urls::grammar::sequence_rule_t<boost::urls::grammar::char_rule, boost::urls::detail::segment_ns_rule_t>; R1 = boost::urls::grammar::sequence_rule_t<boost::urls::grammar::char_rule, boost::urls::pct_encoded_ref_rule_t<boost::urls::grammar::lut_chars> >; bool Small = true; T = boost::urls::pct_encoded_view]’:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:15: note: ‘<anonymous>’ declared here  
  212 |             v = rv.error();  
      |             ~~^~~~~~~~~~~~  
In member function ‘void boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&, const boost::mp11::mp_true&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’,  
    inlined from ‘typename std::enable_if<(Ir < (1 + sizeof... (Rn)))>::type boost::urls::grammar::detail::parse_sequence<false, R0, Rn ...>::apply(const char*&, const char*, boost::mp11::mp_size_t<I>&, boost::mp11::mp_size_t<Iv>&) [with long unsigned int Ir = 0; long unsigned int Iv = 0; R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:252:14,  
    inlined from ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’ at /opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:276:12:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:13: warning: ‘((unsigned char*)((char*)&<unnamed> + offsetof(boost::system::result<boost::urls::pct_encoded_view, boost::system::error_code>,boost::system::result<boost::urls::pct_encoded_view, boost::system::error_code>::v_.boost::variant2::variant<boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ma_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_mc_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_ca_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_cc_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::<unnamed>.boost::variant2::detail::variant_base_impl<true, true, boost::urls::pct_encoded_view, boost::system::error_code>::st_)))[24]’ may be used uninitialized [-Wmaybe-uninitialized]  
  212 |             v = rv.error();  
      |             ^  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp: In member function ‘boost::urls::result<typename boost::mp11::detail::mp_eval_if_c_impl<boost::urls::grammar::sequence_rule_t<R0, Rn>::IsList, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type, boost::mp11::mp_front, typename boost::mp11::detail::mp_remove_impl<std::tuple<typename R0::value_type, typename Rn::value_type ...>, void>::type>::type> boost::urls::grammar::sequence_rule_t<R0, Rn>::parse(const char*&, const char*) const [with R0 = boost::urls::grammar::char_rule; Rn = {boost::urls::detail::segment_ns_rule_t}]’:  
/opt/fastnetmon/libraries/url_224cca0/include/boost/url/grammar/impl/sequence_rule.hpp:212:15: note: ‘<anonymous>’ declared here  
  212 |             v = rv.error();  
      |             ~~^~~~~~~~~~~~  
  
```

---

## Comment 6

> Username: pdimov  
> Created at: 2022-08-03 12:39:47 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1203896751  

Unfortunately, my initial theory was wrong and the warning has a different cause. The above occurs because `result<T>` is basically `union { T t; error_code e; }` and when `T` is trivial, as it is in the above cases, assigning the union (with an `error_code` in it) causes the compiler to complain that the part of `T` that exceeds `sizeof(error_code)` will be copied uninitialized.  
  
That's technically true but the union assignment operator is compiler-generated, so the compiler shouldn't be complaining about it.  
  
So I'm afraid that this is a false positive in gcc-12. We probably need to report it as a bug, but isolating this into a small and self-contained example is not going to be easy, as this warning is issued by the optimizer and is very sensitive to inlining. Smaller examples don't seem to warn.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-08-03 12:48:41 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1203906611  

@pavel-odintsov I would maybe try something crazy  
  
what if we do something crazy with the thing  
. maybe we can rewrite this a different way  
```  
v = *(rv.get_error_ptr());  
```  
  
internally cast the address of the error part of the union to void?  
```  
v = *reinterpret_cast<error_code const*>(rv.get_error_addr_as_void());  
```  
  
idk... try fooling around with the source code to `result`, and my code and maybe we can make the compiler shut up

---

## Comment 8

> Username: pdimov  
> Created at: 2022-08-03 12:50:48 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1203908855  

A simple example (https://godbolt.org/z/eoKqPxe9f) doesn't warn, so there must be something else that's confusing the compiler.

---

## Comment 9

> Username: alandefreitas  
> Created at: 2022-08-03 21:12:46 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1204488940  

> T that exceeds sizeof(error_code) will be copied uninitialized.  
  
In a workaround, maybe `sequence_rule` can initialize the result (e.g.: `v = 0;`) with the trivial type before `v = rv.error();`?

---

## Comment 10

> Username: pdimov  
> Created at: 2022-08-03 21:16:51 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1204492791  

That's not going to help, as the uninitialized part of the rhs will still get copied over.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-08-05 01:16:38 UTC  
> Url: https://github.com/boostorg/url/issues/301#issuecomment-1205935477  

Should be fixed now.
