# #509 - attribute of x3::raw [Closed]

> Username: wanghan02  
> Created at: 2019-05-15 09:03:57 UTC  
> Updated at: 2019-05-16 22:50:23 UTC  
> Closed at: 2019-05-16 22:50:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/509  

`x3::raw` has a placeholder attribute type `raw_attribute_type`. I think it's because we don't know the `Iterator` type yet before calling `parse` function. Then we have some tricks to replace this placeholder type in semantic action if it is attached directly to `x3::raw`. It even works if `x3::raw` is the only parser with attribute in a sequence of parsers. However, it doesn't work for more complex parsers say a sequence of `x3::raw`, or alternative parser with `x3::raw` as one of the branches.   
  
We find `x3::raw` very useful because we avoid creating unnecessary containers as much as possible and use range instead. For the complex parsers using `x3::raw` we have to workaround this problem by defining a rule with attribute for it, which is not very elegant (something like `qi::as`, but still).   
  
Can we solve this problem by dumping this placeholder attribute type? Say by adding a template parameter `Iterator` to `attribute_of` and specialize `attribute_of` for `x3::raw`. This placeholder attribute type already brought some customization points and it is far from enough.

---

## Comment 1

> Username: djowel  
> Created at: 2019-05-15 09:38:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492582670  

Could you please post some code that exhibits this behavior?

---

## Comment 2

> Username: wanghan02  
> Created at: 2019-05-15 09:57:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492589172  

@djowel Sure! For example this alternative parser. We can not natually synthesize an attribute of `variant<int, boost::iterator_range<It>>` from it.   
  
```  
    std::string str("test");  
    x3::parse(  
        str.begin(), str.end(),   
        (x3::int_ | x3::raw[*x3::char_])[([](auto const& ctx){  
            boost::apply_visitor(  
                [](auto const& v) { std::cout<<v<<std::endl; },  
                x3::_attr(ctx)  
            );  
        })]  
    );  
```

---

## Comment 3

> Username: djowel  
> Created at: 2019-05-15 10:16:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492595329  

I suppose you have the complete minimal cpp file (MCVE)?

---

## Comment 4

> Username: djowel  
> Created at: 2019-05-15 10:25:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492598003  

At any rate, I am not sure what the intent here is. Do you want to capture each char or the full alternatives? Basically, you attach raw and the SA to the node you need. E.g.:  
  
x3::int_ | x3::raw[*x3::char_][semantic_action] // capture the char  
  
or:  
  
x3::raw[x3::int_ | *x3::char_][semantic_action] // capture the alternatives

---

## Comment 5

> Username: wanghan02  
> Created at: 2019-05-15 10:29:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492599442  

The code could be found here [coliru](https://coliru.stacked-crooked.com/a/35a96ea602605853).

---

## Comment 6

> Username: wanghan02  
> Created at: 2019-05-15 11:14:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492612048  

@djowel I think neither of the 2 solutions are good enough.   
  
Solution#1 of course solves the problem. Actually it solves all the problems but not elegantly. This is why we have attribute type for alternative operator. We want to record this alternative information for later use. The alternative attribute works for all the other parsers but not the raw parser.   
  
Solution#2 in my opinion does not solve the problem. If we want to use this result later we have to parse it again if it is an int or a string.   
  
It's not just the alternative operator. A sequence of raw also cannot synthesize an attribute for example.

---

## Comment 7

> Username: djowel  
> Created at: 2019-05-15 13:04:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492645495  

> @djowel I think neither of the 2 solutions are good enough.  
  
What is your intent? Your code does not convey a clear use-case. What is it that you are trying to do? And what results are you expecting?  
  
> We want to record this alternative information for later use. The alternative attribute works for all the other parsers but not the raw parser.  
  
What information? The int?  
  
I suggest posting a more meaningful use-case.

---

## Comment 8

> Username: wanghan02  
> Created at: 2019-05-15 13:20:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492651396  

The information is **int or string**. Say we have something like this  
  
`(int_ | raw[+char_]) >> some_following_parsers`  
  
We want to use the variant (int or string) only at the end of the whole parser. If we have int, do something. If we have string, do something else. Having a variant attribute is useful in this case.   
  
It's not about this special case. As I said, alternative operator could synthesize an attribute for any other parsers except raw parser. And sequence operator could also synthesize an attribute for any other parsers with the same attribute except raw parser. My intent is to make raw parser as handy as all the other parsers.

---

## Comment 9

> Username: djowel  
> Created at: 2019-05-15 13:31:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492655605  

OK, I think I understand what you mean now.

---

## Comment 10

> Username: djowel  
> Created at: 2019-05-15 14:37:52 UTC  
> Updated at: 2019-05-15 14:40:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492682701  

OK, I have a [fix in develop](https://github.com/boostorg/spirit/commit/fc331f5a4da6e1fe2025e9dbbc5f51d64b3bb558) with a test case:  
  
```c++  
    {  
        using range = boost::iterator_range<std::string::const_iterator>;  
        boost::variant<int, range> attr;  
  
        std::string str("test");  
        parse(str.begin(), str.end(),  (int_ | raw[*char_]), attr);  
  
        auto rng = boost::get<range>(attr);  
        BOOST_TEST(std::string(rng.begin(), rng.end()) == "test");  
    }  
```  
There's a new trait for converting pseudo attributes to the correct type. I recall wanting to do this a long time ago. Now's the chance. I'm sure there are other places that this trait needs to be applied. I'd appreciate your help in providing more test cases. I'll keep this "issue" open.

---

## Comment 11

> Username: djowel  
> Created at: 2019-05-16 01:17:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/509#issuecomment-492879651  

I have more fixes for sequences + tests:  
  
```c++  
    {  
        std::vector<boost::iterator_range<std::string::iterator>> attr;  
        std::string str("123abcd");  
        parse(str.begin(), str.end()  
          , (raw[int_] >> raw[*char_])  
          , attr  
        );  
        BOOST_TEST(attr.size() == 2);  
        BOOST_TEST(std::string(attr[0].begin(), attr[0].end()) == "123");  
        BOOST_TEST(std::string(attr[1].begin(), attr[1].end()) == "abcd");  
    }  
  
    {  
        std::pair<int, boost::iterator_range<std::string::iterator>> attr;  
        std::string str("123abcd");  
        parse(str.begin(), str.end()  
          , (int_ >> raw[*char_])  
          , attr  
        );  
        BOOST_TEST(attr.first == 123);  
        BOOST_TEST(std::string(attr.second.begin(), attr.second.end()) == "abcd");  
    }  
```
