# #153 reenabled support for BOOST_FUSION_ADAPT_ADT adapted structs [Merged]

> Username: tobias-loew  
> Created at: 2015-08-31 08:28:38 UTC  
> Updated at: 2018-09-10 14:30:49 UTC  
> Merged at: 2015-08-31 08:35:21 UTC  
> Closed at: 2015-08-31 08:35:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/153  

since commit 69cc5370381e48352dc01c708599e9b49b7460f0 (hkaiser hkaiser  
authored on 7 Nov 2009) spirit support BOOST_FUSION_ADAPT_ADT adapted  
structs was broken

---

## Comment 1

> Username: djowel  
> Created_at: 2015-08-31 08:34:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-136301559  

Great you found a fix!

---

## Comment 2

> Username: tobias-loew  
> Created_at: 2015-08-31 09:02:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-136307281  

No problem - I'm happy to help such a great project.

---

## Comment 3

> Username: sehe  
> Created_at: 2015-11-01 23:57:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-152879056  

It seems there are still some loose ends with this. While answering http://stackoverflow.com/a/33468657/85371 I found that ADAPT_ADT is till practically broken on 1.59/develop with the permutation parser.  
  
In the below testcase  
  
``` c++  
        ("a=" >> qi::char_ >> ';')  
    >> ("b=" >> qi::char_ >> ';')  
```  
  
compiles, but  
  
``` c++  
("a=" >> qi::char_ >> ';')  
^ ("b=" >> qi::char_ >> ';')  
```  
  
does not.  
  
