# #586 Karma: Do not limit precision of real_inserter [Merged]

> Username: jpfeuffer  
> Created at: 2020-04-10 09:55:19 UTC  
> Updated at: 2021-02-15 13:54:50 UTC  
> Merged at: 2021-02-15 13:54:50 UTC  
> Closed at: 2021-02-15 13:54:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/586  

Users should be able to decide how much they want to print.  
Especially since this implementation does not care about significant digits, which hinders unique serialization when using fixed notation with leading zeros.  
  
AFAIK digits10 and max_digits10 are about the **significant** digits, not the digits in the fractional part.  
And for serialization and a string-float-string round-trip you should actually at least allow max_digits10 instead of digits10.  
  
Closes #585

---

## Comment 1

> Username: hkaiser  
> Created_at: 2020-04-10 13:03:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/586#issuecomment-612019102  

We could limit the precision to `15` instead if we knew that we're dealing with an IEEE 754 conforming FP implementation (see https://en.wikipedia.org/wiki/Floating-point_arithmetic). However, the standard does not assume FP numbers to be IEEE 754 conforming, thus the only way to detect the available precision is to look at `digits10`.   
  
We've had some discussion on a related topic here: https://github.com/boostorg/spirit/issues/529.  For the reasons outlined there, I'm not fond of completely lifting the limitation.  
  
@jpfeuffer for your purposes I would suggest you switch to represent FP numbers in hexadecimal format, which is guaranteed to represent all bits of the number.

---

## Comment 2

> Username: jpfeuffer  
> Created_at: 2020-04-10 13:26:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/586#issuecomment-612027403  

Ok hmm, I indeed (probably wrongly) assumed IEEE754 conformance. But what are potential drawbacks of allowing more digits (except writing some potentially useless digits)? I am just curious.  
  
I think with the current solution you can not print enough digits for numbers like:  
0.007519531251111111376517204263336680014617741107940673828125  
  
Boost::karma will print (mind the leading zeros):  
0.0075195312511111  
  
Which, when read back in will give you the equivalent of:  
0.007519531251111100100814610414090566337108612060546875  
  
Actually, I don't necessarily need this precision but I thought if I could do it lossless (while keeping it  
easily readable in decimal format) then I should give it a try.

---

## Comment 3

> Username: hkaiser  
> Created_at: 2020-04-10 13:48:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/586#issuecomment-612036017  

@jpfeuffer please go read the discussion in #529, that explains why we can't do that.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2020-04-10 14:16:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/586#issuecomment-612047628  

@hkaiser FYI there was trac ticket about karma-qi roundtrip issue due to karma limiting the number of digits https://svn.boost.org/trac10/ticket/11056, I also do not think IEEE754 has anything with the string representation and AFAIK numerically stable float to string implementations produce as much digits as needed for parsers to roundtrip, see https://github.com/ulfjack/ryu.

---

## Comment 5

> Username: hkaiser  
> Created_at: 2020-04-10 14:56:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/586#issuecomment-612065066  

> @hkaiser FYI there was trac ticket about karma-qi roundtrip issue due to karma limiting the number of digits https://svn.boost.org/trac10/ticket/11056  
  
Sure. I still think that there is no point in generating more digits than the internal representation can guarantee.  
  
> I also do not think IEEE754 has anything with the string representation  
  
I agree and I didn't mean to conflate the two things. What I meant was that if we knew that the FP numbers are IEEE 754 conforming, we could limit the precision to `15` instead of relying on `digits10`.  
  
> and AFAIK numerically stable float to string implementations produce as much digits as needed for parsers to roundtrip, see https://github.com/ulfjack/ryu.  
  
I need to investigate this further. I'd be happy to accept PRs that ensure numerical roundtrip stability. Just un-limiting the precision is not the correct way to go, however.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2020-04-10 15:11:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/586#issuecomment-612071991  

> What I meant was that if we knew that the FP numbers are IEEE 754 conforming, we could limit the precision to `15` instead of relying on `digits10`.  
  
`digits10` does not account for rounding errors, `max_digits10` should though.  
  
> Just un-limiting the precision is not the correct way to go, however.  
  
We clearly see here that policies that meant to allow customizing the generation are not useful because the parser overrides the value, may be move that code into a default policy?

---

## Comment 7

> Username: hkaiser  
> Created_at: 2020-04-10 15:19:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/586#issuecomment-612075044  

>> What I meant was that if we knew that the FP numbers are IEEE 754 conforming, we could limit the precision to 15 instead of relying on digits10.  
>   
> digits10 does not account for rounding errors, max_digits10 should though.  
  
I'd be happy to accept a PR that does this change (if it is accompanied with a thorough suite of tests for corner cases and ensuring nothing else breaks).  
  
>> Just un-limiting the precision is not the correct way to go, however.  
>   
> We clearly see here that policies that meant to allow customizing the generation are not useful because the parser overrides the value, may be move that code into a default policy?  
  
Same here - PRs welcome.

---

## Comment 8

> Username: bubnikv  
> Created_at: 2021-01-26 18:52:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/586#issuecomment-767753305  

I have been bitten by this issue as well. We need to export a float into a XML file with its full precision, so after reading the float from the text file the float binary representation will not be altered. For that the std::numeric_limits<T>::max_digits10 decimal digits should be exported, possibly dropping the trailing zeros. We cannot change the numbers to a hex representation, the file format not in our control and frankly it would no more be human readable.  
  
Boost::spirit produces one digit less than needed to guarantee a lossless round trip conversion and there is no way around it.   
  
Maybe karma::real_policies could be extended with a flag to decide, whether std::numeric_limits<T>::digits10 or std::numeric_limits<T>::max_digits10 is to be used to limit the number of decimal digits?  
  
  
from https://en.cppreference.com/w/cpp/types/numeric_limits/max_digits10  
> Conversion of a floating-point value to text and back is exact as long as at least max_digits10 were used (9 for float, 17 for double). It is guaranteed to produce the same floating-point value, even though the intermediate text representation is not exact.  
  
We use std::stringstream, where we set   
stream << std::setprecision(std::numeric_limits<float>::max_digits10);  
Using the "default" conversion policy, the stream automagically switches between scientific and fixed notation, and it produces up to the number of digits specified by setprecision, counting both the digits before and after the decimal point, thus guaranteeing the round trip conversion. I failed to achieve such behavior with karma.

---

## Comment 9

> Username: Kojoley  
> Created_at: 2021-02-13 13:21:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/586#issuecomment-778618502  

I still think that the PR is valid. Possibly, @hkaiser missed that the removed code hardcaps a user specified limit, the default precision of Karma was 3 digits and remains after. The change will affect only people whos custom `RealPolicy::precision` returns values bigger than this artificial hardcap. Passing test suit means that we even do not even have tests for that behavior.

---

## Comment 10

> Username: hkaiser  
> Created_at: 2021-02-13 14:32:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/586#issuecomment-778627512  

> Passing test suit means that we even do not even have tests for that behavior.  
  
Fair. Let's get this in, then.

---
