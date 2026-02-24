# #114 - add seralization for std::optional and std::variant [Closed]

> Username: ibmibmibm  
> Created at: 2018-08-08 15:37:31 UTC  
> Updated at: 2023-09-03 20:06:02 UTC  
> Closed at: 2023-09-03 20:06:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/114  

We need a serialization function for std::optional and std::variant.  
Maybe they can be placed in `boost/serialization/optional.hpp`, the file for boost::optional and `boost/serialization/variant.hpp` for boost::variant, or new header files?

---

## Comment 1

> Username: ibmibmibm  
> Created at: 2018-08-08 15:38:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-411451076  

std::optional and std::variant are c++17 only, so it can be implemented in c++17.

---

## Comment 2

> Username: PhilippMuenzel  
> Created at: 2018-12-11 04:42:04 UTC  
> Updated at: 2018-12-18 03:09:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-446070327  

I have the same problem.   
I have to decide now whether I go with std::variant in a new piece of code I'm writing right now for C++17 (so backward compatibility with older compilers is not an issue for this piece of code) or still use boost::variant. I found that the only dependency that keeps me from using std::variant all the way is the ability to boost::serialize it.   
  
I can hack my own crude serialization for the _specific_ variants I'm using, but I'm not enough of an MPL or template metaprogramming wizard to implement serialization of a generic variant.   
  
So now the question is to use boost::variant or std::variant+"kludge", in the hope of being able to remove "kludge" some day - whether and when this day comes is the variable on which the decision depends.

---

## Comment 3

> Username: robertramey  
> Created at: 2018-12-18 02:42:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-448076365  

Traditionally, we've implemented serialization for standard library types within the serialization libraries and for boost library types within the particular boost library.  This is done in order to prevent the obligations of being the boost serialization library maintainer from growing beyond what I can do.  As the standard library keeps importing more boost stuff and creating more stuff, it's hard to keep up with just the standard library.  
  
For some reason long, long ago, I put serialization of boost::optional and boost::variant into the serialization library.  Now that the standard has incorporated their own versions of these libraries things could get pretty confusing.  So what I believe that the current implementations of variant and optional should be moved to their own boost/variant and boost/optional libraries and new versions for the std::variant and std::optional should be placed in the boost serialization library.  
  
Make the move is not too difficult.  But even that is a pain.  The tests should be moved as well.  
  
But then there is the question of implementation of the new libraries.  One could start modifying the current implementations for boost.  But I'm pretty sure their would be more.  The Committee likes to take a boost library and "improve" it so that it's only mostly compatible.  Also, the implementations could make use of more recent versions of C++.  In particular the implementation of boost::variant supports MSVC going back to version 6.0 - very, very, old.  I'm sure implementation could be better today.  Of course this means tweaking the test Jamfile so that the tests for these libraries are run only for more recent compilers.  
  
So this seemingly trivial task turns into the usual can of worms.  I don't know when I'll get to it.  If anyone want's to do it, I recommend that he:  
  
a) fork the library  
b) add your implementation to the code  
c) update the documentation  
d) add a test of serialization  
  
create a pull request.  
  
What's in it for you?  
  
a) you get the enhancement to the library that you need.  
b) you get can put your name on the copyright of the serialization code and the test and a note in the documentation.  
c) so you can proudly wear the title of official boost library maintainer - which can look good on your resume,  
d) the eternal gratitude of those who come after you.

---

## Comment 4

> Username: Winklbauer  
> Created at: 2019-08-21 12:04:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-523426660  

Any update. I am using C++17 structures. We use boost::serialization, but it is getting less easy to use due to lack of support for more modern standard containers like the ones above. C++17 was 2 years ago. Still not supported. Soon we have to look for replacements as it is a lot of overhead to hack support into.

---

## Comment 5

> Username: egorpugin  
> Created at: 2020-02-02 12:53:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-581132684  

+1

---

## Comment 6

> Username: robertramey  
> Created at: 2020-02-02 16:11:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-581150028  

anyone is free to implement it and post it as  a PR.  I think I have a good record of accepting such contributions while at the same time maintaining a high standard.  It's not as easy at it looks.

---

## Comment 7

> Username: PhilippMuenzel  
> Created at: 2020-02-04 17:09:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-582014701  

what's the status of the open pull request(s)? Are they unacceptable? Should we use a fork with the PR merged in to move forward?

---

## Comment 8

> Username: robertramey  
> Created at: 2020-02-12 02:24:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-584986139  

I'll look this weekend

---

## Comment 9

> Username: hdclark  
> Created at: 2020-06-03 16:08:34 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-638297307  

Support for std::variant is a feature I'm eagerly awaiting as well. @robertramey is there anything I can do to help unstick this?

---

## Comment 10

> Username: nightsparc  
> Created at: 2021-04-12 15:51:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-817923932  

Is there any progress so far regarding std::variants inclusion? :)

---

## Comment 11

> Username: Febbe  
> Created at: 2022-01-22 15:05:40 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-1019287881  

Push, this is already one year old, and no one implemented this.

---

## Comment 12

> Username: demihamster  
> Created at: 2022-07-06 14:04:58 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-1176264949  

For anyone looking for a quick workaround for serializing std::variant (std::optional should be conceptually the same), this works for me:  
  
```  
namespace boost  
{  
	namespace serialization  
	{  
		template <class Archive, typename ...Ts>  
		void save(Archive& ar, const std::variant<Ts...>& obj, const unsigned int version)  
		{  
			boost::variant<Ts...> v;  
			std::visit([&](const auto& arg) {  
				v = arg;   
			}, obj);  
  
			ar & v;  
		}  
  
		template <class Archive, typename ...Ts>  
		void load(Archive& ar, std::variant<Ts...>& obj, const unsigned int version)  
		{  
			boost::variant<Ts...> v;  
			ar& v;  
  
			boost::apply_visitor([&](auto& arg) {  
				obj = arg;  
			}, v);  
		}  
  
		template <class Archive, typename ...Ts>  
		void serialize(Archive& ar, std::variant<Ts...>& t, const unsigned int file_version)  
		{  
			split_free(ar, t, file_version);  
		}  
	}  
}  
```  
  
However please note that it still may not work as there is some bug with tracking memory for boost::variant #203 .  
I serialize variants with PODs, so I overrode the header boost/serialization/variant.hpp line   
`typedef mpl::int_< ::boost::serialization::track_always> type;`  
to  
`typedef mpl::int_< ::boost::serialization::track_never> type;`  
  
and now I have correctly serialized std::variant with pod types. I don't know whether it will work for objects that need tracking

---

## Comment 13

> Username: kilasuelika  
> Created at: 2022-09-23 14:20:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-1256277969  

For those who need this, please check @sdebionne 's [std_variant.hpp](https://github.com/boostorg/serialization/pull/148). It runs smoothly on my test though I don't know why it's not merged.

---

## Comment 14

> Username: edisonhello  
> Created at: 2022-10-03 11:47:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-1265324013  

Push. Looking forward to seeing this get merged.

---

## Comment 15

> Username: mbUSC  
> Created at: 2023-06-16 18:32:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-1595121108  

This is sad :(

---

## Comment 16

> Username: PhilippMuenzel  
> Created at: 2023-06-18 14:43:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/114#issuecomment-1596172341  

Just move on. Cereal supports variant and optional since 2019 (v1.3.0).  
Don't waste your time with unmaintained abandonware.
