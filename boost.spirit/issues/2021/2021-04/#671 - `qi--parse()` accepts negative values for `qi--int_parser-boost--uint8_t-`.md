# #671 - `qi::parse()` accepts negative values for `qi::int_parser<boost::uint8_t>` [Closed]

> Username: emmenlau  
> Created at: 2021-04-27 11:27:44 UTC  
> Updated at: 2021-04-28 07:04:26 UTC  
> Closed at: 2021-04-27 15:01:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/671  

Thanks for the very nice and fast number parsers. I found an issue with one of the parsers that hopefully you can clarify:  
```c++  
    uint8_t vResult;  
    typedef boost::spirit::qi::int_parser<boost::uint8_t> aParser;  
  
    std::string::const_iterator vIterator = aString.begin();  
    if (!boost::spirit::qi::parse(vIterator, aString.end(), aParser, vResult)) {  
        throw(std::runtime_error("Could not parse string '" + aString + "'"));  
    }  
```  
For most data types, this will throw the exception if the number is negative. But not for `uint8_t`, where is seems to just accept negative numbers too. Is this a bug or am I doing something wrong?

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-04-27 14:14:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/671#issuecomment-827640664  

That's an interesting question. I read the [documentation](https://www.boost.org/doc/libs/1_76_0/libs/spirit/doc/html/spirit/qi/reference/numeric/int.html#spirit.qi.reference.numeric.int.expression_semantics) like it should fail overflow check and as a result the parsing, but actually I like the current 'by accident' behavior because if you were to not want negative numbers you would be using `uint_parser`.

---

## Comment 2

> Username: emmenlau  
> Created at: 2021-04-27 14:18:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/671#issuecomment-827643551  

> behavior because if you were to not want negative numbers you would be using `uint_parser`.  
  
This is actually something I found too. But I'm under the impression that the `int_parser` is working as a perfectly valid `uint_parser` when the type is unsigned, with the noteworthy exception of `uint8_t`. So at the very least, something seems to be a bit inconsistent, unless I'm misunderstanding something?

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-04-27 14:25:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/671#issuecomment-827648828  

Unsigned integers in C++ are modulo 2n, any behavior could be justified.

---

## Comment 4

> Username: emmenlau  
> Created at: 2021-04-27 14:35:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/671#issuecomment-827656046  

> Unsigned integers in C++ are modulo 2n, any behavior could be justified.  
  
I'm not sure I understand :-)  
  
What I'm saying is, that the `int_parser` is rejecting signed number strings when its instantiated with any unsigned template argument, *except* for `uint8_t`. I'm under the impression that this is inconsistent.

---

## Comment 5

> Username: Kojoley  
> Created at: 2021-04-27 14:45:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/671#issuecomment-827663290  

> What I'm saying is, that the `int_parser` is rejecting signed number strings when its instantiated with any unsigned template argument, _except_ for `uint8_t`. I'm under the impression that this is inconsistent.  
  
I cannot reproduce the inconsistency you are talking about, for me it is consistently parsing negative numbers into a different unsigned types: https://wandbox.org/permlink/I6NbF0eA0TcNsJgq

---

## Comment 6

> Username: emmenlau  
> Created at: 2021-04-27 15:01:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/671#issuecomment-827676272  

I'm very sorry @Kojoley , I think you are right. I experienced this issue with code from another developer and it seems I did not isolate the issue well enough. The developer commented about this (above mentioned) problem with boost 1.66, and he implemented a workaround, and when testing the code with boost 1.72, I did not clear the code of all parts of the workaround.  
  
The behavior *is* consistent, and everything works well!

---

## Comment 7

> Username: djowel  
> Created at: 2021-04-28 00:29:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/671#issuecomment-828050505  

> That's an interesting question. I read the [documentation](https://www.boost.org/doc/libs/1_76_0/libs/spirit/doc/html/spirit/qi/reference/numeric/int.html#spirit.qi.reference.numeric.int.expression_semantics) like it should fail overflow check and as a result the parsing, but actually I like the current 'by accident' behavior because if you were to not want negative numbers you would be using `uint_parser`.  
  
IIRC, this is not 'by accident'. I recall there's a test for this, if my memory serves me right. I can't trust my memory, though. In any case, this should be documented.

---

## Comment 8

> Username: Kojoley  
> Created at: 2021-04-28 00:34:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/671#issuecomment-828052141  

> IIRC, this is not 'by accident'.  
  
I am pretty sure it is. Enabled by a short-cut https://github.com/boostorg/spirit/blob/e30073e5bebb8102c73d1ad3588bf3f68cec2b64/include/boost/spirit/home/qi/numeric/detail/numeric_utils.hpp#L211-L213  
  
> I recall there's a test for this,  
  
I did not find any.

---

## Comment 9

> Username: djowel  
> Created at: 2021-04-28 00:54:23 UTC  
> Updated at: 2021-04-28 00:55:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/671#issuecomment-828059865  

> > IIRC, this is not 'by accident'.  
>   
> I am pretty sure it is.  
  
OK, well, happy accident or not, I agree that this is the correct behavior. It makes sense to add a test if there is none, plus update the docs. Would you be interested to contribute that, @emmenlau ?

---

## Comment 10

> Username: emmenlau  
> Created at: 2021-04-28 07:04:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/671#issuecomment-828202276  

> OK, well, happy accident or not, I agree that this is the correct behavior. It makes sense to add a test if there is none, plus update the docs. Would you be interested to contribute that, @emmenlau ?  
  
In principle yes! But not any time soon, I'm already active in some OS projects that eat my spare time :)
