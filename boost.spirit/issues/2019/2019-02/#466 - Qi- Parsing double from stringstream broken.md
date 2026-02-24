# #466 - Qi: Parsing double from stringstream  broken? [Closed]

> Username: cbielow  
> Created at: 2019-02-18 09:39:03 UTC  
> Updated at: 2019-02-19 12:50:35 UTC  
> Closed at: 2019-02-18 15:19:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/466  

Dear Boost-Team,  
  
when parsing a double from a stringstream, the resulting double is incorrect when followed by a space and more numbers.  
Minimal example:  
```  
#include <iostream>  
#include <sstream>  
#include <boost/spirit/include/qi.hpp>  
  
int main() {  
    double target(0);  
    std::stringstream ss("-5.0  9.0");  
    boost::spirit::istream_iterator it_start(ss), it_end;  
      
    std::cout << (boost::spirit::qi::parse(it_start, it_end, boost::spirit::qi::double_, target) ? "success " : "failure");  
  
    std::cout << "at pos: " << ss.tellg() << ", next char: " << char(ss.peek()) << "\n"   
              << "Result: " << target;  
      
  return 0;  
}  
```  
  
This yields  
```  
success at pos: 8, next char: 0  
Result: -5.09  
```  
  
expected would be `-5.0`.  
When using a std::string instead of stringstream the code correctly prints `-5.0`.  
I can live with the fact that the stream is advanced more than it should (is peek() not sufficient in this case after spaces have been encountered) since its an input iterator, but why is the result wrong?  
  
Tested with Boost 1.55 and 1.69 on Wandbox.org, e.g. `$ g++ prog.cc -Wall -Wextra -O2 -march=native -I/opt/wandbox/boost-1.55.0/gcc-6.2.0/include -std=c++11`

---

## Comment 1

> Username: cbielow  
> Created at: 2019-02-18 10:27:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464675857  

is there a way to fix this behaviour with current versions of boost?

---

## Comment 2

> Username: djowel  
> Created at: 2019-02-18 12:53:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464721523  

I would advice against using spirit::istream_iterator. I personally don't use it. Spirit requires Forward iterators, at least, and istream_iterator makes use of the multipass iterator to give the **illusion** of a Forward iterator from Input iterators, at the expense of some (expensive!) buffering but with some quirks(!). In your specific use-case, I'd just use the string in the stringstream as input (ss.str()).   
  
Here's a related stack-overflow question:  
  
https://stackoverflow.com/questions/35256521/boost-spirit-istream-iterator-consumes-too-much-from-stream  
  
There are only very specific cases where you can reliably use a multipass (e.g. with ASTs that allow full back-tracking; Qi attributes don't).  sehe's answer might offer some hints.

---

## Comment 3

> Username: djowel  
> Created at: 2019-02-18 13:03:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464724254  

> There are only very specific cases where you can reliably use a multipass (e.g. with ASTs that allow full back-tracking; Qi attributes don't). sehe's answer might offer some hints.  
  
Update: you are not doing any backtracking, so it might be that istream_iterator is indeed broken. At any rate, my suggestion still stands: avoid using spirit::istream_iterator. It is expensive and quirky.

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-02-18 13:37:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464734175  

The problem is that stream `operator>>` skips the whitespace by its own https://wandbox.org/permlink/nJs3Ee1JCHnVlKFt. You can disable this behavior with `ss >> std::noskipws` https://wandbox.org/permlink/P3I440qT1uigEveA.  
  
We can fix it by utilizing `get` method, but it is a breaking change actually.

---

## Comment 5

> Username: cbielow  
> Created at: 2019-02-18 13:41:32 UTC  
> Updated at: 2019-02-18 13:42:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464735389  

nice timing. Just found that as well by looking at the first example :)  
I can set the noskipws flag on the stream manually, thats no problem (and restore to previous state afterwards).  
  
Final observation: ss.tellg() seems to always point to the 2nd (!) char after the double actually ends. Is this something I can rely on? (I cannot reuse the spirit::istream_iterator without a lot of hassle).

---

## Comment 6

> Username: Kojoley  
> Created at: 2019-02-18 13:45:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464736572  

> Final observation: ss.tellg() seems to always point to the 2nd (!) char after the double actually ends. Is this something I can rely on? (I cannot reuse the spirit::istream_iterator without a lot of hassle).  
  
In general - no, but if you do not use skippers and lookahead you will observe this behavior.

---

## Comment 7

> Username: cbielow  
> Created at: 2019-02-18 13:46:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464737111  

ok, nice. I only need this guarantee for  
`boost::spirit::qi::parse(it_start, it_end, boost::spirit::qi::double_, target);`  
  
So I should be good?

---

## Comment 8

> Username: djowel  
> Created at: 2019-02-18 13:55:56 UTC  
> Updated at: 2019-02-18 13:57:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464740028  

> nice timing. Just found that as well by looking at the first example :)  
> I can set the noskipws flag on the stream manually, thats no problem (and restore to previous state afterwards).  
>   
> Final observation: ss.tellg() seems to always point to the 2nd (!) char after the double actually ends. Is this something I can rely on? (I cannot reuse the spirit::istream_iterator without a lot of hassle).  
  
