# #287 - x3 cannot parse into associative containers anymore [Closed]

> Username: octopus-prime  
> Created at: 2017-11-22 12:10:49 UTC  
> Updated at: 2017-12-07 10:43:34 UTC  
> Closed at: 2017-12-07 10:43:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/287  

Today i took a look at boost 1_66_0_b1 (because of boost beast).  
But this broke my parser based on x3.  
  
```  
source/parser.cpp:48:53:   required from here  
/home/mike/Downloads/boost_1_66_0_b1/boost_1_66_0/boost/spirit/home/x3/core/detail/parse_into_container.hpp:259:17: error: no matching function for call to ‘std::map<hessian::value_t, hessian::value_t>::insert(std::map<hessian::value_t, hessian::value_t>::iterator, std::map<hessian::value_t, hessian::value_t>::iterator, std::map<hessian::value_t, hessian::value_t>::iterator)’  
                 attr.insert(attr.end(), rest.begin(), rest.end());  
```  
  
As you can see there is a `attr.insert(attr.end(), rest.begin(), rest.end());` call now.  
This does not work, because no associative container (std::set, std::map, ...) provides this member function.  
  
I think this bug is result of commit: https://github.com/boostorg/spirit/commit/379413a50c93539aa432fa9f9fc94fec1d6aa60d#diff-2b82341b4caa57a2a0c6bc9a616a1e1a

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-11-22 12:24:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346335496  

The good news is we have container_traits. So replacing  
  
`attr.insert(attr.end(), rest.begin(), rest.end());`  
by  
`traits::append(attr, rest.begin(), rest.end());`  
  
could help.

---

## Comment 2

> Username: Kojoley  
> Created at: 2017-11-22 12:37:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346338164  

Thanks for reporting this. I failed to find tests for associative containers in `x3` what is very disappointing. I am afraid the bug will not be fixed in 1.66 :disappointed:.

---

## Comment 3

> Username: octopus-prime  
> Created at: 2017-11-22 12:41:27 UTC  
> Updated at: 2017-11-22 12:46:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346339075  

Ouch. The bugfix seems really easy. Tried it here successfully. And bugfix period for 1.66 ends at dec/06. So there is plenty of time...  
  
btw: There are a lot of tests for container_traits (including tests for associative containers):  
https://github.com/boostorg/spirit/blob/develop/test/x3/container_support.cpp

---

## Comment 4

> Username: Kojoley  
> Created at: 2017-11-22 12:54:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346342001  

The master has not been updated since August and it contains not only bugfixes so we need to backport them from develop. Additionally `x3` does not run on boost regression and it is the second part of the problem.

---

## Comment 5

> Username: octopus-prime  
> Created at: 2017-11-22 13:00:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346343329  

That's disappointing. Can i help to improve our situation?

---

## Comment 6

> Username: Kojoley  
> Created at: 2017-11-22 13:19:53 UTC  
> Updated at: 2017-11-22 13:21:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346347885  

We need to:  
 - decide what should be backported  https://github.com/boostorg/spirit/compare/master...develop (there is at least #266) to fix the test suite (you can create a branch from master and cherry-pick merge commits from develop to it)  
 - add tests  
 - run the tests  
 - write release notes  
  
I can run tests on VS 2008, 2010, 2015, 2017 locally, and bunch of Clang/GCC on Travis.

---

## Comment 7

> Username: Kojoley  
> Created at: 2017-11-22 15:09:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346378321  

Oh, I am blind, there are tests for `std::set`, `std::map` and etc https://github.com/boostorg/spirit/blob/de81dc44dd30c67393a46319e8f04333c9802bdb/test/x3/container_support.cpp#L195-L209 and they are passing.

---

## Comment 8

> Username: octopus-prime  
> Created at: 2017-11-22 15:23:00 UTC  
> Updated at: 2017-11-22 15:24:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346382315  

Yes, i told you:  
  
> btw: There are a lot of tests for container_traits (including tests for associative containers):  
> https://github.com/boostorg/spirit/blob/develop/test/x3/container_support.cpp  
  
:-)  
  
But you are right. There is still the problem with https://github.com/boostorg/spirit/compare/master...develop and the 25 open pull requests. So I am afraid the bug will not be fixed in 1.66 too.  
  
In my opinion we need a plan what to merge/backport and when. Perhaps @djowel has an idea for the future of spirit (especially x3)?  
  
For providing bug fixes (like this one), fixing test suites and adding tests I would be able to support.

---

## Comment 9

> Username: djowel  
> Created at: 2017-11-22 23:04:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346496382  

25 open PRs! We should start looking into these. I think you guys are doing good, esp. @Kojoley with his splendid work cleaning up Spirit! That's the first step. Then we can talk about new developments. Perhaps we can start a page on what we want to develop for Spirit, esp. x3. I do have some ideas. It's just getting so hard to find the right time for it these days.

---

## Comment 10

> Username: djowel  
> Created at: 2017-11-22 23:05:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346496512  

Oh and @octopus-prime, thanks for the offer providing bug fixes, fixing test suites and adding tests! Much appreciated!

---

## Comment 11

> Username: Kojoley  
> Created at: 2017-11-22 23:15:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346498110  

> Yes, i told you  
>> btw: There are a lot of tests for container_traits (including tests for associative containers):  
>> https://github.com/boostorg/spirit/blob/develop/test/x3/container_support.cpp  
  
Meh, my bad, but `container_support.cpp` does not run because it is not present on `Jamfile`. I just tested and it passes (not on MSVC, because of the other problem).  
  
`container_support.cpp`  did not trigger the needed branch with it's tests. I do not currently understand why. The test runs these branches  
  
pass_attibute_as_is | is_associative  
-------------------- | ----------------  
true | false  
false | true  
  
while problem you described needs `true` `true` to show up.  
  
I have success with this code, but have not got a whole picture for now.  
```  
    auto const seq2_rule = string_rule >> +(',' >> string_rule);  
```

---

## Comment 12

> Username: octopus-prime  
> Created at: 2017-11-23 10:31:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346581542  

If I get you right  
  
* container_support tests pass if started  
* container_support tests could be add to jamfile  
* container_support tests has some problems with MSVC (but we can ignore it?!)  
* container_support tests should be improved to test more branches (e.g. all parse_into_container branches)  
  
?  
  
To reproduce the compiler error try this:  
  
```  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/std_pair.hpp>  
#include <map>  
  
namespace x3 = boost::spirit::x3;  
  
x3::rule<struct string, std::string> const string;  
x3::rule<struct pair, std::pair<std::string, std::string>> const pair;  
x3::rule<struct map, std::map<std::string, std::string>> const map;  
auto const string_def = *x3::alpha;  
auto const pair_def = string >> string;  
auto const map_def = pair >> +(',' >> pair);  
  
BOOST_SPIRIT_DEFINE(string, pair, map);  
  
int main() {  
	std::string data;  
	std::map<std::string, std::string> attr;  
	x3::parse(data.begin(), data.end(), map, attr);  
	return 0;  
}  
```

---

## Comment 13

> Username: octopus-prime  
> Created at: 2017-11-25 17:48:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346955389  

Any chance to release the bugfix in 1.66?

---

## Comment 14

> Username: Kojoley  
> Created at: 2017-11-25 17:51:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/287#issuecomment-346955563  

https://lists.boost.org/Archives/boost/2017/11/239937.php
