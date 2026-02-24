# #46 Winter is coming... [Merged]

> Username: aminiussi  
> Created at: 2017-02-23 13:08:05 UTC  
> Updated at: 2018-02-22 21:11:49 UTC  
> Merged at: 2018-02-22 21:10:20 UTC  
> Closed at: 2018-02-22 21:10:20 UTC  
> Url: https://github.com/boostorg/mpi/pull/46  

Right now, develop passes all its testing with both develop and master branch of serialization.  
Having both is a security I think.  
  
They pass on g++5.2.0 OpenMpi on ubuntu and Intel 15.0.3 (based on g++4.4.7 lib, pretty antique, which is reassuring for compatibility) default mode and intel MPI on CentOS  
 I think we should check both default compile option and explicit C++11 activation.  
  
I can try intel 17 on CentOS7.  
  
@Belcourt I think you have access to other platforms, I'm afraid I do not have access to MS environment.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2017-02-23 15:49:29 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-282030033  

Alain,  
  
I prefer to cherry pick into master, but this does provide a good reference list of commits to merge.  Unfortunately, I see many issues still with MPI on develop with Sandia testers.  I hope to get some time shortly to deal with them.  
  
Noel

---

## Comment 2

> Username: aminiussi  
> Created_at: 2017-02-23 16:52:14 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-282050688  

I'm not sure cherry pick is viable here. At some point before release we need to make sure we have the exact same code on develop and master, and the easiest way is to test develop and merge it. Cherry pick has already been made when contributing to develop through PR.  
And if there are still issues on develop they need to be fixed anyway, we can't have people contributing PR on develop if it's not fit.  
What are the issues on Sandia ?

---

## Comment 3

> Username: aminiussi  
> Created_at: 2017-02-23 17:10:30 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-282056258  

The Sandia output on the boost web pages are not very clear (not actual error message).  
Except for a archive link error with recent icpc.  
I'll try intel 16 and 17 with those flags.

---

## Comment 4

> Username: Belcourt  
> Created_at: 2017-02-23 18:49:15 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-282083607  

Hi Alain,  
  
It’s the same problem we’ve talked about.  Basically the Boost.MPI testing is toast for all older compiler + mpi combinations that don’t support at least c++11.  This is a show stopper for me.  I made a couple of suggestions on how we could both retain support for older compilers as well as move Boost.MPI ahead to use new c++11 and later features.  This is a really important issue, I’m open to suggestions.  
  
Will you be at C++now in May?  Perhaps we should have a small group meeting with those interested in Boost.MPI, so we can try to work through some of these issues?  
  
Noel  
  
On Feb 23, 2017, at 10:10 AM, Alain Miniussi <notifications@github.com<mailto:notifications@github.com>> wrote:  
  
  
The Sandia output on the boost web pages are not very clear (not actual error message).  
Except for a archive link error with recent icpc.  
I'll try intel 16 and 17 with those flags.  
  
—  
You are receiving this because your review was requested.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/mpi/pull/46#issuecomment-282056258>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AA65bV3_cZ2j-Qyci7SRM8AoyDVA16Syks5rfb2HgaJpZM4MJ8VO>.

---

## Comment 5

> Username: aminiussi  
> Created_at: 2017-02-23 21:42:45 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-282130611  

I did pass the tests with no C++11 option with **g++4.4.7 (2012)**. I guess at some point, one can chose to upgrade the compiler or stick to an old boost.  
But the thing is that Boost.MPI does not uses advanced c++11 so I can't see it as a blocking point. I suspect serialization uses more advanced features, and we cannot go below Serialization anyway. So if Serialize passes the test, so should we (and if it doesn't there's nothing we can do). So there is nothing we can't fix.  
  
