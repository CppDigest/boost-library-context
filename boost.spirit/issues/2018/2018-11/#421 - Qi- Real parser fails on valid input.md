# #421 - Qi: Real parser fails on valid input [Closed]

> Username: mwpowellhtx  
> Created at: 2018-11-21 16:24:56 UTC  
> Updated at: 2019-04-25 11:46:56 UTC  
> Closed at: 2019-04-25 11:46:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/421  

Literally, I want the real parser policy to support a format something like this, in regular expression: ``(\d*\.\d+)|(\d+\.\d*)``. Something like that.  
  
Literally, I've got the following v2 Protocol Buffer statement which is being rejected by Boost Spirit:  
  
```  
syntax = 'proto2';option a =    933252761176835091419019014852578134454945897134240121039056292642745934626544776679309792643426912681734775655456099849022472225507009671635655261635494179939591695082743337844489696175391517474159955183289891072214770215979051082304343590361421008538352849939802258131368544983961387362613078430270357504.000000;  
```  
  
This *Floating Point* number is well within the range of maximum ``double``: ``179769313486231570814527423731704356798070567525844996598917476803157260780028538760589558632766878171540458953514382464234321326889464182768467546703537516986049910576551282076245490090389328944075868508455133942304583236903222948165808559332123348274797826204144723168738177180919299881250404026184124858368.0000000000000000``.  
  
```C++  
namespace ast {  
    enum num_sign_t {  
        sign_none  
        /// '-'  
        , sign_minus  
        /// '+'  
        , sign_plus  
    };  
    // ...  
    using float_t = double;  
}  
```  
  
```C++  
qi::rule<It, ast::num_sign_t()> num_lit_sign;  
// ...  
qi::rule<It, ast::float_t()> float_lit;  
qi::rule<It, ast::floating_point_t()> floating_point_lit;  
``  
  
```C++  
// For use with both Integer and Floating Point Literals.  
num_lit_sign %= ('-' >> qi::attr(ast::num_sign_t::sign_minus))  
    | ('+' >> qi::attr(ast::num_sign_t::sign_plus))  
    ;  
