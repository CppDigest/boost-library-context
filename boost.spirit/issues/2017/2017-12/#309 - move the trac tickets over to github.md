# #309 - move the trac tickets over to github [Closed]

> Username: octopus-prime  
> Created at: 2017-12-04 12:02:04 UTC  
> Updated at: 2025-05-10 01:54:56 UTC  
> Closed at: 2025-05-10 01:54:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/309  

Move issues from  
https://svn.boost.org/trac10/query?status=assigned&status=new&status=reopened&component=spirit&col=id&col=summary&col=status&col=type&col=milestone&col=component&order=priority  
to here.

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-12-04 12:25:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-348947732  

A (minimal) GitHub API and code to migrate Trac tickets into GitHub Issues  
https://github.com/STEllAR-GROUP/github-migrate-trac-tickets

---

## Comment 2

> Username: octopus-prime  
> Created at: 2017-12-04 18:25:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-349056462  

The suggested migrating tool (https://github.com/STEllAR-GROUP/github-migrate-trac-tickets) is working on the database file (e.g. xyz-trac.db).  
  
So somebody needs access to the system/database.  
  
May be a xmlrpc based solution would be a better choice, if https://svn.boost.org/trac10 supports xmlrpc and there is a runnable migrating tool (e.g. https://github.com/acdha/migrate-trac-issues-to-github) ?!

---

## Comment 3

> Username: mjcaisse  
> Created at: 2017-12-04 18:35:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-349059702  

I have access to anything we might need. Whatever the group thinks is the right solution, just let me know and we can handle it.

---

## Comment 4

> Username: djowel  
> Created at: 2017-12-04 22:42:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-349131501  

I'm fine with anything as long as it gets the job done :)

---

## Comment 5

> Username: octopus-prime  
> Created at: 2017-12-04 23:04:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-349136621  

Do we want to migrate all tickets (closed tickets too) or only the new/reopened tickets?!  
So the list of migrated tickets should be exactly the tickets from  
https://svn.boost.org/trac10/query?status=assigned&status=new&status=reopened&component=spirit&col=id&col=summary&col=status&col=type&col=milestone&col=component&order=priority  
?

---

## Comment 6

> Username: djowel  
> Created at: 2017-12-04 23:22:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-349140886  

Good question. I think we need to migrate only the open tickets.

---

## Comment 7

> Username: octopus-prime  
> Created at: 2017-12-05 11:03:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-349271168  

@mjcaisse Do you want to try migrating, because you have the needed permissions? Otherwise I could try to play with migrating tools (and a private repo of course), if you can provide the needed trac.db file.

---

## Comment 8

> Username: Kojoley  
> Created at: 2017-12-05 13:58:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-349311585  

If the goal is to close the trac tickets, why not just solve the problem by fixing the bugs? Moreover, some of the problems reported and still opened on trac are already fixed.

---

## Comment 9

> Username: djowel  
> Created at: 2017-12-05 15:38:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-349341983  

We can probably start by looking for the tickets that we can already close.

---

## Comment 10

> Username: Kojoley  
> Created at: 2017-12-13 21:54:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-351537352  

The list of trac tickes that should be closed:  
  
https://svn.boost.org/trac10/ticket/12817  
https://svn.boost.org/trac10/ticket/6750  
https://svn.boost.org/trac10/ticket/9613  
https://svn.boost.org/trac10/ticket/12166  
https://svn.boost.org/trac10/ticket/13093  
https://svn.boost.org/trac10/ticket/12084  
https://svn.boost.org/trac10/ticket/12473  
https://svn.boost.org/trac10/ticket/8556  
https://svn.boost.org/trac10/ticket/8447  
https://svn.boost.org/trac10/ticket/8446  
https://svn.boost.org/trac10/ticket/12928  
https://svn.boost.org/trac10/ticket/11410  
https://svn.boost.org/trac10/ticket/11530  
https://svn.boost.org/trac10/ticket/11925  
https://svn.boost.org/trac10/ticket/13240  
https://svn.boost.org/trac10/ticket/12349  
https://svn.boost.org/trac10/ticket/8949  
https://svn.boost.org/trac10/ticket/6869  
https://svn.boost.org/trac10/ticket/6751  
https://svn.boost.org/trac10/ticket/13311  
https://svn.boost.org/trac10/ticket/11115  
https://svn.boost.org/trac10/ticket/5270

---

## Comment 11

> Username: djowel  
> Created at: 2017-12-13 22:33:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-351546945  

Wonderful! I'll do the closing today, @Kojoley

---

## Comment 12

> Username: djowel  
> Created at: 2017-12-13 23:11:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-351555234  

OK. Done. Splendid job, @Kojoley. I would've asked for your access to Trac, but we're moving to Github anyway, so I'll just do the closing.

---

## Comment 13

> Username: mjcaisse  
> Created at: 2017-12-13 23:47:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-351562206  

@octopus-prime I missed your message. Sorry about the delay. I can look in the next couple days at doing this for the open tickets.

---

## Comment 14

> Username: Kojoley  
> Created at: 2017-12-14 19:24:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-351810442  

A new list of to be closed trac tickets:  
  
https://svn.boost.org/trac10/ticket/11450  
https://svn.boost.org/trac10/ticket/11605  
https://svn.boost.org/trac10/ticket/11606  
https://svn.boost.org/trac10/ticket/8537  
https://svn.boost.org/trac10/ticket/12366  
https://svn.boost.org/trac10/ticket/12497  
https://svn.boost.org/trac10/ticket/7161  
https://svn.boost.org/trac10/ticket/9456  
https://svn.boost.org/trac10/ticket/9457  
https://svn.boost.org/trac10/ticket/9238

---

## Comment 15

> Username: djowel  
> Created at: 2017-12-15 00:00:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-351873091  

Rockin'! 👍

---

## Comment 16

> Username: Kojoley  
> Created at: 2017-12-17 23:38:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-352295374  

A fresh one:  
  
https://svn.boost.org/trac10/ticket/6868  
https://svn.boost.org/trac10/ticket/12639  
https://svn.boost.org/trac10/ticket/11540  
https://svn.boost.org/trac10/ticket/8577  
https://svn.boost.org/trac10/ticket/9135  
https://svn.boost.org/trac10/ticket/8984  
https://svn.boost.org/trac10/ticket/9901  
https://svn.boost.org/trac10/ticket/8870

---

## Comment 17

> Username: djowel  
> Created at: 2017-12-17 23:43:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-352295679  

And it just keeps on coming! :-) 👍

