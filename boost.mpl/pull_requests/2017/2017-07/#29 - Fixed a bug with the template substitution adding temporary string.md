# #29 Fixed a bug with the template substitution adding temporary string [Merged]

> Username: amarzial  
> Created at: 2017-07-10 10:59:27 UTC  
> Updated at: 2017-07-10 16:50:58 UTC  
> Merged at: 2017-07-10 16:32:35 UTC  
> Closed at: 2017-07-10 16:32:35 UTC  
> Url: https://github.com/boostorg/mpl/pull/29  

(e.g.) with i = 110 the script created 10100 instead:  
line = re.sub(r'20', re.escape(str(i+10)), line.rstrip())  
    20 -> 110  
line = re.sub(r'11', re.escape(str(i + 1)), line.rstrip())  
    110 -> 1010  
line = re.sub(r'10(?![0-9])', re.escape(str(i)), line.rstrip())  
    1100 -> 10100

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-07-10 16:18:29 UTC  
> Url: https://github.com/boostorg/mpl/pull/29#issuecomment-314156982  

I think you mean with i = 100, not 110, in your comment. As in:  
  
(e.g.) with i = 100 the script created 10100 instead:  
line = re.sub(r'20', re.escape(str(i+10)), line.rstrip())  
20 -> 110  
line = re.sub(r'11', re.escape(str(i + 1)), line.rstrip())  
110 -> 1010  
line = re.sub(r'10(?![0-9])', re.escape(str(i)), line.rstrip())  
1010 -> 10100

---

## Comment 2

> Username: DenizThatMenace  
> Created_at: 2017-07-10 16:25:30 UTC  
> Url: https://github.com/boostorg/mpl/pull/29#issuecomment-314159066  

I think your example has some typos but I think I understand what kind of problem it solves.  
  
In your example you probably meant generating (at least) `110` elements which will eventually result in `i = 100` which again results in  
  
* `20` being replaced by `110`,  
* `11` being replaced by `101`, which also replaces the first substitution from `110` to `1010` and  
* alone-standing `10` being replaced by `100`, which again replaces the second substitution from `1010` to `10100`.  
  
(You are probably the first to try to generate more than 100 elements, because nobody else reported this error. :-) )  
  
Probably, someone of the *Boost Community Maintenance* team ( @eldiener ?) can merge this pull-request?

---

## Comment 3

> Username: DenizThatMenace  
> Created_at: 2017-07-10 16:44:22 UTC  
> Url: https://github.com/boostorg/mpl/pull/29#issuecomment-314164490  

:-) I realize, Edward was faster responding than me typing my response.  
Thanks for merging, Edward.

---

## Comment 4

> Username: amarzial  
> Created_at: 2017-07-10 16:50:57 UTC  
> Url: https://github.com/boostorg/mpl/pull/29#issuecomment-314166412  

True, sorry.  
Basically it happens at i=100 every time you put a value bigger than 100 on  
the command line arguments  
  
Il 10 lug 2017 18:18, "Edward Diener" <notifications@github.com> ha scritto:  
  
> I think you mean with i = 100, not 110, in your comment. As in:  
>  
> (e.g.) with i = 100 the script created 10100 instead:  
> line = re.sub(r'20', re.escape(str(i+10)), line.rstrip())  
> 20 -> 110  
> line = re.sub(r'11', re.escape(str(i + 1)), line.rstrip())  
> 110 -> 1010  
> line = re.sub(r'10(?![0-9])', re.escape(str(i)), line.rstrip())  
> 1010 -> 10100  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/mpl/pull/29#issuecomment-314156982>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AOo0N8o33ZLIvz1uHcN2KDjUKQxmhoCxks5sMk7VgaJpZM4OSreB>  
> .  
>

---
