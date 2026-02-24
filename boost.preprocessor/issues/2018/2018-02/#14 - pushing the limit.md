# #14 - pushing the limit [Closed]

> Username: tobias-loew  
> Created at: 2018-02-27 10:29:28 UTC  
> Updated at: 2020-12-14 16:17:22 UTC  
> Closed at: 2020-12-14 16:17:22 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14  

Hi,  
  
I'm using the library to generate case labels for the serialization-versioning. My version number is currently at 230 and it will break 255 maybe in a year.  
I was wondering if the limits for the library could be extended to 512 or even 1024? (If there are concerns about compile-time/compiler-limits, there could be a macro for enabling the enlargement.)  
I would like to help implementing the extension.  
  
Tobias

---

## Comment 1

> Username: eldiener  
> Created at: 2018-02-27 12:41:36 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-368860930  

Are you talking about the limit to numbers in the library ?

---

## Comment 2

> Username: tobias-loew  
> Created at: 2018-03-02 13:51:02 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-369925729  

Yes. I'd like to be able to use BOOST_PP_REPEAT... for values (or ranges) larger than 256 (like 512 or 1024).

---

## Comment 3

> Username: eldiener  
> Created at: 2018-03-31 06:47:11 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-377671191  

The BOOST_PP_REPEAT construct is re-entrant. You can use it's re-entrancy to increase the number of times it calls a construct beyond the limits of a single number. This way you can have more than 255 calls using it and you can track what its total call to a 'macro(z, n, data)' is using both z and n. Similar functionality also can occur for BOOST_PP_FOR and BOOST_PP_WHILE.

---

## Comment 4

> Username: tobias-loew  
> Created at: 2018-03-31 10:06:41 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-377681939  

That's a helpful hint, but I also want to be able to use numbers above 256. For instance, I'd like to do  
  
