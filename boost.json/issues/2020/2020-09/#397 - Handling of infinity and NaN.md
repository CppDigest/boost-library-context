# #397 - Handling of infinity and NaN [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 17:18:04 UTC  
> Updated at: 2023-06-02 08:55:28 UTC  
> Closed at: 2023-06-02 08:55:28 UTC  
> Url: https://github.com/boostorg/json/issues/397  

Specifically, if the user puts an inf or NaN into a value, what happens on serialization? We need to specify what should happen on serialization. And if we output "inf" and "nan" then we need to specify what happens on parsing. And we might need to provide a configuration option for allowing it / disallowing it (on parse).

---

## Comment 1

> Username: accelerated  
> Created at: 2021-12-16 22:15:57 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-996235691  

I have also bumped into this while testing. `NaN` is serialized as such but then it actually throws as it's not recognized by the parser.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-02-04 14:42:45 UTC  
> Updated at: 2022-02-04 14:46:15 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1030051073  

Currently it produces invalid json (https://gcc.godbolt.org/z/31ebz6b9q).  
  
I personally serialize to reuse it in javascript (and I imagine that would be quite common) so just putting in a string would work out alright for that. `"Infinity"  * 42  == Infinity`, because javascript.  
  
Alternatively, 1e800 is Infinity (-1e800) in JS, plus NaN could be null. I don't know how well that works for other languages though.

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-02-04 16:53:28 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1030166725  

@pdimov suggested using a struct like this:  
```  
struct serialize_options {  
  string_view pinf;  
  string_view ninf;  
  string_view nan;  
};  
```  
to control serializer's behaviour.  
  
Usage would be something like  
```  
os << serialize(jv, { .pinf = "null", .ninf = "null", .nan = "null" });  
```  
If a `string_view`s is empty and the corresponding special value is encountered the serializer would error-out.  
  
@vinniefalco has added that sometimes users would want to know if a special value has been encountered even when they do provide a string to serialize it as. So, maybe serializer should always produce an `error_code` when a special value is encountered.  
  
Last thing to consider: what should be the default behaviour, i.e. when `serialize_options` is not used?

---

## Comment 4

> Username: pdimov  
> Created at: 2022-02-04 20:01:18 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1030308005  

The default should be the same as passing `{}`, that is, throw/error.  
  
This may not technically be the most correct default, but it's the most intuitive given the interface. The rule is, if you provide a representation for a special value, this representation is used, otherwise, there's an error on encountering it.  
  
No, I don't think the serializer should error out even when a representation is supplied.

---

## Comment 5

> Username: madmongo1  
> Created at: 2022-02-04 20:25:08 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1030323865  

I agree with Klemens here. JSON is closely linked with the value models of  
JavaScript and to a lesser extent, python.  
  
If you’re going to handle non-numbers at all, the treatment should model  
that of JavaScript, otherwise interoperability becomes a headache - you’ll  
have to remember two rule sets and code up the conversions between the two.  
  
On Fri, 4 Feb 2022 at 15:42, Klemens Morgenstern ***@***.***>  
wrote:  
  
> Currently it produces invalid json (https://gcc.godbolt.org/z/31ebz6b9q).  
>  
> I personally serialize to reuse it in javascript (and I imagine that would  
> be quite common) so just putting in a string would work out alright for  
> that. "Infinity" * 42 == Infinity, because javascript.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/json/issues/397#issuecomment-1030051073>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSNBCZU42D5W5HDAJL3UZPQXFANCNFSM4RWBEF5A>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>  
--   
Richard Hodges  
***@***.***  
office: +44 2032 898 513  
home: +376 861 195  
mobile: +376 380 212

---

## Comment 6

> Username: pdimov  
> Created at: 2022-02-04 20:58:19 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1030343945  

> and to a lesser extent, python.  
  
That's exactly what the current behavior is.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-02-04 21:06:43 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1030349340  

> @vinniefalco has added that sometimes users would want to know if a special value has been encountered even when they do provide a string to serialize it as. So, maybe serializer should always produce an error_code when a special value is encountered.  
  
But this is not the same thing. A non-successful error code is non-recoverable. You can't "always produce an error code when a special value is encountered." If we are going to aggregate statistics on the number of non-compliant numbers encountered, they need to be communicated out of band. For example by calling a separate function which returns the statistics, when serialization is complete. Since this can always be added later as a feature if/when needed, without affecting the design of the original topic of this issue, we don't have to worry about it.

---

## Comment 8

> Username: x10000year  
> Created at: 2022-11-14 07:35:35 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1313215619  

what about deserialize?

---

## Comment 9

> Username: grisumbras  
> Created at: 2022-11-14 19:54:40 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1314293675  

You mean parse? JSON spec doesn't support infinities and NaNs. If it did, this issue wouldn't have existed.

---

## Comment 10

> Username: grisumbras  
> Created at: 2023-05-04 13:11:51 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1534758049  

An approach that is very simple to implement that will allow us to support infinity (but not NaN) is to serialize infinity as 1e999. Many implementations (including ours) parse that as infinity.

---

## Comment 11

> Username: pdimov  
> Created at: 2023-05-04 14:01:03 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1534838136  

1e999 is a valid long double, so we should avoid it and use, e.g. 1e99999.

---

## Comment 12

> Username: grisumbras  
> Created at: 2023-05-04 14:06:58 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1534847852  

To repeat what was said by @pdimov on Slack, these are the options:  
  
* Infinity, -Infinity, NaN  
* 1e99999, -1e99999, null  
* 1e99999, -1e99999, throw  
* throw, throw, throw  
  
We probably don't want to throw here. This leaves first two options. I personally prefer option 2 ( 1e99999, -1e99999, null), because it's valid JSON. Option 1 has the benefit of not changing source values to something different, and this non-standard syntax is supported by several popular implementations (Python and RapidJSON with an option).  
  
So, how about option 2 by default and option 1 enabled explicitly?

---

## Comment 13

> Username: pdimov  
> Created at: 2023-05-04 14:14:35 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1534861123  

Fine with me in principle. How would this look like in the API?

---

## Comment 14

> Username: grisumbras  
> Created at: 2023-05-04 14:17:21 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1534866090  

Something like  
```c++  
auto s = serialize( jv, {.explicit_special_numbers=true} );  
auto jv = parse( s, {.explicit_special_numbers=true} );  
```

---

## Comment 15

> Username: pdimov  
> Created at: 2023-05-04 18:08:30 UTC  
> Url: https://github.com/boostorg/json/issues/397#issuecomment-1535199927  

The symmetry is appealing; maybe this is better than my suggestion above.
