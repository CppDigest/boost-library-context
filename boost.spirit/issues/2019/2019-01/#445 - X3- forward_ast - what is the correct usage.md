# #445 - X3: forward_ast - what is the correct usage [Closed]

> Username: Xeverous  
> Created at: 2019-01-31 09:27:46 UTC  
> Updated at: 2019-02-09 01:33:06 UTC  
> Closed at: 2019-01-31 14:36:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/445  

There is pretty much nothing regarding `x3::forward_ast` in the documentation. The only example is rexpr, which uses it as one of the variant alternatives. I'm not sure if it's intended to be used anywhere else - I have a recursive grammar and I don't know whether something like this is valid:  
  
    struct block : x3::position_tagged  
    {  
        std::string name;  
        x3::foward_ast<struct block_list> child_blocks;  
    };  
  
    struct block_list : x3::position_tagged  
    {  
        std::vector<block> blocks;  
    };  
  
Currently I'm running into compilation errors which result from different types than expected:  
  
    [...] boost/spirit/home/x3/support/traits/move_to.hpp:68:18: error: no match for 'operator=' (operand types are 'boost::spirit::x3::forward_ast<ast::block_list>' and 'std::remove_reference<std::vector<ast::block>&>::type' {aka 'std::vector<ast::block>'})  
             dest = std::move(fusion::front(src));  
             ~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
- Is `forward_ast` intended to be used anywhere else than variant alternatives?  
- If not, how should I deal with recursive grammars that bind wrongly attributes like in the error above? Should I adapt it differently (eg `child_blocks.get()` instead of `child_blocks`)?

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-01-31 13:39:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459347145  

> The only example is rexpr  
  
There also bunch of calc examples that use it.  
  
> Is `forward_ast` intended to be used anywhere else than variant alternatives?  
  