---

## Comment 18

> Username: Kojoley  
> Created at: 2017-12-17 23:56:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-352296458  

Below a hundred trac tickets. Whoa!

---

## Comment 19

> Username: djowel  
> Created at: 2017-12-18 00:00:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-352296674  

You did it, @Kojoley! Amazing!

---

## Comment 20

> Username: Kojoley  
> Created at: 2017-12-18 16:18:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-352475852  

https://svn.boost.org/trac10/ticket/6912  
https://svn.boost.org/trac10/ticket/11864  
https://svn.boost.org/trac10/ticket/5895  
https://svn.boost.org/trac10/ticket/12388  
https://svn.boost.org/trac10/ticket/9924 wontfix  
https://svn.boost.org/trac10/ticket/10185  
https://svn.boost.org/trac10/ticket/7883  
https://svn.boost.org/trac10/ticket/5983  
https://svn.boost.org/trac10/ticket/12599  
https://svn.boost.org/trac10/ticket/9152

---

## Comment 21

> Username: djowel  
> Created at: 2017-12-18 23:31:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-352591449  

👍  Closed down more tickets.

---

## Comment 22

> Username: Kojoley  
> Created at: 2017-12-23 00:33:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-353695478  

A new truck of trac tickets here:  
  
https://svn.boost.org/trac10/ticket/5713  
https://svn.boost.org/trac10/ticket/7371  
https://svn.boost.org/trac10/ticket/7048 invalid  
https://svn.boost.org/trac10/ticket/7946 wontfix  
https://svn.boost.org/trac10/ticket/8041  
https://svn.boost.org/trac10/ticket/7638  
https://svn.boost.org/trac10/ticket/6247 obsolete

---

## Comment 23

> Username: djowel  
> Created at: 2017-12-23 00:42:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-353696073  

Done for this truck of trac tickets :-)

---

## Comment 24

> Username: Kojoley  
> Created at: 2017-12-24 22:58:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-353806550  

A few more:  
  
https://svn.boost.org/trac10/ticket/9022 invalid  
https://svn.boost.org/trac10/ticket/11531  
https://svn.boost.org/trac10/ticket/11614  
https://svn.boost.org/trac10/ticket/10144 Duplicate of trac [5872](https://svn.boost.org/trac10/ticket/5872)  
https://svn.boost.org/trac10/ticket/9803 invalid

---

## Comment 25

> Username: djowel  
> Created at: 2017-12-25 23:29:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-353895586  

Done. 👍

---

## Comment 26

> Username: Kojoley  
> Created at: 2018-02-19 22:46:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-366820404  

Not a big one:  
  
https://svn.boost.org/trac10/ticket/8715  
https://svn.boost.org/trac10/ticket/6770  
https://svn.boost.org/trac10/ticket/10109  
https://svn.boost.org/trac10/ticket/12322  
https://svn.boost.org/trac10/ticket/10681  
https://svn.boost.org/trac10/ticket/8268

