# #334 X3 examples sio fixes [Closed]

> Username: bonastos  
> Created at: 2017-12-17 22:57:53 UTC  
> Updated at: 2025-05-09 12:11:34 UTC  
> Closed at: 2025-05-09 12:11:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/334  

Hi JOel, Ichoose to fllow the calc7 example of returning a copy.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-17 23:02:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352293196  

Looks good to me.

---

## Comment 2

> Username: djowel  
> Created_at: 2017-12-17 23:12:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352293697  

Hmmm, I don't quite like init_helper. Doesn't this fall into the same trap as before? What if a copy of expression is called before the TU constructs init_helper? I think it's better to have add_keywords a static variable in expression().

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-12-17 23:12:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352293707  

Last time I tried to build x3 examples, calcs were failing to link.  
  
P.S.: CI is not configured to build examples yet. I have not fixed them all so I should probably push the fixes I have, setup Circle-CI with building examples and disable failing ones.

---

## Comment 4

> Username: djowel  
> Created_at: 2017-12-17 23:16:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352293891  

Also, please be consistent with the coding style: open brace '{' in a new line.

---

## Comment 5

> Username: bonastos  
> Created_at: 2017-12-18 06:01:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352333675  

It's true, when  expression gets called brfore the static ctors, you've got a problem. But the point Iwanted to make on the mailing list was that calling add_keywords() before the TU is initialized invokes member finctions on uninitialized symbol table objects. This is the lesser evil,  
To make a separately compiled rule callable before the TU is constructed would (IMHO) mean to construct all objects as part of a meyers singleton.

---

## Comment 6

> Username: djowel  
> Created_at: 2017-12-18 11:27:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352399972  

Well then in that case, it's better to have expression() return a reference, like before. As long as you don't do anything with expression before main, you should be good. And no one should be doing that anyway.

---

## Comment 7

> Username: bonastos  
> Created_at: 2017-12-18 18:27:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352516092  

I suspect we're thinking along the same lines, but have a communication problem ...  
  
Wether expression() returns a reference to a Meyers singleton or a copy is an implementation detail. What matters is that the caller receives a fully initialized object.  
  
The problem I see are the other file local objects in expression.o .  
  
Let's compare the current approach and my implentation if statement.o gets initialized before expression.o.  
  
Current approach  
- statement.o: calls expression()  
  - calls add_keywords();  undefined behaviour, symboltables in expression.o not initialized yet.  
- expression.o: calls ctors of symbol tables. initialze to empty?  
- main(): will eventually call parse on expression_rule object. All bets are off due to the undefined behaviour  
  
My approach  
- statement.o: calls expression()  
- expression.o:   
  - calls ctors of symbol tables.  
  - adds  keywords in ctor of initialization object  
- main(): will eventually call parse on expression_rule object.  
  
This is why I think that the keywords must be added by a file static ctor. (And yes, I agree that it's not pretty)  
  
As you mentioned, one must not use the expression object before main. But that's true for both approaches.

---

## Comment 8

> Username: djowel  
> Created_at: 2017-12-18 22:59:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352585218  

It seems we're having a communication problem alright. I agree with your fix with the keywords added by a file static ctor. But you should also return expression by reference so that the reference will track the changes to the rules. If you do a copy, it *will* return a valid object, but possibly with an empty symbol table.

---

## Comment 9

> Username: bonastos  
> Created_at: 2017-12-19 08:00:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352667739  

I guess my (admittedly limited) understanding of x3 might be wrong.  
  
So please allow me one last question for my education.  
  
- The rule object returned from expression() is distinct from the symbol table objects.  
- The only state the rule object carries is the pointer to it's name.  
- It's type is used in statement.o to select the correct parse_rule<>() instantiation.  
- parse_rule<>() in expression.o references the local symbol table objects.  
- by the tone the rule gets called under main() the symbol tables contain the correct values  
  
What difference does it make if expression() returns a copy or a reference?  
  
I don't mind changing the return values to reference, but I'd like to understand the reason why it's necessary.

---

## Comment 10

> Username: djowel  
> Created_at: 2017-12-19 08:15:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352670738  

Man! You are absolutely correct. I'm the one confused. Haha! I confused QI with X3. Yes. X3 rules are lightweight placeholders and it's the parse_rule that joins the rule placeholders with their definitions. Thanks for setting me straight. Hah! How can I get confused with the libraries that I wrote? :-P  
  
This one is good to go. Pardon the confusion!

---

## Comment 11

> Username: bonastos  
> Created_at: 2017-12-19 08:41:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352676207  

No problem. I'm just glad that my understanding goes  in the right direction :-)

---

## Comment 12

> Username: djowel  
> Created_at: 2017-12-19 08:53:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352679004  

Now I wonder about this CI thing saying "Some checks haven’t completed yet"... "1 errored and 1 pending checks". @Kojoley ?

---

## Comment 13

> Username: Kojoley  
> Created_at: 2017-12-19 10:56:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352712072  

I wrote above that CI is not run examples, so I have cancelled the jobs to not use the limited CI capacity (apparently, it looks like Appveor did not signal GitHub that the job was cancelled).

---

## Comment 14

> Username: djowel  
> Created_at: 2017-12-19 23:59:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352923419  

OK, I guess this is good to go then.

---

## Comment 15

> Username: Kojoley  
> Created_at: 2017-12-20 00:05:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352924456  

Squash all the commits.

---

## Comment 16

> Username: djowel  
> Created_at: 2017-12-20 01:08:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-352934745  

👍

---

## Comment 17

> Username: saki7  
> Created_at: 2025-05-09 12:11:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/334#issuecomment-2866316962  

Even if this PR remains technically valid in 2025, #493 appears to be the more suitable, having been submitted 2 years later. That said, it is unclear why #493 was self-closed despite receiving explicit approval in review.  
  
This PR is simply too old to reconstruct the original context, and it's not realistic to spend time trying to do so now.  
  
Closing as stale. Apologies that we make didn't get this merged back in 2017.   
Feel free to open a new PR based on the current codebase.

---
