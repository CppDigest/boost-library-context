# #179 - boost regex constructor is not throwing boost::regex_constants::error_badbrace in boost-1.66.0-10.el8.x86_64 [Closed]

> Username: lohitendu2000  
> Created at: 2022-09-28 11:03:53 UTC  
> Updated at: 2022-10-10 17:53:24 UTC  
> Closed at: 2022-09-28 11:49:28 UTC  
> Url: https://github.com/boostorg/regex/issues/179  

This following program I have compiled and run in two versions of boost, which is boost-1.53.0-27.el7.x86_64 and boost-1.66.0-10.el8.x86_64  
  
#include <boost/regex.hpp>  
#include <string>  
#include <iostream>  
  
int main()  
{  
   try {  
         std::string value = "^[a-z0-9]{,4}$";  
         boost::regex pattern(value);  
   }  
   catch (boost::regex_error &e)  
   {  
     std::cout<<"the error caught is "<<e.what()<<std::endl;  
     return 0;  
   }  
   std::cout<<"no error caught"<<std::endl;  
   return 0;  
}  
  
Output when boost is in version 1.53  
the error caught is Invalid content of repeat range.  The error occurred while parsing the regular expression: '^[a-z0-9]{>>>HERE>>>,4}$'  
  
Output when boost is in version 1.66  
no error caught  
  
  
I am expecting the error thrown in 1.66 version to be same as error thrown in 1.53. It looks like in 1.66 version no error is thrown.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-09-28 11:49:28 UTC  
> Url: https://github.com/boostorg/regex/issues/179#issuecomment-1260788807  

That's correct: I think the Perl regex engine changed and we eventually caught up: {,4} means "repeat up to 4 times", so the same as {0,4}.  
  
Please re-open if I've misunderstood.

---

## Comment 2

> Username: lohitendu2000  
> Created at: 2022-09-29 04:37:29 UTC  
> Updated at: 2022-09-29 04:47:06 UTC  
> Url: https://github.com/boostorg/regex/issues/179#issuecomment-1261743701  

Ok but, when I change the value from std::string value = "^[a-z0-9]{,4}$"; to this: std::string value = "^[a-z0-9]{a,z}$"; in the same sample program, then also there is a difference.  
  
Output when boost is in version 1.53  
the error caught is Invalid content of repeat range.  The error occurred while parsing the regular expression: '^[a-z0-9]{>>>HERE>>>a,z}$'.  
Output when boost is in version 1.66  
no error caught  
  
For this I have opened a new issue #180

---

## Comment 3

> Username: lohitendu2000  
> Created at: 2022-09-29 04:56:18 UTC  
> Url: https://github.com/boostorg/regex/issues/179#issuecomment-1261752274  

I also think a{4,} where a can be repeated at least 4 times and then is no upper limit. But should a{,4} be treated as a valid pattern at all? 0 and no lower limit is not same.  
  
The boost documentation also mentions only about missing upper limit but not about missing lower limit.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-09-30 17:19:12 UTC  
> Url: https://github.com/boostorg/regex/issues/179#issuecomment-1263825269  

> Ok but, when I change the value from std::string value = "^[a-z0-9]{,4}$"; to this: std::string value = "^[a-z0-9]{a,z}$"; in the same sample program, then also there is a difference.  
  
Very old bug fix: https://svn.boost.org/trac10/ticket/8569  
  
Basically { followed by illegal quantifiers is treated as a literal.  This is for compatibility with what Perl does.  
  
>ut should a{,4} be treated as a valid pattern at all? 0 and no lower limit is not same.  
  
Sigh.  Like you I like strong error handling, but we promise to do what Perl does.  
  
>The boost documentation also mentions only about missing upper limit but not about missing lower limit.  
  
I don't think the Perl docs mention this either, it doesn't mean there is no lower bound, here the { is treated as a literal as in the {a,z} case.

---

## Comment 5

> Username: lohitendu2000  
> Created at: 2022-10-10 07:07:31 UTC  
> Updated at: 2022-10-10 11:09:37 UTC  
> Url: https://github.com/boostorg/regex/issues/179#issuecomment-1272867153  

@jzmaddock  Ok, In the first case I can see that from perl 5.34.0 version {,x} is accepted as an enhancement: https://perldoc.perl.org/5.34.0/perldelta  
Therefore no issues in first case.  
  
But for {a,z} case I have tested following program in perl 5.34.0 version:  
  
#!/usr/bin/env perl  
use strict;  
use warnings;  
while (<>) {   # Read input from command-line into default variable $_  
   print /^[a-z0-9]{a,z}$/ ? "Accept\n" : "Reject\n";  
}  
  
And following is the output:  
./try_1.pl  
Unescaped left brace in regex is passed through in regex; marked by <-- HERE in m/^[a-z0-9]{ <-- HERE a,z}$/ at ./try_1.pl line 6.  
  
So I dont think perl latest version also accepts this as a literal and backtracks it? But it throws error as above. If Perl throws error, then why boost treats it as literal and backtracks it?   
perl accepts it when the { is escaped i,e ^[a-z0-9]\{a,z\}$ and then a{a,z} will be matched  
are you saying boost from 1.66 version treats it this way?  
  
Perl documentation says this:  
_(If a non-escaped curly bracket occurs in a context other than one of the quantifiers listed above, where it does not form part of a backslashed sequence like \x{...}, **it is either a fatal syntax error, or treated as a regular character,** generally with a deprecation warning raised. To escape it, you can precede it with a backslash ("\{") or enclose it within square brackets ("[{]"). This change will allow for future syntax extensions (like making the lower bound of a quantifier optional), and better error checking of quantifiers)._  
  
  
it is either a fatal syntax error, or treated as a regular character, ----- > although perl treats it as syntax error, are you saying boost has taken the path to take it as regular character (literal) ?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2022-10-10 17:53:24 UTC  
> Url: https://github.com/boostorg/regex/issues/179#issuecomment-1273645364  

OK, it looks like Perl has changed.  
  
This behaviour was introduced as a result of this bug report: https://svn.boost.org/trac10/ticket/8569 as Perl always used to treat the { as a literal if not part of a valid repeat range.  I'll investigate what current Perl does.