---

## Comment 27

> Username: djowel  
> Created at: 2018-02-19 23:16:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-366824810  

Done. 👍

---

## Comment 28

> Username: Kojoley  
> Created at: 2018-03-15 22:49:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-373547476  

I feel like an archaeologist, like I am speaking with a rock at commenting ancient tickets :laughing:  
  
https://svn.boost.org/trac10/ticket/10752  
https://svn.boost.org/trac10/ticket/12085  
https://svn.boost.org/trac10/ticket/11616  
https://svn.boost.org/trac10/ticket/9509  
https://svn.boost.org/trac10/ticket/7825  
https://svn.boost.org/trac10/ticket/13470  
https://svn.boost.org/trac10/ticket/6046

---

## Comment 29

> Username: djowel  
> Created at: 2018-03-15 22:57:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-373549125  

> I feel like an archaeologist, like I am speaking with a rock at commenting ancient tickets 😆  
  
:-) Seems like it.

---

## Comment 30

> Username: Kojoley  
> Created at: 2018-11-22 12:55:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-441022360  

Since trac was closed for new tickets it also does not allow commenting still open tickets, it is not a big problem as nobody respond to activity in old tickets, but now I cannot leave a message with PR/commit when the bug was fixed.  
  
---  
  
A bunch of tickets to be closed:  
  
https://svn.boost.org/trac10/ticket/13476  
https://svn.boost.org/trac10/ticket/7460 duplicate of [6940](https://svn.boost.org/trac10/ticket/6940)  
https://svn.boost.org/trac10/ticket/13615 invalid  
https://svn.boost.org/trac10/ticket/12138  
https://svn.boost.org/trac10/ticket/13531  
https://svn.boost.org/trac10/ticket/6126  
https://svn.boost.org/trac10/ticket/13370 invalid  
https://svn.boost.org/trac10/ticket/12860 fixed in ce7be3a2f93224d2115fdb1a645d1acd83039ea6

---

## Comment 31

> Username: djowel  
> Created at: 2018-11-22 13:57:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-441038405  

Done!

---

## Comment 32

> Username: Kojoley  
> Created at: 2018-11-23 14:42:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-441257876  

@djowel if it is not hard for you, please leave a comment with the PR/commit information that fixed it (I am providing them with the list) when you are closing tickets, because I cannot do it myself (https://github.com/boostorg/spirit/issues/309#issuecomment-441022360).  
  
The new list:  
  
https://svn.boost.org/trac10/ticket/12427 fixed in boost-1.65 (not a spirit bug)  
https://svn.boost.org/trac10/ticket/7299 fixed in 3ba8b8b8ed3a88ea2ba168379d019dbb9c4a4e77  
https://svn.boost.org/trac10/ticket/5935 invalid  
https://svn.boost.org/trac10/ticket/8440 fixed in 304a10ff33599f35637450501fb327630c4857a7  
https://svn.boost.org/trac10/ticket/9737 fixed in #422  
https://svn.boost.org/trac10/ticket/11514 fixed in #146  
https://svn.boost.org/trac10/ticket/8578 invalid

---

## Comment 33

> Username: djowel  
> Created at: 2018-11-24 05:12:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-441344408  

Done.

---

## Comment 34

> Username: Kojoley  
> Created at: 2021-03-14 19:02:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-798960742  

https://svn.boost.org/trac10/ticket/4779 broken invalid test was removed  
https://svn.boost.org/trac10/ticket/6050 invalid: breaking change, examples are inconclusive for a feature request as the same can be done with exceptions, semantic actions or custom parsers.  
https://svn.boost.org/trac10/ticket/7539 could not reproduce with boost-1.51 and clang 3.4, probably a fixed bug in libc++  
https://svn.boost.org/trac10/ticket/8699 fixed in https://github.com/boostorg/spirit/issues/415  
https://svn.boost.org/trac10/ticket/8778 since https://github.com/boostorg/spirit/pull/648 (will be in boost-1.76) we no longer use Boost.Math  
https://svn.boost.org/trac10/ticket/9705 wontfix: request to support an ancient compilers workaround without a patch  
https://svn.boost.org/trac10/ticket/10266 fixed in https://github.com/boostorg/spirit/commit/147f4296735ee90efe70acd0dd53ef55ab0c906d  
https://svn.boost.org/trac10/ticket/11726 fixed in https://github.com/boostorg/spirit/pull/161  
https://svn.boost.org/trac10/ticket/7785, https://svn.boost.org/trac10/ticket/11056 fixed in https://github.com/boostorg/spirit/pull/586

---

## Comment 35

> Username: djowel  
> Created at: 2021-03-14 19:56:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/309#issuecomment-798969227  

Wonderful! Done.
