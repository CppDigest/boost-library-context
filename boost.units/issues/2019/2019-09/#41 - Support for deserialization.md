# #41 - Support for deserialization [Open]

> Username: FlorentTomi  
> Created at: 2019-09-27 14:10:45 UTC  
> Updated at: 2019-10-04 19:03:17 UTC  
> Url: https://github.com/boostorg/units/issues/41  

Hello,  
  
Right now, only serialization is possible. Is there any reason deserialization is not implemented ?  
I'm on a project where I want to extends the "safety" of units to the data in our database.  
For example, I would like to be able to do that:  
```  
quantity <si::length> l0 = 3.14 * si::meter;  
stream << l0; // "3.14 m"  
  
quantity <si::area> a;  
stream >> a; // throw an exception  
  
quantity <si::length> l1;  
stream >> l1; // OK  
```  
  
Thank you!

---

## Comment 1

> Username: correaa  
> Created at: 2019-09-30 23:25:00 UTC  
> Url: https://github.com/boostorg/units/issues/41#issuecomment-536792610  

This is more input/output (io) than and serialization. This is not implemented probably because it requires a pretty complicated parser of units and the result can be error prone. For example, in the most flexible cases requires numeric/unit conversions. It is not clear if inconsistent input out would allowed and the gain is marginal after a lot of work and a lot of assumptions.   
  
Having said that, Boost.Serialization supports serialization and deserialization of Boost.Units but not for human consumption.

---

## Comment 2

> Username: FlorentTomi  
> Created at: 2019-10-01 07:34:34 UTC  
> Url: https://github.com/boostorg/units/issues/41#issuecomment-536909285  

I'm not really talking about parsing, for example, "10 m" into a feet quantity, but at least having the simple case of allowing parsing "10 m" into a meter quantity.   
Shouldn't it be only a matter of checking the prefix, and parsing the numeric value ?

---

## Comment 3

> Username: correaa  
> Created at: 2019-10-04 03:38:28 UTC  
> Url: https://github.com/boostorg/units/issues/41#issuecomment-538215746  

> I'm not really talking about parsing, for example, "10 m" into a feet quantity, but at least having the simple case of allowing parsing "10 m" into a meter quantity.  
> Shouldn't it be only a matter of checking the prefix, and parsing the numeric value ?  
  
Not really, there are a lot of variables that can spoil the apparent simplicity. For example the format of the output depends on the files that are included for example  
```  
std::cout << 3.14*si::joule << std::endl   
```  
outputs "3.14 m^2 kg s^-2". but if you `#include<boost/units/systems/si/io.hpp>` it will output `3.14 J`. So which of the two should be checked against? not to mention other surprising behavoir relative to scaled quantities (for example an input with `3.14e6 µJ`).  
  
I would say that in the best imaginable case human input will always give surprising results, the main problem is that the parser will have to have a lot of compile time information about all possible ways to interpret and disambiguate unit initials, etc.  
  
Having said that, yes you can in principle be very strict and only accept input that exactly matches the output, at least for the unit portion. Again, with the caveat that many configuration variables can affect the output format. But if you promise the input and output will occur with the same compilation configuation perhaps you can accept this implementation below.  
  
This solves your problem the way you want but it also shows the exceeding number of assumptions made but this simple parser. This is not at the quality or generality level expected for a library like Boost.Units, so it is probably a good thing that they decided to leave input from streams unimplemented.  
  
