# #127 - Setting mpfr_float_backend to 0 and default_precision to 0 - 6 and results do not make sense [Closed]

> Username: Light-Wizzard  
> Created at: 2019-04-01 23:24:59 UTC  
> Updated at: 2019-06-19 20:16:58 UTC  
> Closed at: 2019-06-19 11:13:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/127  

Keep in mind that Precision is based on the total number of digits and not the decimal places, as such it is the right and left side of a decimal point combined, but I need a way to set the Decimal places and all I can find is Precision, so I am trying to work with it, but this is what I get, if I take a number like "0.123", and set precision to 1 or 0, why does it not return 0, not that this is useful for anything, and most likely an error to begin with, since you can only ask for what you give it, and 0 is 1 Precision digit, and 0.0 is 2, so setting it to 2 should return 0.1, setting to 3 should give me 0.12, and it is at 4 Precision digits to begin with, so I see this as a bug, or something I do not understand, did I miss something?  
  
Logic table:  
  
0.123456789  
  
set to 0 Precision I get: 0.12  
  
set to 1 Precision I get: 0.125 (in what universe does this make sense, 3 becomes 5?)  
  
set to 2 Precision I get: 0.1235  
  
set to 3 Precision I get: 0.12347 (in what universe does this make sense, 4 becomes 7?)  
  
set to 4 Precision I get: 0.123455 (?)  
  
set to 5 Precision I get: 0.123457 (?)  
  
set to 6 Precision I get: 0.12345678 (?)  
  
Is this the correct behavior?  
  
If so please explain, how is 4 and 5 correct, and why the jump in precision between 5 and 6, this cannot be the correct behavior, these results do not even make sense to me.   
  
Here is a working example of what I am doing using PI as an example:  
  
`using namespace boost::multiprecision;`  
`typedef boost::multiprecision::number<mpfr_float_backend<0> > my_mpfr_float;`  
`my_mpfr_float::default_precision(6);`  
`my_mpfr_float aThis("3.141592653589793239");`  
  
https://en.cppreference.com/w/cpp/io/manip/setprecision  
  
Setting Precision to 6 should give me this: left = 1 and right = 5 for a total of 6 digits of precision: 3.14159  
  
Setting to 6 I get 6 decimals: 3.141592 leaving me to believe that precision and decimal places mean the same thing.  
  
Setting it to 5 I get 6 decimals: 3.141586 but expected this 3.141592   
  
Setting to 4 and I get 6 decimals: 3.141586  
  
Setting to 3 and I get 4 decimals: 3.1406  
  
Setting it to 2 and I get 3 decimals: 3.141  
  
Setting it to 1 and I get 2 decimals: 3.12  
  
Setting it to  0 and I get: 4 and that is correct, if Precision is based on number of Decimals.  
  
Look at the Math in each setting, it does not seem correct to me.  
  
It is clear to me that setPrecison does not work the same as this function.  
  
Run this test and tell me this is the expect behavior and I will shut up about it, but looks like a bug to me, and it is giving me a headache trying to figure it out, the math just seems wrong to me for some reason, I can not put my finger on it, 3.141586 but expected this 3.141592 please explain how this type of math works, please round this by hand in your head:  
  
   123456  
  
3.141592653589793239  
  
3.141592 round at 6 the answer should be 3.141593, or did I flunk math? Do you see a problem here or is it me?  
  
I was converting an old bash script, so I know my numbers are right, I did them by hand, to make sure, I use bc in bash, it takes a string, uses Arbitrary Precision math, and Genus Calculator is another tool to verity the math, but seriously, I can do this math in my head, I know it is wrong, and this is freaking me out more than the accuracy of float and double, and they were off by a 100 miles, and I am not exaggerating, I can prove it, I am trying to calculate something, not estate it.  
  
Using the documents and the latest build defined as 1.67 and 1.69, and possibly going back further, as well as the latest in development:  
  
https://www.boost.org/doc/libs/1_69_0/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/mpfr_float.html  
  
`#include <boost/multiprecision/mpfr.hpp>`  
`#include <boost/multiprecision/gmp.hpp>`  
`using namespace boost::multiprecision;`  
`typedef boost::multiprecision::number<mpfr_float_backend<0> > my_mpfr_float;`  
`my_mpfr_float::default_precision(precision);`  
`my_mpfr_float aThis("123456789.123456789");`  
  
Note: I use string input because the use of float, double or long double messes up the accuracy,  
I also return a string, and conversions do the same thing.  
  
