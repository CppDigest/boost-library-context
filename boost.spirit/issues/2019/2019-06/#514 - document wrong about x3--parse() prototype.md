# #514 - document wrong about x3::parse() prototype [Closed]

> Username: rockonedege  
> Created at: 2019-06-16 13:12:37 UTC  
> Updated at: 2019-06-19 00:10:24 UTC  
> Closed at: 2019-06-19 00:10:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/514  

it looks x3::parse and x3::phrase_parse does not accept multiple attributes anymore as it does with qi.  
  
> For sequences only: Spirit.X3 exposes a set of API functions usable mainly with sequences. Very much like the functions of the scanf and printf families these functions allow to pass the attributes for each of the elements of the sequence separately. Using the corresponding overload of X3's parse function, the expression above could be rewritten as:  
  
>double d1 = 0.0, d2 = 0.0;  
>x3::phrase_parse(begin, end, x3::double_ >> x3::double_, x3::space, d1, d2);  
  
>where the first attribute is used for the first double_, and the second attribute is used for the >second double_.   
>x3::phrase_parse(begin, end, x3::double_ >> x3::double_, x3::space, d1, d2);  
  
The above citation from the [doc(boost 1.70/spirit 3.04)](https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/spirit_x3/abstracts/compound_attributes.html) does not hold true any more.

---

## Comment 1

> Username: djowel  
> Created at: 2019-06-17 02:10:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/514#issuecomment-502510531  

The tip in the docs need to be fixed. X3 does not support more than one attribute anymore in the parse function only. And I  don't think we need to. We want to keep it simple. Just use a tie (tuple tie or fusion tie).

---

## Comment 2

> Username: rockonedege  
> Created at: 2019-06-17 11:46:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/514#issuecomment-502649230  

Yeah, that's what I thought.   
  
It would be nice have an example for this use case in the tutorial.   
  
My own experience is that new users to`spirit` or ones with prior experience with `qi` would find it useful.  
  
here's what works for me  in case it helps:  
``` cpp  
std::tuple<uint32_t, uint32_t, uint32_t> parse_message_prefix_revision(const std::string &s)  
{  
  
  namespace x3 = boost::spirit::x3;  
  
  auto const uint_3_digits = x3::uint_parser<std::uint32_t, 10, 3, 3>{};  
  auto const uint_4_digits = x3::uint_parser<std::uint32_t, 10, 4, 4>{};  
  
  auto iter = s.cbegin();  
  auto end_iter = s.cend();  
  
  std::tuple<uint32_t, uint32_t, uint32_t> len_id_rev;  
  
  x3::parse(iter, end_iter,  
            uint_4_digits >> uint_4_digits >> uint_3_digits,  
            len_id_rev);  
  
  return len_id_rev;  
}  
  
BOOST_AUTO_TEST_CASE(test_parse_message_prefix_revision)  
{  
  auto[len, mid, revision] = parse_message_prefix_revision("00200060001");  
  BOOST_TEST(20 == len);  
  BOOST_TEST(60 == mid);  
  BOOST_TEST(1 == revision);  
}  
  
```

---

## Comment 3

> Username: djowel  
> Created at: 2019-06-17 22:28:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/514#issuecomment-502874342  

> It would be nice have an example for this use case in the tutorial.  
  
Would you volunteer to provide this example? A PR will be appreciated.

---

## Comment 4

> Username: rockonedege  
> Created at: 2019-06-18 07:05:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/514#issuecomment-502976019  

Done. Please check PR #516

---

## Comment 5

> Username: djowel  
> Created at: 2019-06-19 00:10:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/514#issuecomment-503356705  

Thanks @rockonedege !