I do not see anything older than **g++4.4.7** in [http://www.boost.org/development/tests/develop/developer/mpi.html](http://www.boost.org/development/tests/develop/developer/mpi.html) and the failed test do not show any compilation related error messages, so I guess that's another platform ?   
I suggest the following:  
- If  you can provide me the compiler version and I can install it (ideally a gnu, although I might be able to find an old Intel.. I can get it to pass the testing).  
- if not, can you provide me the error messages and we'll iterate from there.  
  
I'm very unlikely to be in C++Now, just getting a passport done in due time would be very difficult. But we could find another way I guess.

---

## Comment 6

> Username: aminiussi  
> Created_at: 2017-02-24 10:33:53 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-282259464  

I just got a few syntax error in the test source code due to bracket initialisation with g++4.3.1(released on june 2008).  
I'm fixing those.  
g++4.3.1 is significantly older than the earlier linux compiler mentioned on the boost 1.63 release notes.  
Is your compiler older than 2008 ?  
Thanks

---

## Comment 7

> Username: aminiussi  
> Created_at: 2017-02-24 12:40:51 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-282282050  

g++4.3.1 passes with no warning with #47 PR.  
Do you still have problems with that one ? If yes, can you provided me with the error message ?  
  
Thanks

---

## Comment 8

> Username: aminiussi  
> Created_at: 2017-02-27 09:02:17 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-282663544  

3.4.4 does not pass, but serilization library does not compile either so that's not a real issue.

---

## Comment 9

> Username: aminiussi  
> Created_at: 2017-02-28 12:51:34 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-283030890  

With the last update of #47, test passes with g++4.0.3, which is really old (10years+)  
Older version (like 3.x.y) are not even tested on serialization (there is a thread in the mailing list regarding the issue).  
So, unless there is a pending issue with #47, and unless there is still an issue on some platform, I suggest we merge #47 and #46.  
  
If there is still an issue, I'd like to see the platform/compiler involved, a quick description of the issue and the actual error message.  
  
Cheers

---

## Comment 10

> Username: aminiussi  
> Created_at: 2017-05-19 14:40:34 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-302721157  

1.64 build is currently failing, could you please proceed with merge or provide documented reasons not to ?  
Regards

---

## Comment 11

> Username: Lastique  
> Created_at: 2017-07-02 10:46:31 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-312484054  

@aminiussi, I think you are one of Boost.MPI maintainers, aren't you? If you feel #47 and #46 are ready for master, you could merge them yourself.  
  
IMO, merging the fixes from develop and #47 is better than doing nothing since Boost.MPI is completely broken in 1.64 and will be still as broken in 1.65.

---

## Comment 12

> Username: aminiussi  
> Created_at: 2017-07-03 09:57:30 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-312603119  

Yes, I saw a few months ago I was in that list. But being both the submitter/reviewer/executioner  of those pull requests makes me uncomfortable. If I did, it would be in opposition with the current cherry pick release model (which I do not approve, but still) without having heard of the exacts problems/platforms mentioned by  @Belcourt.  
Also, it would be mostly based on the tests I can run locally.   
[http://www.boost.org/development/tests/develop/developer/mpi.html](http://www.boost.org/development/tests/develop/developer/mpi.html) shows failures with no useful details (some are clearly configuration issues, but for other we do not know). I could re-activate my test to show some results on the web page, but that is quite some work and I'm a little reluctant putting in the effort if the conclusion is that the work won't go to master for some unknown regression on some unknown platform (I asked but got no answer yet, see this discussion for details).  
So, I'll try to relauch my test, but would like to have some inputs regarding what people thing we should do and have as much feed back as possible regarding the test. Does anybody knows who's running these Sandia failing test at: [http://www.boost.org/development/tests/develop/developer/mpi.html](http://www.boost.org/development/tests/develop/developer/mpi.html) .

---

## Comment 13

> Username: Lastique  
> Created_at: 2017-07-03 10:51:59 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-312614658  

Well, at least merging #47 to develop would be desirable so that it gets tested and fixes a few test failures.  
  
Regarding community opinion I think it's best to write a message to the Boost developers mailing list. Personally, as I mentioned, I think the changes make the library "better" (at least, it compiles) and therefore should be merged before the 1.65 release.  
  
Regarding Sandia, I think @Belcourt manages these testers. You can also request test logs through the dev ML.

---

## Comment 14

> Username: Rombur  
> Created_at: 2018-02-13 13:34:13 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-365267822  

Is there any plan to merge this PR? Right now I have to patch boost mpi to be able to use it with nvcc (this has been fixed a year ago in this branch)

---

## Comment 15

> Username: aminiussi  
> Created_at: 2018-02-15 12:56:06 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-365919335  

On a related note, has anyone any news from Noel Belcourt ?  
  
Otherwise I'm afraid I'll have to merge it on my own.  
  
Cheers  
  
  
  
On 13/02/2018 14:34, Bruno Turcksin wrote:  
>  
> Is there any plan to merge this PR? Right now I have to patch boost   
> mpi to be able to use it with nvcc (this has been fixed a year ago in   
> this branch)  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/mpi/pull/46#issuecomment-365267822>, or   
> mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAhsjNs3FdSNarok6UiAlJDEFCHejjyhks5tUY9VgaJpZM4MJ8VO>.  
>

---

## Comment 16

> Username: dalg24  
> Created_at: 2018-02-20 18:25:56 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367071632  

@aminiussi would you please make sure 90e84fe makes it into 1.67.0?

---

## Comment 17

> Username: danieljames  
> Created_at: 2018-02-20 18:41:13 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367076542  

If this is a breaking change, then it really should be merged today, if not then there's a week left. If that isn't possible, let us (the release managers) know.

---

## Comment 18

> Username: aminiussi  
> Created_at: 2018-02-20 21:19:53 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367123208  

The idea would be to merge long due develop on master.  
  
So it's big, but has been tested as devlop for a long time. Is that ok ?  
  
I'm relaunching my linux tests  
  
Thk  
  
On 20/02/2018 19:41, Daniel James wrote:  
>  
> If this is a breaking change, then it really should be merged today,   
> if not then there's a week left. If that isn't possible, let us (the   
> release managers) know.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/mpi/pull/46#issuecomment-367076542>, or   
> mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAhsjGNd6GYMjAbhxbUMRCaWsceaOxgsks5tWxHJgaJpZM4MJ8VO>.  
>

---

## Comment 19

> Username: danieljames  
> Created_at: 2018-02-20 21:38:49 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367128998  

I'm not keen on massive merges, but if it's unavoidable then I think the important thing is to try to get people to test the beta.

---

## Comment 20

> Username: Lastique  
> Created_at: 2018-02-20 22:00:09 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367135116  

IMHO, it's been tested in develop for ages. It doesn't matter how big it is, we should accept develop as the "best" Boost.MPI version there is and just merge it. Otherwise, if develop is considered broken somehow then let's revert it to master state.

---

## Comment 21

> Username: danieljames  
> Created_at: 2018-02-20 22:12:00 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367138543  

I don't think the length of time they've been tested for makes much of a difference, as it's always the same tests. I think there were problems with pointer containers in the last release, which was a merge of old develop changes.

---

## Comment 22

> Username: Belcourt  
> Created_at: 2018-02-20 22:20:39 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367140885  

Folks, I’m completely unable to help with MPI, can someone else (Alain) please help out with this?  Sorry about the delay.  
  
Noel Belcourt  
  
On Feb 20, 2018, at 11:25 AM, Damien L-G <notifications@github.com<mailto:notifications@github.com>> wrote:  
  
  
@aminiussi<https://github.com/aminiussi> would you please make sure 90e84fe<https://github.com/boostorg/mpi/commit/90e84fe143caacee583fa5aeea1d678dd53aebac> makes it into 1.67.0?  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/mpi/pull/46#issuecomment-367071632>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AA65bebPup_r9xdkzfhMVp5M3w1XOpuDks5tWw40gaJpZM4MJ8VO>.

---

## Comment 23

> Username: aminiussi  
> Created_at: 2018-02-20 22:30:46 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367143586  

On 20/02/2018 23:12, Daniel James wrote:  
>  
> I don't think the length of time they've been tested for makes much of   
> a difference, as it's always the same tests. I think there were   
> problems with pointer containers in the last release, which was a   
> merge of old develop changes.  
>  
Didn't saw these, if anyone has a pointer to the problem, that would be   
welcomed, if only to add a test ?  
The mains issues usually comes from source incompatibilities between mpi   
and serialize (mpi uses the internals of serialize, which is something   
to fix, so some source change in serialize impact mpi in a difficult to   
predict way (since both libs have two branches, and boost distribution   
on the test platform can trigger/mask issues)).  
  
So yes, the huge merge is a problem, but nobody knows how to split that   
huge merge into small one without doing more harm than good.  
So if develop appears not to be good enough, I think we might as well   
reset it to master and forget about the last years development. But if   
we can cover enough platform, I think it's worth give it a try.  
  
The reason why we're in this situation is described in this #46 issue.  
  
Cheers  
  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/mpi/pull/46#issuecomment-367138543>, or   
> mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAhsjAmm1gwkX7vgxdSyiLhsmB-t7PIbks5tW0MwgaJpZM4MJ8VO>.  
>

---

## Comment 24

> Username: danieljames  
> Created_at: 2018-02-20 22:51:09 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367149002  

Sorry, pointer containers is another library, not your problem. I was just using it to illustrate a point.  
  
I'm afraid it's up to you whether or not develop is good enough. But don't be over-cautious, it looks like people want these improvements. We understand that you're in a difficult position and appreciate your work. I hope I wasn't discouraging.

---

## Comment 25

> Username: aminiussi  
> Created_at: 2018-02-20 23:02:57 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367151980  

Discouraging ?  
  
I do Fortran with astronomers on a weekly basis....  
  
;-)  
  
On 20/02/2018 23:51, Daniel James wrote:  
>  
> Sorry, pointer containers is another library, not your problem. I was   
> just using it to illustrate a point.  
>  
> I'm afraid it's up to you whether or not develop is good enough. But   
> don't be over-cautious, it looks like people want these improvements.   
> We understand that you're in a difficult position and appreciate your   
> work. I hope I wasn't discouraging.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/mpi/pull/46#issuecomment-367149002>, or   
> mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAhsjI8GPBeO5WroKyk3jcI66ZF0C4DDks5tW0xdgaJpZM4MJ8VO>.  
>

---

## Comment 26

> Username: Lastique  
> Created_at: 2018-02-20 23:53:47 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367163266  

> I think there were problems with pointer containers in the last release, which was a merge of old develop changes.  
  
Even if so, I assume those problems were localized and sorted out. Which ultimately resulted in a better code rather than stagnation.

---

## Comment 27

> Username: aminiussi  
> Created_at: 2018-02-21 15:54:37 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367372479  

Does anyone has the possibility to give it a try on windows platforms ?  
  
I tested  intel 17.0.4 with intel's MPI in the plain, C++11, C++14   
flavour and gcc 4.8.5.  
  
The configuration is everything on master except mpi which is develop.  
  
I'll have a look on the regression web pages to check if they're not   
tooo out of date.  
thk  
  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/mpi/pull/46#issuecomment-367163266>, or   
> mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAhsjGtRNJFFp6N2H_N-3RklYy_wc0NZks5tW1sLgaJpZM4MJ8VO>.  
>

---

## Comment 28

> Username: aminiussi  
> Created_at: 2018-02-22 21:11:49 UTC  
> Url: https://github.com/boostorg/mpi/pull/46#issuecomment-367823555  

Ok, so we have the test on the boost web site and my local linux tests   
and they look ok.  
  
I'm pushing the merge.  
  
  
  
On 21/02/2018 00:53, Andrey Semashev wrote:  
>  
>     I think there were problems with pointer containers in the last  
>     release, which was a merge of old develop changes.  
>  
> Even if so, I assume those problems were localized and sorted out.   
> Which ultimately resulted in a better code rather than stagnation.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/mpi/pull/46#issuecomment-367163266>, or   
> mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AAhsjGtRNJFFp6N2H_N-3RklYy_wc0NZks5tW1sLgaJpZM4MJ8VO>.  
>

---