Full sample: **<kbd>[Live On Coliru](http://coliru.stacked-crooked.com/a/e1324cad918fc3d7)</kbd>**  
  
``` c++  
#include <boost/spirit/include/qi.hpp>  
#include <boost/fusion/adapted.hpp>  
#include <iostream>  
  
class Foo {  
    public:  
    char const& getA() const { return a; }   
    char const& getB() const { return b; }   
  
    void setA(char value)    { a = value; }   
    void setB(char value)    { b = value; }   
    private:  
    char a, b;  
};  
  
BOOST_FUSION_ADAPT_ADT(  
        Foo,  
        (char const&, char const&, obj.getA(), obj.setA(val))  
        (char const&, char const&, obj.getB(), obj.setB(val))  
    )  
  
  
int main() {  
    namespace qi = boost::spirit::qi;  
  
    boost::spirit::istream_iterator f(std::cin), l; // input e.g. "a=a;b=b;"  
    Foo foo;  
  
    bool r = qi::parse(f, l,  
#if 1 // FAILS TO COMPILE  
                        ("a=" >> qi::char_ >> ';')  
                    ^ ("b=" >> qi::char_ >> ';')  
#else // COMPILES WITH FIX FROM PR #153  
                        ("a=" >> qi::char_ >> ';')  
                    >> ("b=" >> qi::char_ >> ';')  
#endif   
                , foo);  
  
    if (r)  
        std::cout << "Parsed: " << boost::fusion::as_vector(foo) << "\n";  
    else  
        std::cerr << "Parse failed\n";  
}  
```

---

## Comment 4

> Username: daminetreg  
> Created_at: 2015-11-03 13:44:01 UTC  
> Updated_at: 2015-11-03 13:44:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-153356175  

A bit off-topic : I think the problem of ADAPT_ADT is that the proxy object it uses for each member is not transparent enough, I've this on my list of possible improvement in fusion, because I believe if we could make adapted ADTs equal to adapted structs  we could remove the code specific for ADAPT_ADT in spirit and there wouldn't be any differences anymore.

---

## Comment 5

> Username: sehe  
> Created_at: 2015-11-03 14:06:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-153365749  

Sounds like a plan. For the short term, it's broken though.   
  
Do have a look at Niebler's articles on "perfect proxies" or similar. He seems to have really thought about the dos and don'ts as well as new possibilities in c++14

---

## Comment 6

> Username: daminetreg  
> Created_at: 2015-11-03 14:17:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-153368342  

Thanks for the tips, Eric Niebler is awesome :smile: , but as you say it's no short term solution.

---

## Comment 7

> Username: Kojoley  
> Created_at: 2018-03-14 15:57:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-373073983  

@sehe the bug should be fixed in #376.

---

## Comment 8

> Username: Superlokkus  
> Created_at: 2018-09-07 14:58:52 UTC  
> Updated_at: 2018-09-07 16:00:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-419466725  

@Kojoley Seems it's not fixed, see my other example https://stackoverflow.com/questions/52225089/boost-fusion-adapt-adt-explodes-when-using-with-boostkarma . I used boost 1.69 (todays master branch)  
Update: Works fine with BOOST_FUSION_ADAPT_STRUCT, maybe also BOOST_FUSION_ADAPT_ADT, but the later ones still seems to have some trouble on my machine, but could be artifacts. So considers this as:  
**No problem at the moment/disregard**

---

## Review 9 [Commented]

> Username: Kojoley  
> Created_at: 2018-09-07 17:26:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/153#pullrequestreview-153433919  

**Update**: Never mind, I forgot that `transform_attribute` is for `rule`s/`attr_cast`s.

---

## Comment 10

> Username: tobias-loew  
> Created_at: 2018-09-09 11:17:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-419708834  

Hi, the reason for the initial PR was that adapted structs like:  
  
    BOOST_FUSION_ADAPT_ADT(  
    boost::uuids::uuid,  
    (obj.data[0],  obj.data[0]  = val )  
    (obj.data[1],  obj.data[1]  = val )  
    (obj.data[2],  obj.data[2]  = val )  
    (obj.data[3],  obj.data[3]  = val )  
    (obj.data[4],  obj.data[4]  = val )  
    (obj.data[5],  obj.data[5]  = val )  
    (obj.data[6],  obj.data[6]  = val )  
    (obj.data[7],  obj.data[7]  = val )  
    (obj.data[8],  obj.data[8]  = val )  
    (obj.data[9],  obj.data[9]  = val )  
    (obj.data[10], obj.data[10] = val )  
    (obj.data[11], obj.data[11] = val )  
    (obj.data[12], obj.data[12] = val )  
    (obj.data[13], obj.data[13] = val )  
    (obj.data[14], obj.data[14] = val )  
    (obj.data[15], obj.data[15] = val )  
    )  
  
could not be used in spirit as they result in compile errors in assign_to.hpp line 153 (version 1.68 with my changes from this PR commented out), when using the following code for parsing a uuid inside a grammar:  
  
        qi::uint_parser<uint8_t, 16, 2, 2> hx;  
        uuid %= hx >> hx >> hx >> hx >> lit('-') >> hx >> hx >> lit('-') >> hx >> hx >> lit('-') >> hx >> hx >> lit('-') >> hx >> hx >> hx >> hx >> hx >> hx;  
        qi::rule<Iterator, boost::uuids::uuid(), skipper<Iterator>> uuid;  
  
Tobias

---

## Comment 11

> Username: tobias-loew  
> Created_at: 2018-09-10 06:03:55 UTC  
> Updated_at: 2018-09-10 06:04:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-419797493  

@sehe I tried your example http://coliru.stacked-crooked.com/a/e1324cad918fc3d7 on VS 2015 (Update 3) and VS 2017 (Update 8.2) with Boost 1.68 and both versions (sequence and permutation parser) compile and work as expected.

---

## Comment 12

> Username: Superlokkus  
> Created_at: 2018-09-10 14:24:11 UTC  
> Updated_at: 2018-09-10 14:24:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-419931436  

@Kojoley Did you see https://wandbox.org/permlink/w7RLrUcoK8SJSXcn / my comment that `BOOST_FUSION_ADAPT_ADT` still fails, when you actually instantiate the boost qi grammar?

---

## Comment 13

> Username: Kojoley  
> Created_at: 2018-09-10 14:28:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-419932968  

@Superlokkus yes, see #396.

---

## Comment 14

> Username: Superlokkus  
> Created_at: 2018-09-10 14:30:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/153#issuecomment-419933960  

Thank you very much guys/gals, keep up the good work! (Hope I will be able to contribute somewhere in the near future)

---
