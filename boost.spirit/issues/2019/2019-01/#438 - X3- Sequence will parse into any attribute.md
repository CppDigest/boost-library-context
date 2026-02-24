# #438 - X3: Sequence will parse into any attribute [Closed]

> Username: Kojoley  
> Created at: 2019-01-15 00:16:27 UTC  
> Updated at: 2019-02-09 01:38:36 UTC  
> Closed at: 2019-01-21 11:23:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/438  

```cpp  
#include <boost/spirit/home/x3.hpp>  
  
int main()  
{  
    namespace x3 = boost::spirit::x3;  
    char const* s = "";  
      
    int i;  
    parse(s, s, x3::int_ >> x3::int_, i);  
  
    boost::optional<int> o;  
    parse(s, s, x3::int_ >> x3::int_, o);  
  
    boost::variant<int, long> v;  
    parse(s, s, x3::int_ >> x3::int_, v);  
}  
```  
  
I was expecting this to not compile, but it does.  
  
The code that responsible for this: https://github.com/boostorg/spirit/blob/a36077efbd3b91bc7dbfad211bcf4533aa137159/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L328-L390  
  
Basically it is wrong and/or redundant. I do not know what was original intentions, but what actually it does - pass the attribute to all underlying parsers of a sequence. I tried to remove it and did not find a test case that will fail.

---

## Comment 1

> Username: djowel  
> Created at: 2019-01-15 05:44:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/438#issuecomment-454274116  

Looks wrong indeed. I don't recall the original intentions anymore. As long as the tests pass, I'm OK with the change.

---

## Comment 2

> Username: cppljevans  
> Created at: 2019-01-15 13:22:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/438#issuecomment-454389992  

On 1/14/19 6:16 PM, Nikita Kniazev wrote:  
 > ```cpp  
 > #include <boost/spirit/home/x3.hpp>  
 >  
 > int main()  
 > {  
 >      namespace x3 = boost::spirit::x3;  
 >      char const* s = "";  
 >  
 >      int i;  
 >      parse(s, s, x3::int_ >> x3::int_, i);  
 >  
 >      boost::optional<int> o;  
 >      parse(s, s, x3::int_ >> x3::int_, o);  
 >  
 >      boost::variant<int, long> v;  
 >      parse(s, s, x3::int_ >> x3::int_, v);  
 > }  
 > ```  
 >  
 > I was expecting this to not compile, but it does.  
 >  
[snip]  
  
Strange! The example code posted above is pretty simple;  
hence, I am pretty surprised this problem hasn't been  
discovered before in end-user's use of the library.  But  
maybe that's because the result for all the parses are  
false, and the end-user inferred the reason it was false was  
the wrong attribute type and corrected the attribute type  
and didn't bother to complain to spirit.  
  
 > I do not know what was original intentions,  
  
Joel's reply:  
  
   https://github.com/boostorg/spirit/issues/438#issuecomment-454274116  
  
said "I don't recall the original intentions anymore".  
  
Since not knowing the "original intentions" of code is a  
problem for, you, Nikita in this case, and I've experienced  
similar problems in other cases, I would suggest searching  
for an answer on how to document such original intentions  
somewhere so they are not lost. Maybe this could be done  
with more extensive in-source comments or maybe with  
descriptions in a new doc-programmers_guide directory.  
  
 > but what actually it does - pass the attribute to all  
 > underlying parsers of a sequence. I tried to remove it and  
 > did not find a test case that will fail.  
  
BTW, with your removal, does the compiler show an error with  
the above example code?  
  
-regards,  
Larry

---

## Comment 3

> Username: cppljevans  
> Created at: 2019-01-15 13:33:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/438#issuecomment-454393101  

On 1/15/19 7:22 AM, cppljevans wrote:  
[snip]  
  
> discovered before in end-user's use of the library.  But  
> maybe that's because the result for all the parses are  
> false  
I should have clarified that I modified the original code  
so that the input string was "5" and the first and last  
iterators to parse actually pointed to the begin and end  
of that string.  
[snip]

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-01-15 18:25:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/438#issuecomment-454498320  

@cppljevans The examples above have to fail at *compilation*. They do not meant to run. Here is an example that has to fail at compilation but runs and reports successful parsing:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
  
