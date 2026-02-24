# #158 - BOOST_FUSION_ADAPT_ADT `not enough actual parameters for macro` [Open]

> Username: Kojoley  
> Created at: 2017-11-13 15:30:19 UTC  
> Updated at: 2017-11-15 20:04:16 UTC  
> Url: https://github.com/boostorg/fusion/issues/158  

On MSVC if one wants to leave the setter/getter empty, e.g.:  
```cpp  
BOOST_FUSION_ADAPT_ADT(  
    data4,  
    (boost::optional<int>, boost::optional<int> const&, obj.a(), /**/)  
    (boost::optional<double>, boost::optional<double> const&, obj.b(), /**/)  
    (boost::optional<std::string>, boost::optional<std::string> const&, obj.c(), /**/)  
);  
```  
will get a warning message:  
```  
..\libs\spirit\test\karma\regression_adapt_adt.cpp(110): warning C4003: not enough actual parameters for macro 'BOOST_PP_SEQ_SIZE_3'  
..\libs\spirit\test\karma\regression_adapt_adt.cpp(110): warning C4003: not enough actual parameters for macro 'BOOST_PP_SEQ_ELEM_0'  
```  
Is it safe to ignore it?  
Are there any methods except disabling the warning to fix this? MSVC will compile fine and without the warning if you replace `/**/` with `()` but it does not work for GCC and Clang,

---

## Comment 1

> Username: daminetreg  
> Created at: 2017-11-14 09:40:34 UTC  
> Url: https://github.com/boostorg/fusion/issues/158#issuecomment-344200264  

The API of [BOOST_FUSION_ADAPT_ADT](http://www.boost.org/doc/libs/1_65_1/libs/fusion/doc/html/fusion/adapted/adapt_adt.html) doesn't foresee this use, nor the unit tests do.  
  
Because your snippet *lies*, it tells that it declare a [Random Access Sequence](http://www.boost.org/doc/libs/1_65_1/libs/fusion/doc/html/fusion/sequence/concepts/random_access_sequence.html), but yours cannot have the sequence elements modified.  
  
But naturally this is your concern inside your app :open_hands:. Because if you don't do any action modifying the elements you are not going to have any issue. It might happen when someone else will overtake your code without knowing you did that. :smile:  
  
But to your very specific question emptiness in parameters is not portable across compilers (sadly). Therefore you can avoid the warning and surprises in the future if you just provide any C++ expression doing nothing. You can use `val` instead of `/**/`. In the end the expression will be pasted into a setter function which will do only : `val;`.  
  
Hope this helps.

---

## Comment 2

> Username: Kojoley  
> Created at: 2017-11-14 23:19:43 UTC  
> Url: https://github.com/boostorg/fusion/issues/158#issuecomment-344433070  

> The API of BOOST_FUSION_ADAPT_ADT doesn't foresee this use, nor the unit tests do.  
  
But Spirit's [do](https://github.com/boostorg/spirit/blob/9a8cded55355644c749d6511504b59c048d64de3/test/karma/regression_adapt_adt.cpp#L105-L110) and demonstrates it in an [example](http://www.boost.org/doc/libs/1_65_0/libs/spirit/doc/html/spirit/karma/tutorials/karma_adapted_complex.html).  
  
> You can use `val` instead of `/**/`. In the end the expression will be pasted into a setter function which will do only : `val;`.  
  
Sadly this will confuse people looking at the code of the example.  
  
Thanks for your answer.

---

## Comment 3

> Username: daminetreg  
> Created at: 2017-11-15 08:03:36 UTC  
> Url: https://github.com/boostorg/fusion/issues/158#issuecomment-344515121  

Oups 🙊.  
  
I could add a check to BOOST_PP_IS_EMPTY to handle this case. I'll give it a try today.

---

## Comment 4

> Username: Kojoley  
> Created at: 2017-11-15 20:04:16 UTC  
> Url: https://github.com/boostorg/fusion/issues/158#issuecomment-344712007  

Huh, fusion have test with omitted arguments too https://github.com/boostorg/fusion/blob/f7b90f29817563bf1f3bc6ded860076f8085ba92/test/sequence/adapt_struct.cpp#L158  
  
And produces bunch of warnings http://www.boost.org/development/tests/develop/output/teeks99-09-p-win2012R2-64on64-fusion-msvc-14-1-warnings.html#adapt_adt_named  
  
> I could add a check to BOOST_PP_IS_EMPTY to handle this case.  
  
I suspect it was here but had been removed in #124