```  
#define GENERATE_CASE(z, n, data) \  
case n :  
  
#define CASES_FROM_TO(first, last) \  
BOOST_PP_REPEAT_FROM_TO(first, BOOST_PP_INC(last), GENERATE_CASE, _)  
  
#define CASES_TO(last) CASES_FROM_TO(1, last)  
  
  
int main()  
{  
int n;  
std::cin >> n;  
switch (n) {  
CASES_TO(260)  
{}  
break;  
  
default:{}break;  
}  
return 0;  
}  
  
````  
  
for values n from 1 to 500. I though the easiest way to reach that would be extending the predefined macros in Boost.Preprocessor to allow arithmetics up to 512 or 1024.

---

## Comment 5

> Username: timblechmann  
> Created at: 2020-04-22 04:29:08 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-617542367  

i'd like to ping on this: i'm also running into the same limitation when it comes to generating string literals from the pp iteration index. works fine below 256, but miserably fails above

---

## Comment 6

> Username: eldiener  
> Created at: 2020-04-26 15:03:01 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-619566282  

Changing the limit involves changing a number of other limits in the library, since the arithmetic and comparison operations use other macros in the library to get things done which have similar limits based on the number 256. While other limits can be changed I am worried that this will slow down the preprocessing to a significant extent. This is the main reason why expanding the numerical limits has not been done.

---

## Comment 7

> Username: timblechmann  
> Created at: 2020-04-26 15:37:46 UTC  
> Updated at: 2020-04-26 15:38:01 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-619571504  

i'm wondering if you have any estimation on the possible performance impact? compared to doing metaprogramming with templates, the prepocessor tends to be very fast.  
  
btw, do you know if this code is auto-generated? it looks a little like it, but i didn't find the sources. people who have control over their boost version could re-generate those files with the limit needed for that project

---

## Comment 8

> Username: eldiener  
> Created at: 2020-04-26 15:47:00 UTC  
> Updated at: 2020-04-26 15:47:34 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-619572903  

The code is not auto-generated. Paul Mensonides wrote the code. The sources are in the library.  
  
The performance impact is hard to determine because it is hard to time. How can one know how long it takes for the preprocessor to complete its phase in normal compilation of a TU. The only way I can think of to time it would be to invoke the preprocessor by itself for some given compiler and try to determine how much time it would take between the invocation and the preprocessor output. This would likely have to be done from the command line as I do not think that bjam has any timing facilities.

---

## Comment 9

> Username: tobias-loew  
> Created at: 2020-04-26 18:26:17 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-619600789  

Maybe we could have pregenerated macros for up to 512 or even 1024, but control their inclusion through macros which have to be enabled explicitly. So, the impact on existing code would be minimal.

---

## Comment 10

> Username: eldiener  
> Created at: 2020-04-26 21:25:34 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-619627407  

That is a possibility and I am not against it. Design wise it seems as if would be possible to let the end-user override the BOOST_PP_LIMIT_MAG macro with his own number, given possibilities of 256, 512, 1024, and then include the set of header files which would work with that number from within the normal header files an end-user uses. The problem as always with the preprocessor is that it is TU bound, with no facility to check that in one TU the setting is the same as another TU. Even in a single TU it is theoretically possible to change the setting from one inclusion to another although it is possible to track such a change and flag such a change as an error.  
  
Another more practical  problem is that it is much work creating sets of macros for, let's say, 512 or 1024, because the change from 256 involves quite a bit of functionality even outside just the numerical calculations. As an example changing the maximum number would also need to change the number of BOOST_PP_WHILE iterations, and this may affect other functionality that uses BOOST_PP_WHILE. There may be also be other things that would need to change.

---

## Comment 11

> Username: timblechmann  
> Created at: 2020-04-27 04:39:45 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-619712153  

> The performance impact is hard to determine because it is hard to time. How can one know how long it takes for the preprocessor to complete its phase in normal compilation of a TU. The only way I can think of to time it would be to invoke the preprocessor by itself for some given compiler and try to determine how much time it would take between the invocation and the preprocessor output. This would likely have to be done from the command line as I do not think that bjam has any timing facilities.  
  
yes, one would have to invoke the compiler to preprocess only (`-E` or `/E`). i'm not sure if it needs to be automated via the buildsystem ...  
  
> The problem as always with the preprocessor is that it is TU bound, with no facility to check that in one TU the setting is the same as another TU.   
  
what problem do you see with it? for my use cases this wouldn't be a problem, but maybe i'm missing something

---

## Comment 12

> Username: eldiener  
> Created at: 2020-04-28 08:04:24 UTC  
> Updated at: 2020-04-28 08:06:16 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-620448253  

The problem is that the user may not realize that in one TU he has the number limit at the default of 256 while in another TU he has set the number limit higher to, let's say, 512. Or that in a single TU he has the number limit at 256 but later in the same TU sets the number limit to, let's say, 512. The problem is really that the ability to change the number limit can be abused. But like everything in programming it is up to the programmer to understand what is offered and to use it correctly.  
  
Nonetheless I am working on a configurable number limit. It is a bit more tedious than difficult, but luckily I discovered some facilities in my text editor which makes it much less tedious. I am not sure what number limits I will allow but right now I am looking at 512, with the possibility of going up to 1024, 2048, or even 4096. The design is that it should be configurable by defining BOOST_PP_LIMIT_MAG to any of those numbers. It will be up to the programmer who chooses a higher limit if the preprocessor slows down just too much to be worth setting a higher limit than the currently default 256. Again I am not sure beyond 512 how high I will go, since the tediousness of having to creating a set of some very large files when the number goes to 1024 or above may just get to me. If you are interested take a look at the internal auto_rec.hpp in the preprocessor\include\boost\preprocessor\detail subdirectory to get an idea what has to be done to expand numbers to 512 and upward. The auto_rec.hpp is used by the BOOST_PP_WHILE macro and the BOOST_PP_WHILE macro is used extensively by number calculations. So auto_rec.hpp, which is a binary search macro algorithm, has to be expanded by another order of 2 each time the number expands by an order of 2 above 256. Nonetheless all this is doable but at a certain point will  create some very large preprocessing header files and may serious slow down the preprocessor when number calculations in the preprocessor get fairly large, ie. 3875 / 279 etc. But I think it is doable and practical to a certain point so I will give it a shot.

---

## Comment 13

> Username: timblechmann  
> Created at: 2020-04-28 08:54:40 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-620473470  

thanks a lot for looking into it

---

## Comment 14

> Username: eldiener  
> Created at: 2020-07-04 20:50:30 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-653810738  

I will not be able to get this functionality in for Boost 1.74 but should complete it for Boost 1.75.

---

## Comment 15

> Username: eldiener  
> Created at: 2020-09-14 22:32:29 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-692348438  

I have implemented the change you asked for in the 'develop' branch of the preprocessor. Please read the topic called "Limitations" and test at your convenience. I have gone beyond your initial suggestion, as you will see when you read the appropriate topic. Comments are welcome. I intend to merge this into the 'master' branch once enough CI and regression testing has been done.

---

## Comment 16

> Username: eldiener  
> Created at: 2020-12-14 16:17:22 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/14#issuecomment-744547770  

The change is in Boost 1.75, so I will close this issue.