IIUC `x3::forward_ast` is the same thing for `x3::variant` as [`boost::recursive_wrapper`](https://www.boost.org/doc/libs/1_69_0/doc/html/boost/recursive_wrapper.html) for `boost::variant`. I do not know what were the motives behind `x3::variant`, I hope @djowel will shed light upon it.  
  
> If not, how should I deal with recursive grammars that bind wrongly attributes like in the error above? Should I adapt it differently (eg `child_blocks.get()` instead of `child_blocks`)?  
  
I do not understand what you meant, you did not provide a grammar (you shown only data structures), please provide an [MWE](https://stackoverflow.com/help/mcve). From the `block_list` definition I see that you have a single element sequence and it is a usual pitfall (see #408, https://stackoverflow.com/questions/54013284/position-annotation-for-sequences-in-spirit-x3), I partly 'fixed' (added workarounds for) single element sequences, but now I regret I done it.

---

## Comment 2

> Username: djowel  
> Created at: 2019-01-31 14:05:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459355870  

Here: https://sourceforge.net/p/spirit/mailman/message/34869892/  
  
Quoting myself:  
  
There are performance advantages with x3::variant. Boost variant has this  
odd (IMO) requirement that prevents it to move recursive variants  
cheaply. There was an ensuing debate over that some years ago. It  
boils down to the never-empty guarantee. The way around that is to  
have some kind of nullable object that I use in x3 to make move a  
cheap pointer swap.  
  
Another advantage is that you have a unique type everytime. An xr variant  
is a user struct/class that you can use for overloading and type detection.  
A variant, OTOH is defined by its structure. Hence, for example, variant<float, int>  
does not say about its type, but only its structure. If you have a typedef  
my_type and his_type both having the same variant<float, int>, they are not  
unique types.  
  
And indeed:  
  
"I just replaced all x3::variant/forward_ast by  
boost::variant/recursive_wrapper and it is not good... The new parser is  
70% slower than the x3::variant one :("  
  
Nikita, feel free to add that comment in the docs or the code itself.

---

## Comment 3

> Username: Xeverous  
> Created at: 2019-01-31 14:36:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459366463  

After a bit of investigating I realized that `x3::forward_ast` is not really needed and Spirit can handle grammars that have ASTs like this:  
  
    struct block : x3::position_tagged  
    {  
        std::string name;  
        std::vector<block> child_blocks;  
    };  
  
They just have to have proper grammars to prevent infinite left-recursion with no input consumption.  
  
My pitfall is that my code currently workarounds 3 different spirit bugs (especially "sequence in variant parser with container attribute" and type copying) and also has probably my own grammar bugs which results in much harder tracking when something goes wrong.  
  
Regarding 1-element sequences: I have already realized that Spirit prefers when the number of elements between `>`/`>>` matches the number of members in a fusion sequence. Making grammars as small as possible makes it easier for me to extend any part later and make sure that all attributes are filled correctly. So If I understand you correctly, 1-element sequences are not a problem as long as their grammar does not use `>` or `>>`.

---

## Comment 4

> Username: cppljevans  
> Created at: 2019-01-31 14:36:40 UTC  
> Updated at: 2019-01-31 15:18:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459366704  

> Here: https://sourceforge.net/p/spirit/mailman/message/34869892/  
>   
> Quoting myself:  
>   
> There are performance advantages with x3::variant. Boost variant has this  
> odd (IMO) requirement that prevents it to move recursive variants  
> cheaply. There was an ensuing debate over that some years ago. It  
> boils down to the never-empty guarantee. The way around that is to  
> have some kind of nullable object that I use in x3 to make move a  
> cheap pointer swap.  
>   
> Another advantage is that you have a unique type everytime. An xr variant  
  
Should xr be x3 instead?  Seems likely since r is close to 3 on keyboard  
suggesting a typo is likely here ;)  
  
> is a user struct/class that you can use for overloading and type detection.  
> A variant, OTOH is defined by its structure. Hence, for example, variant<float, int>  
> does not say about its type, but only its structure. If you have a typedef  
> my_type and his_type both having the same variant<float, int>, they are not  
> unique types.  
  
I assume you mean boost::variant<float,int>.   But I don't understand  
how x3::variant<float,int> is any more "unique" than boost::variant<float,int>.  
Could you please explain a bit more?  
  
>   
> And indeed:  
>   
> "I just replaced all x3::variant/forward_ast by  
> boost::variant/recursive_wrapper and it is not good... The new parser is  
> 70% slower than the x3::variant one :("  
>   
> Nikita, feel free to add that comment in the docs or the code itself.  
  
Yes, yes!  Please!  Maybe even a doc similar to the one for valu_init.htm  
which Nikita mentioned in another recent comment.

---

## Comment 5

> Username: djowel  
> Created at: 2019-01-31 14:46:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459370248  

> I assume you mean boost::variant<float,int>. But I don't understand  
> how x3::variant<float,int> is any more "unique" than boost::variant<float,int>.  
> Could you please explain a bit more?  
>   
> > And indeed:  
> > "I just replaced all x3::variant/forward_ast by  
> > boost::variant/recursive_wrapper and it is not good... The new parser is  
> > 70% slower than the x3::variant one :("  
> > Nikita, feel free to add that comment in the docs or the code itself.  
> > Yes, yes!  Please!  Maybe even a doc similar to the one for valu_init.htm  
> > which Nikita mentioned in another recent comment.  
  
As you can see in the examples/use-cases, the x3 variant is meant to be subclassed. So you write something like:  
  
struct my_ast : x3::variant<...>  ... { ... };

---

## Comment 6

> Username: Kojoley  
> Created at: 2019-01-31 14:52:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459372558  

> There are performance advantages with x3::variant.  
  
Is there a benchmark (I cannot find it in the linked thread)?   
  
> Boost variant has this  
> odd (IMO) requirement that prevents it to move recursive variants  
> cheaply. There was an ensuing debate over that some years ago. It  
> boils down to the never-empty guarantee. The way around that is to  
> have some kind of nullable object that I use in x3 to make move a  
> cheap pointer swap.  
  
This is about copying/moving the non-recursive_wrapper objects (and not the case if the type has noexcept copy/move constructor https://www.boost.org/doc/libs/1_69_0/doc/html/variant/design.html#variant.design.never-empty.optimizations). The `recursive_wrapper` objects are heap allocated, so if `boost::variant` does not do a simple pointer stealing for `recursive_wrapper` on move it should be a bug.  
  
> struct my_ast : x3::variant<...> ... { ... };  
  
I do not get the point, the same thing can be done with `boost::variant`.

---

## Comment 7

> Username: djowel  
> Created at: 2019-01-31 14:55:50 UTC  
> Updated at: 2019-01-31 14:56:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459373908  

> Is there a benchmark (I cannot find it in the linked thread)?  
  
No there isn't a formal benchmark.   
  
> This is about copying/moving the non-recursive_wrapper objects (and not the case if the type has noexcept copy/move constructor https://www.boost.org/doc/libs/1_69_0/doc/html/variant/design.html#variant.design.never-empty.optimizations). The `recursive_wrapper` objects are heap allocated, so if `boost::variant` does not do a simple pointer stealing for `recursive_wrapper` on move it should be a bug.  
>   
> > struct my_ast : x3::variant<...> ... { ... };  
>   
> I do not get the point, the same thing can be done with `boost::variant`.  
  
Yes you can.

---

## Comment 8

> Username: cppljevans  
> Created at: 2019-01-31 15:21:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459383261  

> > Is there a benchmark (I cannot find it in the linked thread)?  
>   
> No there isn't a formal benchmark.  
>   
Should there be one?  
> > This is about copying/moving the non-recursive_wrapper objects (and not the case if the type has noexcept copy/move constructor https://www.boost.org/doc/libs/1_69_0/doc/html/variant/design.html#variant.design.never-empty.optimizations). The `recursive_wrapper` objects are heap allocated, so if `boost::variant` does not do a simple pointer stealing for `recursive_wrapper` on move it should be a bug.  
> > > struct my_ast : x3::variant<...> ... { ... };  
> >   
> >   
> > I do not get the point, the same thing can be done with `boost::variant`.  
>   
> Yes you can.  
  
What about the possible bug Nikita mentioned?

---

## Comment 9

> Username: djowel  
> Created at: 2019-01-31 15:29:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459386622  

> What about the possible bug Nikita mentioned?  
  
What about it? There's a long underlying story behind this in the Boost list. Variant does not do a simple pointer stealing on move. I'm not sure if I have the time to dig it up. maybe search never-empty guarantee + variant + move.

---

## Comment 10

> Username: Kojoley  
> Created at: 2019-01-31 16:03:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459399851  

> Variant does not do a simple pointer stealing on move.  
  
At least if both variants have the same recursive_wrapper type it will  
https://github.com/boostorg/variant/blob/d069511e31a3b7fee45e22634bb43b8f4e4608cd/include/boost/variant/recursive_wrapper.hpp#L79-L83.

---

## Comment 11

> Username: djowel  
> Created at: 2019-01-31 23:54:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459552456  

OK, I did some search. This is (I think) the discussion:  
  
http://boost.2283326.n4.nabble.com/variant-Basic-rvalue-and-C-11-features-support-td4637894i20.html#a4641141  
  
and here:  
  
">> A recursive_wrapper is not a pointer. It's a value-like wrapper   
>> that is assumed to always contain a valid object. The move   
>> constructor should leave the moved-from recursive_wrapper   
>> in a valid state, which precludes nullifying it.   
"

---

## Comment 12

> Username: djowel  
> Created at: 2019-01-31 23:58:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459553458  

I think this is a better view of the complete thread:  
  
http://boost.2283326.n4.nabble.com/variant-Basic-rvalue-and-C-11-features-support-tt4637894.html#none

---

## Comment 13

> Username: djowel  
> Created at: 2019-02-01 00:01:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459554066  

Hopefully, these threads will give a better idea of the necessity of x3::variant facility.

---

## Comment 14

> Username: Kojoley  
> Created at: 2019-02-01 00:11:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459556388  

Thanks, I will read the linked thread.  
From what I see that's not true because you can not make any assumptions on an object state after it was moved out.  
Also I already filed a PR https://github.com/boostorg/variant/pull/59 so if I am wrong I hope to see an explanation.

---

## Comment 15

> Username: djowel  
> Created at: 2019-02-01 00:14:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459557021  

> Thanks, I will read the linked thread.  
> From what I see that's not true because you can not make any assumptions on an object state after it was moved out.  
  
What is not true?  
  
> Also I already filed a PR [boostorg/variant#59](https://github.com/boostorg/variant/pull/59) so if I am wrong I hope to see an explanation.  
  
I think you will get the same explanation as in the thread which already covers a lot of ground on the subject.

---

## Comment 16

> Username: Kojoley  
> Created at: 2019-02-01 00:18:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459557952  

> What is not true?  
  
This statement:  
  
> precludes nullifying it  
  
By nullifying you leaving an object in a valid state, as `delete` will no do anything for null-pointer it.

---

## Comment 17

> Username: djowel  
> Created at: 2019-02-01 00:19:46 UTC  
> Updated at: 2019-02-01 00:19:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459558267  

> > What is not true?  
>   
> This statement:  
>   
> > precludes nullifying it  
>   
> By nullifying you leaving an object in a valid state, as `delete` will no do anything for null-pointer it.  
  
Ah and we're on the same boat :-) Alas, we lost in 2013 already.

---

## Comment 18

> Username: djowel  
> Created at: 2019-02-01 00:22:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459558806  

So the solution with x3 variant is to allow empty (relax the never empty guarantee).

---

## Comment 19

> Username: Kojoley  
> Created at: 2019-02-01 00:25:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459559538  

IIUC the bad thing could happen if variant use backup storage for doing this move, but it marked noexcept so no backup storage is used. Also there should be a test in variant for this case after doing that change all tests passed so... if there is no test case that broken and no one can show a counterexample I think that PR should be merged.

---

## Comment 20

> Username: Kojoley  
> Created at: 2019-02-01 00:32:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459560803  

I am reading the thread and see that the worrying is about `operator<` on moved out object, but I do not understand why it is allowed to be called in first place, because calling anything with precondition on moved out object is UB, is not it?

---

## Comment 21

> Username: djowel  
> Created at: 2019-02-01 02:43:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459585460  

> I am reading the thread and see that the worrying is about `operator<` on moved out object, but I do not understand why it is allowed to be called in first place, because calling anything with precondition on moved out object is UB, is not it?  
  
That's the thing about the debate. It is not. There's no such thing as zombee/half-dead objects. "The moved-from state is part of the object's invariant, and objects in that state may become exposed in several ways (e.g. use std::remove on a sequence of them)"

---

## Comment 22

> Username: Kojoley  
> Created at: 2019-02-01 14:04:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/445#issuecomment-459732448  

I did not say "zombee/half-dead objects", I was saying about the same thing as shown in an example given in standard [\[defns.valid\]](http://eel.is/c++draft/defns.valid). If precondition not met - the call is illegal.  
The main problem is that currently variant itself does not provide a way to check the precondition. You can say that `variant` guarantees to be non-empty, but that thing was designed for copying operation that **can** throw, no one needs it for move operation that **cannot** throw. For crazy guys who want to apply a visitor to a moved out variant it is possible to provide a method that will check if currently active `recursive_wrapper` is not accessible.
