# #175 x3::lit should delegate std::basic_string<Char> instead of Char* [Merged]

> Username: octopus-prime  
> Created at: 2016-02-24 21:04:43 UTC  
> Updated at: 2016-02-25 08:24:51 UTC  
> Merged at: 2016-02-24 22:26:29 UTC  
> Closed at: 2016-02-24 22:26:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/175  

The rule x3::lit("H\x02\x00"s) is currently broken, because x3::lit delegates the c_str() to literal_string.  
So the string is truncated at '\x00'.  
  
Instead we should delegate the string. Then x3::lit("H\x02\x00"s) works fine.

---

## Comment 1

> Username: djowel  
> Created_at: 2016-02-24 22:07:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188473290  

No. lit is just Char const*, no more. If you want std::string, use string.

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2016-02-24 22:10:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188474174  

Do you mean x3::string("H\x02\x00"s)? That would produce a string instead of unused.  
Or do you mean x3::lit(string)? Note: "H\x02\x00"s IS a string...

---

## Comment 3

> Username: djowel  
> Created_at: 2016-02-24 22:14:42 UTC  
> Updated_at: 2016-02-24 22:22:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188477012  

(edit) Yes, I mean, use x3::string(str) instead of x3::lit(str). lit should be as lightweight as possible without heap storage. And yes, it produces an attribute. Not sure how best to approach this. But lit should not have heap store. The best I can suggest is make it unused: omit[string(str)]

---

## Comment 4

> Username: octopus-prime  
> Created_at: 2016-02-24 22:21:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188481078  

x3::omit [ x3::string("H\x02\x00"s) ]  
produces 4k more binary size than  
x3::lit("H\x02\x00"s)  
  
Most of the time people will use x3::lit("foo") instead of x3::lit("foo"s). So most of the time it is lightweight. But if you explicit give a string we should use the string too...  
  
Or not?

---

## Comment 5

> Username: djowel  
> Created_at: 2016-02-24 22:22:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188481356  

Hmmm.. good point, actually

---

## Comment 6

> Username: djowel  
> Created_at: 2016-02-24 22:24:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188482122  

OK, you win :-) Those are very valid points.

---

## Comment 7

> Username: octopus-prime  
> Created_at: 2016-02-24 22:25:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188482423  

Fine :-) Thank you!

---

## Comment 8

> Username: VeXocide  
> Created_at: 2016-02-24 22:37:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188491641  

Would it be an option to accept a `char const [N]` and deduce the length that way, possibly subtracting one if it's zero-terminated?

---

## Comment 9

> Username: djowel  
> Created_at: 2016-02-24 22:41:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188492846  

@octopus-prime, @VeXocide and I discussed that online. I think he makes valid points as well. What do you think? Would you like to give the suggestion a try?

---

## Comment 10

> Username: octopus-prime  
> Created_at: 2016-02-24 22:47:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188494560  

Why not? If x3::lit("H\x02\x00"s) (or something similar containing '\x00') is still valid and matches correctly...  
But I am not sure how to implement it. Perhaps @VeXocide can try it?!

---

## Comment 11

> Username: octopus-prime  
> Created_at: 2016-02-24 23:02:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188500515  

I hope std::basic_string_view will arrive soon... Looks like the think we are looking for...

---

## Comment 12

> Username: mjcaisse  
> Created_at: 2016-02-25 02:45:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188574326  

I'm with @VeXocide on this one. I want to make sure we don't do something that suddenly pulls in heap containers for lit. While x3::lit("H\x02\x00"s) might be interesting, it seems like a waste at that point and you should just write: x3::lit("H\x02\x00").  
  
I'm not seeing the reason for a temporary string.

---

## Comment 13

> Username: djowel  
> Created_at: 2016-02-25 03:46:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188592012  

I keep on searching for the like button!

---

## Comment 14

> Username: octopus-prime  
> Created_at: 2016-02-25 07:40:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188655723  

x3::lit("H\x02\x00") parses 2 chars and x3::lit("H\x02\x00"s) parses ALL 3 chars now.  
And the x3::lit("H\x02\x00"s) rule is no artificial demo case! It's hessian grammar.  
I could revert my code to x3::lit('H') >> x3::lit('\x02') >> x3::lit('\00') - no problem.  
  
But after reverting #175 the x3::lit(string) code becomes obsolete and dangerous:  
  
auto make_danger1()  
{  
  std::string foo("foo");  
  return x3::lit(foo);  
}  
  
auto rule = make_danger();  
  
We can not use the c_str() pointer. Even if we have ownership of the string, somebody could hold a reference to the string and change the string content. So the old c_str() pointer is invalid too.  
  
And what does heap mean?  
  
"H\x02\x00"s allocates memory on heap. It's the decision of x3 user to use a string. So there is heap allocation BEFORE x3::lit() is called.  
  
Now x3::lit() will allocate too, because it takes a copy of the string. That's your point???  
  
So, can we prevent x3::lit() from copying? What's about moving the string?

---

## Comment 15

> Username: octopus-prime  
> Created_at: 2016-02-25 08:16:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188665609  

And even without moving the string...  
  
What is the current string strategy?  
Is it copy_on_write ??  
We never write into the string.  
So no copy occurs.  
So no heap allocation in x3::()  
  
I am not sure... But do we optimize the wrong things - producing dangerous constructs and no gain at all??!!

---

## Comment 16

> Username: mjcaisse  
> Created_at: 2016-02-25 08:18:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188665966  

Hi @octopus-prime  -  
  
The fact that `x3::lit("H\x02\x00")` is parsing 2 chars is just a because `x3:lit` lacks an overload for `char const [N]` as @VeXocide suggested.  
  
If `x3::lit` does implement the overload, your use case provides no benefit as far as I can see. It does create a `std::string` which is a waste if all you wanted to express is: `x3::lit("H\x02\x00")`.  
  
I'm actually not arguing to remove the merged patch. It fixes a real problem. I'm simply suggesting that your use case is better resolved with the `char const [N]` overload instead of `x3::lit` taking a `std::string`.  
  
The general heap concern is that if solutions exist that do not require heap based objects, they should be an option. In this case the source of your problem (escaped null) can be solved with a `std::string` or it could be resolved with knowing the length of the literal string. We have use-cases that target bare wire (no-OS) and therefore making sure the design and implementation support features without relying on the specific standard containers (those lacking allocator specialization) is a goal.

---

## Comment 17

> Username: djowel  
> Created_at: 2016-02-25 08:19:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188666097  

No, don't get the suggestion wrong, @octopus-prime. It's not suggested that the patch be reverted. What's suggested is that the lit taking in literals (e.g. x3::lit("H\x02\x00")) do the right thing and take in the char const\* PLUS the length.

---

## Comment 18

> Username: octopus-prime  
> Created_at: 2016-02-25 08:22:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188666545  

Ok! I am looking forward to it :-) Who will implement it?

---

## Comment 19

> Username: djowel  
> Created_at: 2016-02-25 08:22:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188666748  

seems i will. there's more to it, after taking a deeper look. i need to tweak some lower level traits dealing with such things.

---

## Comment 20

> Username: octopus-prime  
> Created_at: 2016-02-25 08:23:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188666944  

That's fine! Thank you :-)

---

## Comment 21

> Username: djowel  
> Created_at: 2016-02-25 08:24:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/175#issuecomment-188667128  

Many thanks, @octopus-prime! Say Hi to bumble-bat !

---