`   std::stringstream ss;`  
`   ss.imbue(std::locale(""));`  
`   ss << std::fixed << std::setprecision(int(precision)) << aThis.str();`  
  
I understand that these are two different issues, but I do find this disturbing, I would have expected the conversion to fix the problem with precision the float has, but it did not.  
  
precision is unsigned int  
  
0 gives me 4 digits, instead of the expected 0  
  
9 gives me 13 digits instead of 9.  
  
It should always work as expected and it does not.  
  
I do not believe his problem is tied to the OS, which is Ubuntu Mate Cinnamon, AMD ThreadRipper, 16 GB Ram, using Qt 5.11.1, using Qml and Felgo App, also confirmed in Qt console app, with the latest boost libraries installed for Ubuntu which is 1.67 not 1.69 for libboost-dev, been using boost since it first came out, I did check the docs for both versions and nothing should have changed to cause this issue with that version, compared to the latest in the repo, since I found no issues that address this issue, I must assume it is a first report of this problem, and not just a 1.67 issue, it should also be the latest issue, but this does need to be verified, do not assume just because no bug changes were made that improvements were not, but if one was made, I found not reference to it, and as such this is a bug, and should be documented as such, so people can find it, as such, I have no reason to believe this is a programming error or a misunderstand of how the function should work, and it is easy to test in any version to see if this is a bug, but it is not desirable behavior: 0 means 0, 9 means 9, and I do not think this effects fixed, only Arbitrary precision, as far as I can tell it is working, just not correctly at the lower end of the range, the larger the number you use for precision the closer it will get, so it is working, but as Sir Issac Newton wrote, it is best to use the correct precision when doing a calculation, because rounding is only an estimate,   
  
I would expect that if I want to run a number, I could use this function:  
  
` QString round(const QString &mThis, const unsigned int &precision)`  
` {`  
`    unsigned int decimalPlace = uint(mThis.indexOf('.')) + precision;`  
`     using namespace boost::multiprecision;`  
`     typedef boost::multiprecision::number<mpfr_float_backend<0> > my_mpfr_float;`  
`     my_mpfr_float::default_precision(decimalPlace);`  
`     my_mpfr_float aThis(mThis.toStdString());`  
`     qDebug() << "aThis=" + QString::fromStdString(aThis.str());`  
`     // As a test of mpfr_float, and also note that mpf_float is worse`  
`     // when using multiprecision/gmp.hpp`  
`     mpfr_float::default_precision(precision);`  
`     mpfr_float aThat(mThis.toStdString());`  
`     qDebug() << "aThat=" + QString::fromStdString(aThat.str());`  
`     return QString::fromStdString(aThis.str());`  
` }`  
for example the number 123456789.123456789 is 18 precision places if you total left and right :   
  
`round("123456789.123456789", 0) // I get 130000000, this is because there are 9 digits, and 0 actually rolls it backwards, so any value less than the left side length will give this behavior, so really 9 should give you 0 decimal places, but it does not`  
  
`round("123456789.123456789", 9) // returns 123456789.12 that is 11 Precision digits, expected 123456789`  
  
`round("123456789.123456789", 12) // returns 123456789.12347 that is 15 Precision digits, expected 123456789.123`  
  
I have a 9 whole digits on the left, and 4 on the right, I need a precision of 13 to display it the way it is, since Precision counts both sides as a whole, in other words, setprecision will use the total number of digits and not just the decimal places.  
  
Not great if I am dealing in Money, so this is a very ugly bug, if it is in fact a bug, I can not make that call, I just report the behavior I think is a bug. I think that others would have noticed this, so it makes me ask others if they see the same results, and now I have open forum tickets where people do not respond, as if they look at the code and it should work that way, and maybe it is just my problem, but I do not think so, I think if I ask for 0 I should get 0, if I set it to 1 or 2 or 3, that is what I should expect as a result, this is just common sense.  
  
Rounding this way, should be the same as just changing precision.  
  
I use QString because I am using Qt, use what you want for the test, above is stringstream example, but you can only deal in string, and not float, double or long double, keep in mind I call this from Qml JavaScript, so I except a string I can use in the correct precision format.  
  
Keep in mind that Precision normally refers to the total amount of digits displayed and not just the decimal places, as such: 0.123 has a precision of 4, as such I would expect if I set it to 0, I would get 0, grant you that is not what most people would expect, but if they count the whole number, I can account for that in my equation, what I can not do is figure out how to make this work correctly.  
  