// ...  
// Floating-point literals  
float_lit %= real_parser<ast::float_t, strict_ureal_policies<ast::float_t>>{};  
floating_point_lit %= -num_lit_sign >> float_lit;  
```  
  
The parser works fine with scientific notation, as far as I can tell. But it rejects fixed point format. According to the [documentation](http://www.boost.org/doc/libs/1_68_0/libs/spirit/doc/html/spirit/qi/reference/numeric/real.html#spirit.qi.reference.numeric.real._code__phrase_role__identifier__realpolicies__phrase___code_), IMO it should be accepting fixed point format, but it is rejecting that.  
  
I'm not doing anything special for test cases, just generating some randomly distributed data, binary split, recursive, depth ~3 or so, count ~5 or so per depth. In other words, I generate some random values in the range, split the range in half, and call recursively for both halves, rinse and repeat. Like I said, nothing fancy for random test case generation.  
  
However, I'm concerned that the policy is a "hard coded" strategy, literally does not use Qi rules, etc. So, I do not know if that's a thing. Or if there are any traits, etc, I need to specify in order for fixed point to be properly supported.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-11-22 14:05:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441040493  

> This Floating Point number is well within the range of maximum double: 179769313486231570814527423731704356798070567525844996598917476803157260780028538760589558632766878171540458953514382464234321326889464182768467546703537516986049910576551282076245490090389328944075868508455133942304583236903222948165808559332123348274797826204144723168738177180919299881250404026184124858368.0000000000000000  
  
I confirm that `double_` cannot handle this value while it should. The https://svn.boost.org/trac10/ticket/11608 must be the same issue.  
  
> The parser works fine with scientific notation, as far as I can tell. But it rejects fixed point format. According to the documentation, IMO it should be accepting fixed point format, but it is rejecting that.  
  
I do not understand what are you talking about. You did not provide an [MCVE](https://stackoverflow.com/help/mcve) **again**.

---

## Comment 2

> Username: mwpowellhtx  
> Created at: 2018-11-22 15:39:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441065961  

@Kojoley Respectfully, I need to provide an [MCVE](http://stackoverflow.com/help/mcve)? Really? The issue is ***THREE YEARS*** in the making, bare minimum, and no patch for it yet?  
  
> I do not understand what are you talking about.  
  
The real policy works fine for scientific notation. Does not work for fixed notation. That is your MCVE, never minding the [Trac issue](http://svn.boost.org/trac10/ticket/11608).

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-11-22 15:55:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441070246  

> Respectfully, I need to provide an MCVE? Really?  
  
Yes, if you open an issue, please provide an MCVE.  
  
> The issue is THREE YEARS  
  
Calm down. You even did not know that the trac issue exists, did you gave the link to it? No. Do I know that it is the same issue? I am not sure.  
  
> in the making, bare minimum, and no patch for it yet?  
  
I am maintaining the library for the last 11 months, and I am doing it in my **spare** time. You also could make a patch and open a PR instead of blowing the toxicity.  
  
> > I do not understand what are you talking about.  
> The real policy works fine for scientific notation. Does not work for fixed notation.   
  
One more time, I do not understand what are you talking about, there is scientific/fixed notation switches in Karma, but not in Qi.

---

## Comment 4

> Username: mwpowellhtx  
> Created at: 2018-11-22 16:02:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441072140  

@Kojoley No, not a time to calm down. The issue is known for three years and you ask for an MCVE? You have it in the issue.

---

## Comment 5

> Username: Kojoley  
> Created at: 2018-11-22 16:15:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441075294  

> No, not a time to calm down.  
  
I suggest to use your inner forces for making a PR then.  
  
> The issue is known for three years  
  
The issue I have linked is known for me for less than a day.  
  
> and you ask for an MCVE? You have it in the issue.  
  
I do not know what issue you are referring to, and I do not understand what is 90% of https://github.com/boostorg/spirit/issues/421#issue-383196721 is about.

---

## Comment 6

> Username: cppljevans  
> Created at: 2018-11-22 16:20:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441076370  

On 11/22/18 10:02 AM, Michael W Powell wrote:  
> @Kojoley No, not a time to calm down. The issue is known for three years and you ask for an MCVE? You have it in the issue.  
>   
I'd have to side with Kojoley here.  As Kojoley has calmly pointed out,  
he did not start support for spirit until recently; yet, you seem to  
hold him responsible for something that happened three years ago.  
  
Kojoley has contributed greatly to finding bugs and correcting them.  
You'd do well to follow his advice.  
  
-Larry

---

## Comment 7

> Username: mwpowellhtx  
> Created at: 2018-11-22 16:24:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441077462  

@Kojoley It was working three years ago, then was suddenly broken. Sorry, that's beyond my bandwidth to pursue. I'd expect it not to be broken and stay broken for three years. Today, it should be *just working*.

---

## Comment 8

> Username: Kojoley  
> Created at: 2018-11-22 16:41:10 UTC  
> Updated at: 2018-11-22 16:41:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441081223  

> It was working three years ago, then was suddenly broken.  
  
Your bug report does not contain this information, neither you provided dates, versions, or commits when it become broken. It is not necessary for fixing, but you speak like you knew it, I guess you just realized it from the trac ticket I have linked.  
  
> Sorry, that's beyond my bandwidth to pursue. I'd expect it not to be broken and stay broken for three years. Today, it should be just working.  
  
It is a very comfortable position for you. You do not want to fix the problem or at least to make your bug report clear to understand. Instead you are pretending that someone owes you to dig into your problems and to resolve them immediately.

---

## Comment 9

> Username: mwpowellhtx  
> Created at: 2018-11-22 16:50:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441083256  

My position with this today is to evaluate how much of a rabbit hole into which I am willing to dive. Considering the issue was broken three years ago when it had worked prior to that... You are correct, I am not willing to pursue that sort of remediation over something that, in my professional opinion, should have been detected in at least a unit test, integration test, never mind regression testing.  
  
Boost Spirit Qi is not the only game in town, and I've got much more grammar verification to work through just aside from this issue evaluating how strong of a fit it is for the overarching concern I am working on. But I do not need to explain those details to you, @Kojoley, it is none of your business really, apart from the inquiry, is there a parser policy I am missing, undocumented, etc. Then to learn that it is quite probably a three year old issue which is been allowed to stay broken.  
  
So... that to say, fair enough. I do not have the bandwidth to be distracted like this. I file it under "known issues," pros/cons whether this is a viable stack for the problem solution approach.

---

## Comment 10

> Username: Kojoley  
> Created at: 2018-11-22 17:05:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441086515  

> My position with this today is to evaluate how much of a rabbit hole into which I am willing to dive. Considering the issue was broken three years ago when it had worked prior to that... You are correct, I am not willing to pursue that sort of remediation over something that, in my professional opinion, should have been detected in at least a unit test, integration test, never mind regression testing.  
  
So, instead of opening a PR that adds a test you are blaming everyone else that no one have added it.  
  
> Boost Spirit Qi is not the only game in town, and I've got much more grammar verification to work through just aside from this issue evaluating how strong of a fit it is for the overarching concern I am working on. But I do not need to explain those details to you, @Kojoley, it is none of your business really, apart from the inquiry  
  
And I do not want to know, that's why I asked you for an MCVE.  
  
> So... that to say, fair enough. I do not have the bandwidth to be distracted like this.  
  
FYI you have distracted 36 people that are subscribed for updates on the repository.

---

## Comment 11

> Username: djowel  
> Created at: 2018-11-22 23:18:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441132725  

@mwpowellhtx , it's my fault that I haven't followed up on the trac ticket. Admittedly, I haven't had enough time in the recent years and I am very thankful that @Kojoley is giving his time maintaining spirit in the past year or so.

---

## Comment 12

> Username: djowel  
> Created at: 2018-11-22 23:47:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441134739  

@mwpowellhtx, at any rate, I'm pretty sure there will be action on this ticket. It's amazing how @Kojoley has been continuously working on and closing tickets. I just closed a bunch that he workd on yesterday. I wouldn't be surprised if he's already looking into this as we speak. Or perhaps this is a good chance for me to do more Spirit dev.

---

## Comment 13

> Username: Kojoley  
> Created at: 2018-11-23 00:16:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-441136533  

Thanks Joel. I am going to dig into the particular issue on the weekend as I expect it is not easy to debug and fix. I will appreciate any help on it, if it is a regression and was working somewhere in the past we should probably start with `git blame` and hopefully it will reveal the particular change that introduced the regression.

---

## Comment 14

> Username: djowel  
> Created at: 2019-04-25 11:46:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/421#issuecomment-486639035  

Update: Both https://svn.boost.org/trac10/ticket/11608 and this one is now fixed in the latest commits in develop. Tests added.
