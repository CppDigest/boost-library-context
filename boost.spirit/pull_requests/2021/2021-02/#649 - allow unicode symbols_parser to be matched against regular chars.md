# #649 allow unicode symbols_parser to be matched against regular chars [Closed]

> Username: theodelrieu  
> Created at: 2021-02-19 08:30:07 UTC  
> Updated at: 2025-05-09 11:52:17 UTC  
> Closed at: 2021-02-19 17:02:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/649  

Hi,  
  
First, let me say that I'm almost certain this patch is bad, there is surely a better way to solve the problem I'm facing:  
  
I have a grammar that is pure ASCII, but it can be a subset of another grammar which requires Unicode, therefore I have defined a `symbols_parser` with UTF32 values in the former grammar.  
  
Here's the error I get:  
  
```  
/Users/theo/Projects/boost/boost/spirit/home/x3/string/detail/tst.hpp:74:29: error: no matching function for call to object of type  
      'boost::spirit::x3::case_compare<boost::spirit::char_encoding::unicode>'  
                int32_t c = comp(*i,p->id);  
                            ^~~~  
/Users/theo/Projects/boost/boost/spirit/home/x3/string/tst.hpp:56:26: note: in instantiation of function template specialization  
      'boost::spirit::x3::detail::tst_node<char32_t, int>::find<const char *, boost::spirit::x3::case_compare<boost::spirit::char_encoding::unicode> >' requested here  
            return node::find(root, first, last, caseCompare);  
                         ^  
/Users/theo/Projects/boost/boost/spirit/home/x3/string/symbols.hpp:205:27: note: in instantiation of function template specialization 'boost::spirit::x3::tst<char32_t,  
      int>::find<const char *, boost::spirit::x3::case_compare<boost::spirit::char_encoding::unicode> >' requested here  
                = lookup->find(first, last, get_case_compare<Encoding>(context)))  
                          ^  
/Users/theo/Projects/boost/boost/spirit/home/x3/core/parse.hpp:36:29: note: in instantiation of function template specialization  
      'boost::spirit::x3::symbols_parser<boost::spirit::char_encoding::unicode, int, boost::spirit::x3::tst<char32_t, int> >::parse<const char *,  
      boost::spirit::x3::unused_type, int>' requested here  
        return as_parser(p).parse(first, last, unused, unused, attr);  
                            ^  
/Users/theo/Projects/boost/boost/spirit/home/x3/core/parse.hpp:48:16: note: in instantiation of function template specialization 'boost::spirit::x3::parse_main<const char  
      *, boost::spirit::x3::symbols_parser<boost::spirit::char_encoding::unicode, int, boost::spirit::x3::tst<char32_t, int> >, int>' requested here  
        return parse_main(first, last, p, attr);  
               ^  
./test.hpp:73:35: note: in instantiation of function template specialization 'boost::spirit::x3::parse<const char *,  
      boost::spirit::x3::symbols_parser<boost::spirit::char_encoding::unicode, int, boost::spirit::x3::tst<char32_t, int> >, int>' requested here  
        return boost::spirit::x3::parse(in, last, p, attr)  
                                  ^  
symbols3.cpp:94:21: note: in instantiation of function template specialization 'spirit_test::test_attr<char,  
      boost::spirit::x3::symbols_parser<boost::spirit::char_encoding::unicode, int, boost::spirit::x3::tst<char32_t, int> >, int>' requested here  
        BOOST_TEST((test_attr("a3", foo, r)));  
                    ^  
/Users/theo/Projects/boost/boost/spirit/home/x3/support/no_case.hpp:28:17: note: candidate template ignored: deduced conflicting types for parameter 'Char'  
      ('char' vs. 'char32_t')  
        int32_t operator()(Char lc, Char rc) const  
```

---

## Comment 1

> Username: Kojoley  
> Created_at: 2021-02-19 15:53:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/649#issuecomment-782163267  

> I have a grammar that is pure ASCII, but it can be a subset of another grammar which requires Unicode, therefore I have defined a symbols_parser with UTF32 values in the former grammar.  
  
But why the input is `char` string in the test then? This is exactly the opposite of what you are describing.  
  
  
I can understand usage of ASCII parser (`x3::ascii::*`, not `x3::*` which is almost always alias of `x3::standard::*`) with Unicode input, but Unicode parser with ASCII (actually any non-Unicode) input seems pretty odd to me, and I have been thinking a lot about adding a static assert for that.

---

## Comment 2

> Username: theodelrieu  
> Created_at: 2021-02-19 17:02:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/649#issuecomment-782206066  

@Kojoley   
  
>I can understand usage of ASCII parser [...] with Unicode input  
  
True, I think I can just make the lower grammar ascii only, and see how it goes, I'll try to do that during the week-end.  
  
Meanwhile, I'll close this PR.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2021-02-19 17:47:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/649#issuecomment-782232523  

If it does not either - file a bug, and thanks for your efforts to make Spirit better!

---