Ah! I incorrectly assumed you disabled std::ios::skipws. I should read more carefully. That is a must when using istreams when parsing.  
  
Anyway, out of curiosity,  do you really have to use an istream_iterator? Why?

---

## Comment 9

> Username: Kojoley  
> Created at: 2019-02-18 14:07:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464743746  

> ok, nice. I only need this guarantee for  
`boost::spirit::qi::parse(it_start, it_end, boost::spirit::qi::double_, target);`  
>   
> So I should be good?  
  
It is an implementation detail, for example if we in future will `putback`/`unget` the last eaten character in `spirit::istream_iterator` destructor you will get in trouble, but in reality this might work for a long time or even forever.

---

## Comment 10

> Username: djowel  
> Created at: 2019-02-18 14:07:25 UTC  
> Updated at: 2019-02-18 14:10:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464743788  

> ok, nice. I only need this guarantee for  
> `boost::spirit::qi::parse(it_start, it_end, boost::spirit::qi::double_, target);`  
>   
> So I should be good?  
  
Actually, yes, it is documented behavior:  
  
https://www.boost.org/doc/libs/1_69_0/libs/spirit/doc/html/spirit/qi/reference/parser_concepts/parser.html  
  
"On a successful match, f is positioned one past the last matching character/token."  
  
But there is also the post-skip behavior, but only for phrase_parse to be aware of:   
  
https://www.boost.org/doc/libs/1_58_0/libs/spirit/doc/html/spirit/qi/reference/parse_api/iterator_api.html  
  
(Only for phrase_parse, so you should be good!)  
  
**EDIT: This is only true for TRUE forward iterators!**

---

## Comment 11

> Username: djowel  
> Created at: 2019-02-18 14:09:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464744562  

> It is an implementation detail, for example if we in future will `putback`/`unget` the last eaten character in `spirit::istream_iterator` destructor you will get in trouble, but in reality this might work for a long time or even forever.  
  
Gah, right again @Kojoley. All the more reason to avoid  istream_iterator!

---

## Comment 12

> Username: cbielow  
> Created at: 2019-02-18 15:19:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-464771142  

> > ok, nice. I only need this guarantee for  
> > `boost::spirit::qi::parse(it_start, it_end, boost::spirit::qi::double_, target);`  
> > So I should be good?  
>   
> Actually, yes, it is documented behavior:  
>   
> https://www.boost.org/doc/libs/1_69_0/libs/spirit/doc/html/spirit/qi/reference/parser_concepts/parser.html  
>   
> "On a successful match, f is positioned one past the last matching character/token."  
> **EDIT: This is only true for TRUE forward iterators!**  
  
Ok, so inspecting the state of `istream_iterator` (i.e. how far it advanced), should be even safer.  
However, when looking at multi_pass and the overhead it produces (shared_ptr, i.e. atomic locks?), I might go with std::string after all.  
  
Thanks again to everybody for the input. Much appreciated!

---

## Comment 13

> Username: cbielow  
> Created at: 2019-02-19 12:50:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/466#issuecomment-465116281  

just as closing remark:  
I benchmarked the `stringstream+boost::istream_iterator` vs. `string::iterator` versions and the stream version is about 5-10x slower (depending on the precision/length of the double etc).  
Switched to string::iterator now, even though it required some changes to the code, but definitely cleaner and faster this way.