int main()  
{  
    namespace x3 = boost::spirit::x3;  
    char const* const s = "123,456", * const end = s + std::strlen(s);  
      
    int i;  
    if (parse(s, end, x3::int_ >> ',' >> x3::int_, i))  
        std::cout << "result=" << i << '\n';  
    else  
        std::cout << "fail\n";  
}  
```

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-01-15 18:45:01 UTC  
> Updated at: 2019-01-21 00:08:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/438#issuecomment-454504731  

I am also going to remove support of this if there are no objections (it simplifies fix for the cases above):  
```cpp  
    boost::fusion::vector<> v;  
    parse(s, end, x3::eps >> x3::eps, v);  
```

---

## Comment 6

> Username: Kojoley  
> Created at: 2019-01-15 19:48:02 UTC  
> Updated at: 2019-01-21 00:08:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/438#issuecomment-454526280  

Well, removing https://github.com/boostorg/spirit/blob/a36077efbd3b91bc7dbfad211bcf4533aa137159/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L116-L121 still allows  
```cpp  
    boost::fusion::vector<> v;  
    parse(s, end, x3::eps >> x3::eps, v);  
```  
because it is handled by  
https://github.com/boostorg/spirit/blob/a36077efbd3b91bc7dbfad211bcf4533aa137159/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L222-L241  
so I will just remove the redundant specialization.

---

## Comment 7

> Username: cppljevans  
> Created at: 2019-01-18 10:52:29 UTC  
> Updated at: 2019-01-21 00:08:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/438#issuecomment-455506516  

> Well, removing  
>   
> [spirit/include/boost/spirit/home/x3/operator/detail/sequence.hpp](https://github.com/boostorg/spirit/blob/a36077efbd3b91bc7dbfad211bcf4533aa137159/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L116-L121)  
>   
> Lines 116 to 121 in [a36077e](/boostorg/spirit/commit/a36077efbd3b91bc7dbfad211bcf4533aa137159)  
>  template <typename Parser, typename Attribute, typename Enable = void>   
>  struct pass_sequence_attribute :   
>      mpl::if_<   
>          fusion::result_of::empty<Attribute>   
>        , pass_sequence_attribute_unused   
>        , pass_sequence_attribute_used<Parser, Attribute>>::type {};   
> still allows  
>   
> ```c++  
>     boost::fusion::vector<> v;  
>     parse(s, end, x3::eps >> x3::eps, v);  
> ```  
>   
> because it is handled by  
>   
> [spirit/include/boost/spirit/home/x3/operator/detail/sequence.hpp](https://github.com/boostorg/spirit/blob/a36077efbd3b91bc7dbfad211bcf4533aa137159/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L222-L241)  
>   
> Lines 222 to 241 in [a36077e](/boostorg/spirit/commit/a36077efbd3b91bc7dbfad211bcf4533aa137159)  
>  template <typename L, typename R, typename Attribute, typename Context>   
>  struct partition_attribute<L, R, Attribute, Context,   
>      typename enable_if_c<(!traits::has_attribute<L, Context>::value &&   
>          !traits::has_attribute<R, Context>::value)>::type>   
>  {   
>      typedef unused_type l_part;   
>      typedef unused_type r_part;   
>      typedef pass_sequence_attribute_unused l_pass;   
>      typedef pass_sequence_attribute_unused r_pass;   
>     
>      static unused_type left(Attribute&)   
>      {   
>          return unused;   
>      }   
>     
>      static unused_type right(Attribute&)   
>      {   
>          return unused;   
>      }   
>  };   
>   
> so I will just remove the redundant specialization.  
  
Please clarify.  Which specialization is to be removed and why is it redundant?

---

## Comment 8

> Username: Kojoley  
> Created at: 2019-01-18 11:30:37 UTC  
> Updated at: 2019-01-21 00:08:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/438#issuecomment-455516386  

The answer for your question is in the quoted by you message.

---

## Comment 9

> Username: cppljevans  
> Created at: 2019-01-20 11:20:04 UTC  
> Updated at: 2019-01-21 00:08:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/438#issuecomment-455857635  

> The answer for your question is in the quoted by you message.  
  
  
I should better explain my question:  
  
```  
Which specialization is to be removed and why is it  
redundant?  
```  
  
Which I'll break down in 2 parts:  
  
* (q1) Which specialization is to be removed?  
* (q2) Why is that specialization redundant?  
  
which are explained in the following 2 sections:  
  
# (q1) Which specialization is to be removed?  
  
The [relevant comment](https://github.com/boostorg/spirit/issues/438#issuecomment-454526280) mentions *2* `template classes`:  
  
* (c1) pass_sequence_attribute:  
* (c2) partition_attribute:  
  
and indicates that (c1) is redundant:  
  
> because it is handled by  
  
(c2), and therefore:  
  
> I will just remove the redundant specialization.  
  
The problem I have with that statement is  
[(c1)](https://github.com/boostorg/spirit/blob/a36077efbd3b91bc7dbfad211bcf4533aa137159/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L116-L121) is a *primary* template:  
```c++  
    template <typename Parser, typename Attribute, typename Enable = void>  
    struct pass_sequence_attribute :  
        mpl::if_<  
            fusion::result_of::empty<Attribute>  
          , pass_sequence_attribute_unused  