Can someone show me an example of how to set the number of Decimal places, and not the Precision, since I can not seem to make that work, and grant you if I do it is a hack, because Precision and Decimal places do not mean the same thing, and this is just a sample, when I big number, it just gets confusing, I need to know what precision actually means when it comes to this function, it is clear that I am confused with all the test I am doing, sometimes it seems to be close, while others it seems I must be missing something important about this function.

---

## Comment 1

> Username: orkolorko  
> Created at: 2019-06-19 02:27:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/127#issuecomment-503380698  

The reason of the odd behavior is that the mantissa of a multiprecision floating point is a binary number.  
When you set your precision to 1, this means that your mantissa has 3 (or 4) binary digits.  
Therefore 0.123 is not representable exactly, and depending on the chosen rounding, the nearest representable number in 3-bit floating point is 0.125 (which is 1/8=(0.001)_2).

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-06-19 11:13:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/127#issuecomment-503516684  

The question here is "what do you want to do"?  
  
* If you want the output precision (number of decimal places in the string), then use std::setprecision(N) on the output stream.  
* If you want to change the working precision of the number type, then use mpfr_float::default_precision(N).  BUT: please note that this is a binary floating point type and therefore calling default_precision(N) will set the binary precision to enough digits to guarantee AT LEAST N decimal places of precision.  
  
Finally note that due to binary-decimal and decimal-binary conversion being inherently inexact (for example some simple decimal numbers like 0.1 have infinite recurring binary representations), just because you have N digit working precision, that does not mean that the underlying binary representation can be printed out exactly in just N digits - which is what you are seeing above.  
  
Closing for now as it appears to be based on a misunderstanding of binary/decimal conversions.

---

## Comment 3

> Username: Light-Wizzard  
> Created at: 2019-06-19 20:16:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/127#issuecomment-503730333  

I understand why it is giving me strange results now that I have had more time to work with it, my point is that there should be a way to normalize a number to catch edge cases like this, and I know most of my issues was from using precision, when what I want is decimal places, the two are not compatible, precision deals with the number of digits in the whole number, whereas decimal places deal with only the fractional number, and that is where all my issues arise, I am trying to use precision to get decimal places to round correctly, and that does not work, I need a function that deals with decimal places, so you can close this issue, it is just something everyone that uses it needs to understand about it, it just confused me how bad floating-point math can be, and why I was hoping to get away from it, by using a multiprecision library, but it seems I can't, but I did find a solution with another library that does support decimal places, and gave me the correct results, I know now that floating-point math just does not work for what I need, which is accuracy with very large numbers, with very large decimal places, and being able to correctly round the number of decimal places, using these same numbers, bignumbers.js was able to give me the correct results, I understand now that I was trying to use the wrong function for what I needed, and as a result, I thought I found a bug, well I did, but it is with Floating-point math, and beyond the scope of this function, it is what it is, and might work great for that purpose, but it does not have the capability to deal with decimal places as is, and now I understand that is because its not the correct function to use, I just expected more accuracy from it, and that was not resealable giving floating-point and decimal-binary  point conversion, so this was my misunderstanding of what I should expect, and as you pointed out, what I get is not accurate, but it is due to the underlying math protocols, and not so much with the function itself, or a bug in the library, which is what I need to know, and you confirmed it is not, so this issue is considered close, I think more examples would have cleared up my confusion, as it was, I was comparing math I can do in my head, with results returned by this function, and it is not right, floating-point and decimal-binary conversion, go figure, but I did learn that if you ask for something, you have to know what you are asking for, in my case, what I was asking for was unreasonable, and the function did not deal with the request very well, it did no bounds to check to ensure it can be done, because my edge cases fail for a reason, they are edge cases, and without normalizing them, you will get confusing results, by normalizing, I mean if you have 123.123 and as for 6 digits of precision, then do multiplication, thinking that this will cap the decimal places to 3, guess again, logic mistake, decimal places have nothing to do with precision, and you can not change that fact, and I tried, and failed, and thought it must be a bug, but that bug is in my head, my results do not make any sense, because they are failed edge cases, I get that now, thanks for all the help, and just to be clear, is there a function that handles decimals places? I never found one, or any examples of how to use it that worked the way I needed, I do not care about Precision, only the number of Decimal places, and I need to use the results in Qt Qml in JavaScript, not part of my original question, but it should have been, thanks.