```  
namespace boost{   
namespace units{ // I am stomping over boost.units namespace for simplicity  
template<class U, class T>  
std::istream& operator>>(std::istream& is, quantity<U, T>& q){  
	typename quantity<U, T>::value_type val; is >> val;  
	std::string const expected = to_string(U{});  
	std::string tag(expected.size()+1, 0); is.get(&tag[0], tag.size()+1);  
	if(tag[0]==' ' and tag.substr(1)==expected){  
		q = quantity<U, T>::from_value(val);  
	}else{  
	//	std::cerr<<"error expected "<< expected <<", got "<< tag <<std::endl;  
		is.setstate(std::ios::failbit);  
	}  
	return is;  
}  
}}  
  
int main(){  
	{  
		quantity<si::length> l = 3.14*si::meter;  
		std::stringstream ss; ss << l << "abc" << std::endl;  
  
		quantity<si::length> l2; ss >> l2;   
		assert(ss and l == l2);  
  
		std::string check; ss >> check; assert(check=="abc");  
	}  
	{  
		quantity<si::energy> e = 5.24*si::joule;  
		std::stringstream ss; ss << e << "abc" << std::endl;  
		// promise not do funny stuff between write and read, like including new units/io headers  
		quantity<si::energy> e2; ss >> e2;   
		assert(ss and e == e2);  
  
		std::string check; ss >> check; assert(check=="abc");  
	}  
}  
```

---

## Comment 4

> Username: correaa  
> Created at: 2019-10-04 04:16:14 UTC  
> Updated at: 2019-10-04 04:35:49 UTC  
> Url: https://github.com/boostorg/units/issues/41#issuecomment-538223631  

This reminded me that I have some code that I did not update to the gitlab repository with my custom extensions to boost.units. The code I presented above is now here:  
https://gitlab.com/correaa/boost-units/blob/master/io/input.hpp  
  
This also includes _another_ feature which I think is more sane than general input, which it to leave the user to generate dimensionless expressions in which the normal input can be performed.  
  
For example,   
  
suppose you know a certain input ("5") is in micro meters. Then you get the variable in meters.  
```  
quantity<si::length> l; in >> l/(si::micro*si::meter); assert( l == 5e-6*si::meter );  
```  
So as long as you can generate a dimensionless expression (ok, by division only for the moment) then this will work.  I think this is better than investing in a general input parser for a lot of work that is going be thrown away since the compile time type cannot be changed anyway.

---

## Comment 5

> Username: FlorentTomi  
> Created at: 2019-10-04 13:41:43 UTC  
> Url: https://github.com/boostorg/units/issues/41#issuecomment-538401799  

Thank you for the code and these complete answers, this should do the trick.  
  
If I were to give my two cents, I would say that the way IO is handled by the library is either too complicated or too "sneaky". Just including a header shouldn't, imho, change the results of an operation. If it's just to change the way units output themselves, wouldn't it be just fine, for example, to create new stream flags ?  
  
I agree with you that maybe, handling dimensionless quantities are more sane, as IO should be well documented and as clear as possible.

---

## Comment 6

> Username: correaa  
> Created at: 2019-10-04 19:03:16 UTC  
> Url: https://github.com/boostorg/units/issues/41#issuecomment-538522870  

On Fri, Oct 4, 2019 at 6:41 AM Florent TOMI <notifications@github.com>  
wrote:  
  
> Thank you for the code and these complete answers, this should do the  
> trick.  
>  
> If I were to give my two cents, I would say that the way IO is handled by  
> the library is either too complicated or too "sneaky". Just including a  
> header shouldn't, imho, change the results of an operation. If it's just to  
> change the way units output themselves, wouldn't it be just fine, for  
> example, to create new stream flags ?  
>  
I am not an expert in iostreams (and I want to know the least possible),  
but yes, maybe stream flags is the way to go. And why not "locales"? :P  
  
> I agree with you that maybe, handling dimensionless quantities are more  
> sane, as IO should be well documented and as clear as possible.  
>  
Yes, dimensionless expression at least produce is no ambiguities. This  
technique moves enough runtime-work to compile-time work to make the  
runtime-code sane and minimal and well defined in scope.  
  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/units/issues/41?email_source=notifications&email_token=AAXICU4E3SUIUQFJ5WLC4QDQM5BZRA5CNFSM4I3G5JL2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEALVYBY#issuecomment-538401799>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAXICU66B3YVYM25X2CNBIDQM5BZRANCNFSM4I3G5JLQ>  
> .  
>