, pass_sequence_attribute_used<Parser, Attribute>>::type {};  
```  
and **not** a *specialization*; hence, cannot  
be removed without causing compilation failures for the *actual* (c1) specializations  
further down in `detail/sequence.hpp`.  
  
The problem might be that the `relevant comment` has the wrong link to  
(c1) which should be to one showing an actual `pass_sequence_attribute specialization`, [c1'](https://github.com/boostorg/spirit/blob/a6132ae361b5f4d3480d23200ea8d10b8911ff55/include/boost/spirit/home/x3/operator/detail/sequence.hpp#L116-L121):  
  
```cpp  
    template <typename L, typename R, typename Attribute>  
    struct pass_sequence_attribute<sequence<L, R>, Attribute>  
: pass_through_sequence_attribute<Attribute> {};  
```  
  
Could you please confirm that conclusion if right?  
  
Hopefully that conclusion is right; otherwise, I'm still  
confused for reasons outlined above.  
  
# (q2) Why is that specialization redundant?  
  
This section assumes the conclusion from the previous  
section is correct.  IOW, that (c1') is the  
`pass_sequence_attribute specialization` that's the one  
deemed redundant.  
  
The `relevant comment` justifies the removal of (c1')  with:  
  
> still allows  
  
```cpp  
        boost::fusion::vector<> v;  
        parse(s, end, x3::eps >> x3::eps, v);  
```  
  
However, just because (c1') removal still allows compilation  
of that "eps sequence code" does not mean it's redundant,  
because, if that were true, then *both* (c1') *and* (c2) are  
*redundant* because the removal of *both* (c1') and (c2)  
*still* allows compilation of that "eps sequence code",  
according to my tests.  
  
Hence, there must be some other rationale for concluding  
that (c1') is redundant, and I'm asking you to please  
provide that rationale to help me (and other  
potential reviewers) provide a more informed review.

---

## Comment 10

> Username: Kojoley  
> Created at: 2019-01-20 17:17:25 UTC  
> Updated at: 2019-01-21 00:08:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/438#issuecomment-455884487  

> (c1) is a primary template and not a specialization  
  
Look at the `pass_sequence_attribute` base, the specialization is there, in `mpl::if_`. I'll try to help you to see it, the same behavior without using `mpl::if_`:  
  
```cpp  
  
    template <typename Parser, typename Attribute  
      , bool IsEmptySeq = fusion::result_of::empty<Attribute> >  
    struct pass_sequence_attribute_base :  
        pass_sequence_attribute_used<Parser, Attribute> {};  
  
    template <typename Parser, typename Attribute>  
    struct pass_sequence_attribute_base<Parser, Attribute, true> :  
        pass_sequence_attribute_unused {};  
  
    template <typename Parser, typename Attribute, typename Enable = void>  
    struct pass_sequence_attribute :  
        pass_sequence_attribute_base<Parser, Attribute> {};  
```  
  
> Why is that specialization redundant?  
  
It is redundant because it is not used. The `partition_attribute` is disallowed to split the sequence into zero size parts, instead in such case it passes `unused` variable to that branch via `pass_sequence_attribute_unused` directly.
