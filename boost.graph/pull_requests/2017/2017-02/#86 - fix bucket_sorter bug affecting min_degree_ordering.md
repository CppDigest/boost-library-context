# #86 fix bucket_sorter bug affecting min_degree_ordering [Open]

> Username: felix-salfelder  
> Created at: 2017-02-28 22:16:27 UTC  
> Updated at: 2022-03-23 19:37:33 UTC  
> Url: https://github.com/boostorg/graph/pull/86  

removing an element from a stack does not always work, since the  
predecessor of a stack element is not (always) stored. this patch  
merges the array head into next, so a top element in a stack can point  
to the head of the stack it is in.  
  
remarks:  
- i am guessing the intended use (see test below), as it works like that  
  for elements deeper down in a stack  
- the fix abuses pointers/iterators and infers offsets from address  
  differences. (yes it's a bit ugly.)  
- memory needs and complexity are unaffected, size_type is probably big  
  enough.  
  
test case. B is a bucketsorter operating on a vector V.  
  
 V[0]=0;  
 V[1]=1;  
 B.push(0);  
 B.push(1);  
  
// now, stacks 0 and 1 are singletons.  
// try to move 0 to stack 1, should result in  
// head_1->0->1->end, but calls remove first, then  
  
 V[0]=1;  
 B.update(0); // <- BOOM  
  
// the update calls remove, it "removes" 0 from stack V[0]=1.  
// it's not there yet (!).  
// instead 1 (top of bucket 1) must die.  
// the result is head_1->0->end, and wrong.

---

## Comment 1

> Username: felix-salfelder  
> Created_at: 2017-09-11 09:03:54 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-328466769  

need to redesign my patch.. don't know how to do it yet.  
  
the value index datatype may be too small to represent twice the range. so  
actually the longer head array may be outside of the addressable range (if too  
many indexes are used). otoh adding an extra bit will affect run time.  
  
at the same time i'ts unclear to me, why the addressed bug does not affect algorithms such  
as min_degree_ordering. it does use `remove`.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2018-07-25 10:37:49 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-407711556  

I think the best thing is if you can make the simplest unit test that fails when it should pass according to the intended behaviour of the component.

---

## Comment 3

> Username: felix-salfelder  
> Created_at: 2018-07-25 11:05:53 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-407717812  

On Wed, Jul 25, 2018 at 03:37:49AM -0700, Jeremy W. Murphy wrote:  
> I think the best thing is if you can make the simplest unit test that  
> fails when it should pass according to the intended behaviour of the  
> component.  
  
Thank you.  
  
We do have a unit test against the upstream header in treedec (former  
tdlib). It hits the issue, and it is flagged as fail [1]. It is pretty  
simple, but it might not be the simplest possible. See the comments in  
the code. Please modify and use it for any purpose whatsoever.  
  
NB: treedec ships a modified header, which i think fixes the issue.  
similar tests [2,3] witness this.  
  
Please let me know, if/how I can help any further.  
  
[1] https://github.com/freetdi/tdlib/blob/develop/tests/bucket_sorter_2u.cpp  
[2] https://github.com/freetdi/tdlib/blob/develop/tests/bucket_sorter_1.cpp  
[3] https://github.com/freetdi/tdlib/blob/develop/tests/bucket_sorter_2.cpp

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2018-07-25 11:14:58 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-407719728  

I think it's best if you put the relevant unit test into this PR. Whoever is maintaining Boost.Graph won't want to look around at code elsewhere on the web. Ideally (if I was the maintainer, which I'm not) I would want to see that a) the unit test is correct and that b) it fails on master but c) passes with the change to the algorithm.

---

## Comment 5

> Username: felix-salfelder  
> Created_at: 2018-07-25 11:40:34 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-407725145  

On Wed, Jul 25, 2018 at 04:14:59AM -0700, Jeremy W. Murphy wrote:  
> I think it's best if you put the relevant unit test into this PR.  
> Whoever is maintaining Boost.Graph won't want to look around at code  
> elsewhere on the web. Ideally (if I was the maintainer, which I'm not)  
> I would want to see that a) the unit test is correct and that b) it  
> fails on master but c) passes with the change to the algorithm.  
  
I understand that everybodys time is limited, and I consider doing what  
you suggest, once I get to it.  
  
To determine whether the unit test is correct, somebody will have to  
read it in any case. so, in effect I cannot really save somebody elses  
time here, other than the time it took me to find the bug (go figure).  
I fold in contributions into several other projects, and I know how  
little time is saved by trying to convince contributors to do things  
differently. some can't write code, some cant use git, some don't  
understand what they do at all.  
  
If you find that this bug is more important to you, go ahead, bring it  
to the attention to somebody who is willing to spend time on it, or fill  
the forms -- as you suggest. I will eventually, but it's not my  
priority, and it hasn't been for a while.  
  
I do appreciate the reminder, in any case. I forgot about this one.

---

## Comment 6

> Username: anadon  
> Created_at: 2018-08-31 18:10:47 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-417747619  

I'm helping out with the PR backlog. Looks like you have a bugfix, incomplete tests, and no new examples will be needed.  Please make your PR complete.  This is to let you know and help me prioritize PR's.

---

## Comment 7

> Username: felix-salfelder  
> Created_at: 2018-08-31 18:57:53 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-417760011  

thanks. I commited the test from treedec mentioned above.  
  
another bug in min_degree_ordering seems related to this. min_degree_ordering assert fails on some empty and on some complete graphs. see the test.

---

## Comment 8

> Username: anadon  
> Created_at: 2018-08-31 19:01:00 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-417760847  

This may be tangled then.  The PR needs to be clean and not affect other parts of the implementation.  I'm going to hold off on messing with this until after the first two batches of merges.

---

## Comment 9

> Username: felix-salfelder  
> Created_at: 2018-08-31 19:06:05 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-417762085  

On Fri, Aug 31, 2018 at 12:01:02PM -0700, jrmarsha wrote:  
> This may be tangled then.  The PR needs to be clean and not affect  
> other parts of the implementation.  I'm going to hold off on messing  
> with this until after the first two batches of merges.  
  
the fix is unchanged and only touches include/boost/pending/bucket_sorter.hpp.  
  
the min_degree_ordering test shows a side effect only.

---

## Comment 10

> Username: anadon  
> Created_at: 2018-08-31 19:09:15 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-417762795  

There really shouldn't be unintended side effects.  I'm not going to promote a PR that knowingly introduces regressions.

---

## Comment 11

> Username: felix-salfelder  
> Created_at: 2018-08-31 19:25:41 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-417766511  

On Fri, Aug 31, 2018 at 12:09:16PM -0700, jrmarsha wrote:  
> There really shouldn't be unintended side effects.  I'm not going to  
> promote a PR that knowingly introduces regressions.  
  
right now, boost::min_degree_ordering crashes on empty input graphs, or  
silently fails if NDEBUG is set.  
  
with my fix it not only doesn't crash, but also produces a correct  
ordering. this is just extra evidence. i am not aware of any other side  
effects, but i didn't run min degree on all the other graphs yet.  
  
it is clearly tricky. i did mention that sb actually will have to read  
the bucket sorter test case, and guess whether the stack corruption and  
the md crash is a bug or a feature.

---

## Comment 12

> Username: anadon  
> Created_at: 2018-08-31 19:28:21 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-417767125  

Ah crud.  I'm going to keep this out of the first two batches of merges.  I need to sink time into PR.

---

## Comment 13

> Username: jeremy-murphy  
> Created_at: 2018-10-14 03:49:55 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-429594272  

@felix-salfelder thanks for adding the unit test.  
  
Earlier, you wrote:  
  
> need to redesign my patch.. don't know how to do it yet.  
>   
> the value index datatype may be too small to represent twice the range. so  
> actually the longer head array may be outside of the addressable range (if too  
> many indexes are used). otoh adding an extra bit will affect run time.  
  
Is this still an open implementation issue for this patch? I.e., does it need some work before being merged?  
Bucket sort is not an overly complex algorithm in general: we could potentially make a new, simpler, generic implementation, depending on how tightly coupled the buckets are with how Boost.Graph is using it. (I have a little experience with these algorithms and a little spare time to work on open source projects at the moment.)  
  
> at the same time i'ts unclear to me, why the addressed bug does not affect algorithms such  
> as min_degree_ordering. it does use `remove`.  
  
I guess the answer in the end was that it does affect it.

---

## Comment 14

> Username: felix-salfelder  
> Created_at: 2018-10-14 06:13:28 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-429599695  

On Sat, Oct 13, 2018 at 08:49:58PM -0700, Jeremy W. Murphy wrote:  
> > [..] need redesign [..]  
>  
> Is this still an open implementation issue for this patch? I.e., does  
> it need some work before being merged?  
  
Thanks Jeremy for digging into this.  
  
looking at it now.. trying to remember.  
  
the patch embeds the head array into the next array to save several  
conditionals which would be all over the place. i thought there was a  
problem with integer arithmetic overflow. i just can't see it, perhaps  
it was in some other code.  
  
somehow related, the patch introduces ugly assignments in the stack  
class such as  
  
    prev[new_head] = bucket_id + (head - next);  
  
which could possibly be reformulated. does not seem essential.  
  
note that prev is pointing to size_type (hard wired), which is  
unnecessary large in most applications. bucket_id can be an unsigned  
char. but this is not the overflow...  
  
> Bucket sort is not an overly complex algorithm in general: we could  
> potentially make a new, simpler, generic implementation, depending on  
> how tightly coupled the buckets are with how Boost.Graph is using it.  
> (I have a little experience with these algorithms and a little spare  
> time to work on open source projects at the moment.)  
  
not sure. "generic", "simple" and "fast" are harder to achieve  
simultaneously. especially, the current interface is quite useful.  
  
i expect that a new implementation will lead to similar choices and  
tradeoffs.  i'd suggest to change the current code, if you can think of  
a better way. YMMV.  
  
please, if you have time, consider testing this more, including the  
shorter integer types. some simple randomised testing would have easily  
revealed the original bug.  
  
> I guess the answer in the end was that it does affect it [graph::bmd].  
  
a corner case, but yes.

---

## Comment 15

> Username: jeremy-murphy  
> Created_at: 2018-10-14 09:48:24 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-429611015  

I just ran the unit test and it returned "broken", which was unexpected. Is that what it's returning for you?

---

## Comment 16

> Username: jeremy-murphy  
> Created_at: 2018-10-14 10:02:08 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-429611792  

Hmmm, maybe just something to do with the way I compiled it manually, because when I run it as part of the Boost.Graph test suite, it passes. Kinda weird, though.

---

## Comment 17

> Username: felix-salfelder  
> Created_at: 2018-10-14 10:33:12 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-429614340  

On Sun, Oct 14, 2018 at 03:02:09AM -0700, Jeremy W. Murphy wrote:  
> Hmmm, maybe just something to do with the way I compiled it manually,  
> because when I run it as part of the Boost.Graph test suite, it  
> passes. Kinda weird, though.  
  
I only have this. never tried to use the test suite.  
  
test$ g++ bucket_sorter_2u.cpp # uses system headers (old)  
test$ ./a.out | grep -e good -e broken  
broken  
test$ g++ bucket_sorter_2u.cpp -I../include  
test$ ./a.out | grep -e good -e broken  
good  
  
I ticked the "Allow edits from maintainers" box. please feel free.

---

## Comment 18

> Username: jeremy-murphy  
> Created_at: 2018-10-14 12:46:20 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-429623615  

OK, yeah, I must have picked up the system headers by accident when I compiled it manually.

---

## Comment 19

> Username: jeremy-murphy  
> Created_at: 2018-10-14 12:48:17 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-429623755  

>  I ticked the "Allow edits from maintainers" box. please feel free.  
  
I'm not a maintainer.  :)

---

## Comment 20

> Username: anadon  
> Created_at: 2018-10-15 19:57:31 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-429991559  

@felix-salfelder Could you rebase your PR?  More CI tests are in devel, as well as other fixes.

---

## Comment 21

> Username: pdimov  
> Created_at: 2018-10-16 00:56:17 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-430063880  

This PR is against master, and should be against develop.

---

## Comment 22

> Username: anadon  
> Created_at: 2018-11-01 14:11:10 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-435052365  

@felix-salfelder What is your status on this?

---

## Comment 23

> Username: felix-salfelder  
> Created_at: 2018-11-05 21:37:18 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436044425  

On Thu, Nov 01, 2018 at 07:36:12AM -0700, jrmarsha wrote:  
> @felix-salfelder What is your status on this?  
  
tried to follow your instructions (rebase etc.). thought it worked.  
  
(short on time.) what else can I do?

---

## Comment 24

> Username: anadon  
> Created_at: 2018-11-05 21:53:13 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436049312  

It really is just rebasing on boostorg/develop .

---

## Comment 25

> Username: felix-salfelder  
> Created_at: 2018-11-05 21:57:34 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436050632  

On Mon, Nov 05, 2018 at 01:53:14PM -0800, jrmarsha wrote:  
> It really is just rebasing on boostorg/develop .  
  
rebased to f7dfafb5af0ca. was it the right one?  
  
thanks!

---

## Comment 26

> Username: anadon  
> Created_at: 2018-11-05 21:58:38 UTC  
> Updated_at: 2018-11-05 21:59:45 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436050966  

use the `devel` tag, not a hash.  It'll be good git practice (and lets me be lazy so I can buy a display cable).

---

## Comment 27

> Username: felix-salfelder  
> Created_at: 2018-11-05 22:01:56 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436051952  

On Mon, Nov 05, 2018 at 01:58:39PM -0800, jrmarsha wrote:  
> use the `devel` tag, not a hash.  
  
all tags i can see are of the form  
  
[..]  
boost-1.65.0  
boost-1.65.1  
boost-1.66.0  
boost-1.67.0  
boost-1.68.0  
  
no 'devel' here, except for the branch pointing to f7dfafb5af0 on my  
end (?)  
  
thanks

---

## Comment 28

> Username: anadon  
> Created_at: 2018-11-05 22:05:11 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436052830  

Well that's isn't right.  I'll need you to walk me through how to re-create your setup.  How are to making/pulling the graph repo, and how are you rebasing it?  All of your explicit steps.  You might want to add a gist of a sanitized bash history for me.

---

## Comment 29

> Username: felix-salfelder  
> Created_at: 2018-11-05 22:12:49 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436055132  

On Mon, Nov 05, 2018 at 02:05:12PM -0800, jrmarsha wrote:  
> Well that's isn't right.  I'll need you to walk me through how to  
> re-create your setup.  How are to making/pulling the graph repo, and  
> how are you rebasing it?  All of your explicit steps.  You might want  
> to add a gist of a sanitized bash history for me.  
  
i cloned from https://github.com/boostorg/graph  
  
    $ git clone https://github.com/boostorg/graph  
  
this is now my "origin". then added my own fork (after clicking the fork  
button on the github page)  
  
    $ git remote add myownfork ssh://git@github/etcpp  
  
in that repo i found a "devel" branch on which i have rebased the four  
commits.  
  
    $ git log -1 origin/develop |head -n1  
    commit f7dfafb5af0ca1fd0dd119377bc0acf78ed5a1fc  
    $ git checkout my_stuff  
    $ git rebase -i origin/develop  
     [.. select 4 commits ]  
    $ git push myownfork +my_stuff:bucket_sorter  
  
hth  
felix

---

## Comment 30

> Username: anadon  
> Created_at: 2018-11-06 00:20:56 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436085625  

Not the way I would have gone about it, but it does seem to have done the job.  Looks in mostly good shape.  I'll give it a more thorough look over after the CI's complete.  
  
I would have merged from boostorg/develop into your github repo.  Nice web interface and all :P

---

## Comment 31

> Username: felix-salfelder  
> Created_at: 2018-11-06 06:38:36 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436146711  

On Mon, Nov 05, 2018 at 04:20:57PM -0800, jrmarsha wrote:  
> I would have merged from boostorg/develop into your github repo.  
  
(i don't even know what thas means.)  
  
> Nice web interface and all :P  
  
fortunately, git hasn't changed much in 10 years. the web stuff is  
pretty new, weird, unpredictable and always different.  
  
thanks

---

## Comment 32

> Username: anadon  
> Created_at: 2018-11-06 10:54:55 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436212574  

Time to learn a new trick and a new way of doing things!  
  
On Tue, Nov 6, 2018, 01:38 felix <notifications@github.com wrote:  
  
> On Mon, Nov 05, 2018 at 04:20:57PM -0800, jrmarsha wrote:  
> > I would have merged from boostorg/develop into your github repo.  
>  
> (i don't even know what thas means.)  
>  
> > Nice web interface and all :P  
>  
> fortunately, git hasn't changed much in 10 years. the web stuff is  
> pretty new, weird, unpredictable and always different.  
>  
> thanks  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/86#issuecomment-436146711>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AC-CBTG-8EG6Z6Jzk-yKiAIpRkMyaHX5ks5usS5ugaJpZM4MO9Nx>  
> .  
>

---

## Comment 33

> Username: felix-salfelder  
> Created_at: 2018-11-06 12:24:52 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436234819  

On Tue, Nov 06, 2018 at 02:54:57AM -0800, jrmarsha wrote:  
> > On Mon, Nov 05, 2018 at 04:20:57PM -0800, jrmarsha wrote:  
> > > I would have merged from boostorg/develop into your github repo.  
  
> Time to learn a new trick and a new way of doing things!  
  
I can kind of guess your point. what is "boostorg/develop"? and what  
does "merge into my github repo" mean?  It's a matter of communication  
much more than of doing things. Let me try and explain.  
  
Technical communication derails without a mininum of precision. The  
instruction i got earler was: "rebase to develop", which is perfectly  
well defined (see git-rebase(1)), incidently exactly what I did. it has  
been understood and done millions of times, before fancy web stuff  
existed. even before git. No "new way" could possibly be much simpler or  
instructed in fewer words (3!!) without loosing precision.  
  
Your "new way" of doing this apparently also tries to to change the way  
we are talking about things. it is tricky, as you say, but completely  
unneeded, and does not add to the content. consider the following. I  
use aliases and macros to type out git commands. i did not include what  
i *actually* did, when i spelled it out to you. This way there was no  
overhead, and my instructions can be interpreted and reproduced easily.  
I bet they are still valid in 10 more years time.  
  
imo, "new tricks" need to be examined carefully before adoption or  
spreading. i hope i explained why, and, apologies for this off-topic  
post. it's the second already in this thread, but it appeared as  
necessary as the first. back to technical stuff?

---

## Comment 34

> Username: anadon  
> Created_at: 2018-11-06 12:49:51 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-436241235  

Checking your branch, it is effectively at develop, just not the tag develop, which isn't really a problem.  But your changes are breaking.  Last time a build broke like this it was related to `-fvisibility=hidden`.  Here, that is what is happening about `_warp_iter` not having a public constructor.  Double check your scope of your relevant class definition and placement of `public` and `private`.

---

## Comment 35

> Username: felix-salfelder  
> Created_at: 2018-11-09 19:05:38 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437462408  

On Tue, Nov 06, 2018 at 04:49:52AM -0800, jrmarsha wrote:  
> Checking your branch, it is effectively at develop, just not the tag  
> develop, which isn't really a problem.   
  
still, there is no tag "develop" in my clone. i cloned from  
https://github.com/boostorg/graph. this is the repo i thought we were  
talking about.  
  
> But your changes are breaking.  
  
Yes. i had a look into the travis status. it appears that something  
fails (only) for apple [1].  
  
The relevant header has a type "Iter", line 71  
  
  typedef typename std::vector<size_type>::iterator Iter;  
  
i did not change this, nor did i change the use of Iter anywhere.  
  
> Last time a build broke like this it was related to  
> `-fvisibility=hidden`.  Here, that is what is happening about  
> `_warp_iter` not having a public constructor.  Double check your scope  
> of your relevant class definition and placement of `public` and  
> `private`.  
  
Perhaps I am missing something, but I suspect that apple is also broken  
in commit f7dfafb5af0ca1fd, except that the test suite does not compile  
any of the bucketsorter code.  
  
(if that's correct, could an apple person please deal with this -- or  
give a hint why apple is right)  
  
thanks  
  
[1] https://travis-ci.org/boostorg/graph/jobs/451094482

---

## Comment 36

> Username: pdimov  
> Created_at: 2018-11-09 19:19:10 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437466231  

Probably nothing to do with Apple, it's just that the Mac jobs are using libc++ and all the Linux ones are using libstdc++.

---

## Comment 37

> Username: pdimov  
> Created_at: 2018-11-09 19:34:11 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437470672  

No idea why the copy constructor of `__wrap_iter` doesn't work here.

---

## Comment 38

> Username: felix-salfelder  
> Created_at: 2018-11-10 05:08:55 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437559023  

something seems wrong with CI. #135 is a no-op, yet 21 out of 24 runs fail, not just on apple...

---

## Comment 39

> Username: pdimov  
> Created_at: 2018-11-10 05:18:37 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437559419  

Seems broken by Boost.Parameter. @eldiener @CromwellEnage

---

## Comment 40

> Username: anadon  
> Created_at: 2018-11-10 05:19:32 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437559457  

I'm hoping this doesn't affect the rest of graph.

---

## Comment 41

> Username: CromwellEnage  
> Created_at: 2018-11-10 12:12:19 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437579678  

I’ll start working on a fix later today.  
  
On Sat, Nov 10, 2018 at 00:19 jrmarsha <notifications@github.com> wrote:  
  
> I'm hoping this doesn't affect the rest of graph.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/pull/86#issuecomment-437559457>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsMb_fPCKWhbbUtxz0WtZO3cQxN1Aks5utmHlgaJpZM4MO9Nx>  
> .  
>

---

## Comment 42

> Username: eldiener  
> Created_at: 2018-11-10 20:13:18 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437617979  

I have mergeed the parameter fix.

---

## Comment 43

> Username: felix-salfelder  
> Created_at: 2018-11-11 08:49:17 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437653245  

still no joy. travis does things like  
  
    ./boost/parameter/aux_/tagged_argument.hpp:643:1: warning: multi-line comment [-Wcomment]  
     //#if !defined(BOOST_NO_FUNCTION_TEMPLATE_ORDERING) && \  
     ^  
    g++-4.8: internal compiler error: Killed (program cc1plus)  
  
(among others)

---

## Comment 44

> Username: CromwellEnage  
> Created_at: 2018-11-11 14:36:09 UTC  
> Updated_at: 2018-11-11 14:37:06 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437675182  

I'll fix the multi-line comment warning in the next PR to Boost.Parameter.  
  
As for getting around __wrap_iter's copy constructor being private, perhaps this will work:  
  
``  
      stack(bucket_type _bucket_id, Iter h, Iter n, Iter p, IndexValueMap v,  
            const ValueIndexMap& _id)  
#if defined(BOOST_CLANG) && (1 == BOOST_CLANG) && defined(__APPLE_CC__)  
      : bucket_id(_bucket_id), head(), next(), prev(), value(v), id(_id)  
      {  
        head = h;  
        next = n;  
        prev = p;  
      }  
#else  
      : bucket_id(_bucket_id), head(h), next(n), prev(p), value(v), id(_id) {}  
#endif  
  
      // Avoid using default arg for ValueIndexMap so that the default  
      // constructor of the ValueIndexMap is not required if not used.  
      stack(bucket_type _bucket_id, Iter h, Iter n, Iter p, IndexValueMap v)  
#if defined(BOOST_CLANG) && (1 == BOOST_CLANG) && defined(__APPLE_CC__)  
        : bucket_id(_bucket_id), head(), next(), prev(), value(v)  
      {  
        head = h;  
        next = n;  
        prev = p;  
      }  
#else  
        : bucket_id(_bucket_id), head(h), next(n), prev(p), value(v) {}  
#endif  
``  
  
The internal compiler errors are another matter.  I'm not yet sure what would be causing them.

---

## Comment 45

> Username: CromwellEnage  
> Created_at: 2018-11-12 12:45:52 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437866621  

The __wrap_iter workaround I proposed needs to be applied to <boost/pending/bucket_sorter.hpp>, specifically to the stack constructor definitions in lines 79 and 85.  If you tried that and it didn't work, we still need to see this.

---

## Comment 46

> Username: felix-salfelder  
> Created_at: 2018-11-12 13:04:00 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437872301  

On Mon, Nov 12, 2018 at 04:45:53AM -0800, Cromwell D. Enage wrote:  
> The __wrap_iter workaround I proposed needs to be applied to  
> <boost/pending/bucket_sorter.hpp>, specifically to the stack  
> constructor definitions in lines 79 and 85.  If you tried that and it  
> didn't work, we still need to see this.  
  
it seems that most of the graph stuff is broken, so it does not even get  
to compile the bucket sorter, see #135. not just on apple.  
  
i tried what you suggested, see  
https://github.com/felix-salfelder/graph/commit/f01853527e5eeafded5a635aa95ecaafbbfba220  
it failed and it looks pretty pointless at the moment.  
  
a week ago or so, everything was green, except on apple. i don't  
understand what happened in the meantime.  
  
thanks

---

## Comment 47

> Username: CromwellEnage  
> Created_at: 2018-11-12 19:16:00 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-437997104  

I found a major source of the errors.  In <boost/graph/named_function_params.hpp>, the metafunction boost::detail::convert_bgl_params_to_boost_parameter uses boost::parameter::aux::tagged_argument and boost::parameter::aux::arg_list, which are internal Boost.Parameter components whose behavior has changed.  I'll work on a fix and submit it in a separate PR.

---

## Comment 48

> Username: felix-salfelder  
> Created_at: 2018-11-12 19:28:59 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-438000953  

On Mon, Nov 12, 2018 at 07:16:01PM +0000, Cromwell D. Enage wrote:  
> I found a major source of the errors.  In  
> <boost/graph/named_function_params.hpp>, the metafunction  
> boost::detail::convert_bgl_params_to_boost_parameter uses  
> boost::parameter::aux::tagged_argument and  
> boost::parameter::aux::arg_list, which are internal Boost.Parameter  
> components whose behavior has changed.  I'll work on a fix and submit  
> it in a separate PR.  
  
sounds great  
  
thanks

---

## Comment 49

> Username: CromwellEnage  
> Created_at: 2018-11-18 17:32:42 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-439710447  

Okay, I've created PR #137 which fixes all the errors.  Unfortunately, it will create a merging conflict at <boost/pending/bucket_sorter.hpp> and "test/Jamfile.v2".

---

## Comment 50

> Username: felix-salfelder  
> Created_at: 2018-11-19 15:39:45 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-439936116  

rebased to #137. the conflicts in the code were obvious.  
  
thanks

---

## Comment 51

> Username: CromwellEnage  
> Created_at: 2018-11-19 22:09:55 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-440059800  

I see that the copy-ctor workarounds are still failing.  This needs another approach...

---

## Comment 52

> Username: CromwellEnage  
> Created_at: 2018-11-20 21:34:57 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-440437616  

Just to give you a heads-up: I'm reverting my changes to the test suite in my PR, mainly to get rid of those BOOST_PARAMETER_EXPONENTIAL_OVERLOAD_THRESHOLD_ARITY macro definitions that Boost.Parameter now defines as zero by default.

---

## Comment 53

> Username: CromwellEnage  
> Created_at: 2018-11-23 14:03:18 UTC  
> Updated_at: 2018-11-23 14:05:46 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-441248729  

Another heads-up: I've created PR #138 which upgrades minimum_degree_ordering to handle the degenerate case of an input graph that contains vertices but no edges.  The new PR includes a modified version of your test case.  Neither PR #137 nor PR #138 mess with bucket_sorter now.

---

## Comment 54

> Username: felix-salfelder  
> Created_at: 2018-11-23 15:28:42 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-441268393  

On Fri, Nov 23, 2018 at 06:03:19AM -0800, Cromwell D. Enage wrote:  
> Another heads-up: I've created PR #138 which upgrades  
> minimum_degree_ordering to handle the degenerate case of an input  
> graph that contains vertices but no edges.  Neither PR #137 nor PR  
> #138 mess with bucket_sorter now.  
  
thanks for your effort.  
  
afair, mindegree failed due to the broken bucket sorter at least on  
empty and on complete graphs. in other context the bucket sorter is  
completely unusable.  
  
the test for edges and their number does not fix much. yet it will be  
good to see this compile on apple at all.  
  
thanks  
felix

---

## Comment 55

> Username: pdimov  
> Created_at: 2018-11-23 16:32:59 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-441281827  

@mclow says he'll look into it if you file a bug report against libc++ on bugs.llvm.org.  
  
For the record, I've been unable to reproduce this on godbolt with -stdlib=libc++. It does seem to be Apple-specific for some reason.

---

## Comment 56

> Username: jeremy-murphy  
> Created_at: 2019-01-26 01:40:45 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457789501  

@felix-salfelder What's the status on this one? Still waiting to hear back about the Apple libc++ issue?

---

## Comment 57

> Username: felix-salfelder  
> Created_at: 2019-01-26 10:05:46 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457818709  

On Fri, Jan 25, 2019 at 05:40:45PM -0800, Jeremy W. Murphy wrote:  
> @felix-salfelder What's the status on this one? Still waiting to hear  
> back about the Apple libc++ issue?  
  
Yes. And there was an additional issue, also breaking non-apple.  
something with parameters.  
  
I will have to try..

---

## Comment 58

> Username: CromwellEnage  
> Created_at: 2019-01-26 17:47:40 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457850701  

Hi, @felix-salfelder, have you checked the Boost.Graph develop branch recently?  There were a couple of recent commits involving bucket_sorter and min_degree_ordering.

---

## Comment 59

> Username: felix-salfelder  
> Created_at: 2019-01-26 18:01:35 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457851829  

On Sat, Jan 26, 2019 at 09:47:41AM -0800, Cromwell D. Enage wrote:  
> Hi, @felix-salfelder, have you checked the Boost.Graph develop branch  
> recently?  There were a couple of recent commits involving  
> bucket_sorter and min_degree_ordering.  
  
Thanks. Yes, develop seems to compile again! I only checked today, and  
did the merge and did some cleanup.  
  
I dont know what is wrong with CI this time, it is stuck at "pending"  
for half a day now.

---

## Comment 60

> Username: felix-salfelder  
> Created_at: 2019-01-26 23:12:10 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457874247  

the jobs were run. but it's broken on apple, still  
  
    /boost/pending/bucket_sorter.hpp:87:16: error: calling a private constructor of class 'std::__1::__wrap_iter<unsigned long *>'  
        head = h;  
  
there is something wrong with their stl or compiler. i dont know how to work around this, without making it worse.

---

## Comment 61

> Username: mclow  
> Created_at: 2019-01-26 23:36:11 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457875524  

> the jobs were run. but it's broken on apple, still  
>   
> ```  
> /boost/pending/bucket_sorter.hpp:87:16: error: calling a private constructor of class 'std::__1::__wrap_iter<unsigned long *>'  
>     head = h;  
> ```  
> there is something wrong with their stl or compiler. i dont know how to work around this, without making it worse.  
  
What is that code trying to do?  
It looks like it is trying to initialize a `vector<size_type>` with a single `vector<size_type>::iterator`.

---

## Comment 62

> Username: mclow  
> Created_at: 2019-01-26 23:37:42 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457875604  

Never mind; looking at the old code. (which is weird like I commented)

---

## Comment 63

> Username: felix-salfelder  
> Created_at: 2019-01-26 23:40:30 UTC  
> Updated_at: 2019-01-26 23:49:15 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457875753  

On Sat, Jan 26, 2019 at 03:36:12PM -0800, Marshall Clow wrote:  
> What is that code trying to do?  
> It looks like it is trying to initialize a `vector<size_type>` with a single `vector<size_type>::iterator`.  
  
it is simply an assignment. it should have been a copy constructor call  
"head(h)", but that did not work (on apple) for some other reason.  
EDIT: no it's not. that was the issue in the previous code. now it is turning a pointer into a vector iterator.

---

## Comment 64

> Username: mclow  
> Created_at: 2019-01-27 00:08:04 UTC  
> Updated_at: 2019-01-27 00:08:13 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457877224  

> now it is turning a pointer into a vector iterator.  
  
That's the problem. You can't create an iterator from a pointer.

---

## Comment 65

> Username: felix-salfelder  
> Created_at: 2019-01-27 00:32:07 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457878422  

On Sat, Jan 26, 2019 at 04:08:05PM -0800, Marshall Clow wrote:  
> > now it is turning a pointer into a vector iterator.  
> That's the problem. You can't create an iterator from a pointer.  
  
sure I can, and it is the intent, example below.  
  
the question really is, why does it not work on apple? theres something  
wrong with the visibility.  
  
perhaps head should be reverted to be an iterator, but that is not a  
good answer. will try...  
  
  
// ===== example ====  
std::vector<unsigned> a(17);  
unsigned* p=&*a.begin();  
std::vector<unsigned>::iterator i(p);

---

## Comment 66

> Username: pdimov  
> Created_at: 2019-01-27 00:45:40 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457879007  

No, you can't create an iterator from a pointer, and this has nothing to do with Apple. https://godbolt.org/z/BEZSXf  
  
But that's not the original error. I suspect this pointer thing is a later change. It shouldn't work anywhere.

---

## Comment 67

> Username: felix-salfelder  
> Created_at: 2019-01-27 00:58:08 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457879544  

On Sat, Jan 26, 2019 at 04:45:40PM -0800, Peter Dimov wrote:  
> No, you can't create an iterator from a pointer, and this has nothing  
> to do with Apple. https://godbolt.org/z/BEZSXf  
  
try std::vector<int>::iterator i(p); in this godbolt thing, if you  
trust it.  
  
> But that's not the original error. I suspect this pointer thing is a  
> later change. It shouldn't work anywhere.  
  
of course it does. the issue is that a preprocessor workaround (not  
mine) hacks around another apple issue. it then fails when trying to  
assign.  
  
i reverted the head type, lets see.

---

## Comment 68

> Username: mclow  
> Created_at: 2019-01-27 01:58:12 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457882127  

When you try to create an `vector::iterator` from a pointer, you are relying on undocumented implementation details of whatever standard library you are using. This will compile (and work) for libstdc++ (as far as I know), not for libc++, probably for MSVC in release mode, and not for MSVC in debug mode.  
  
But that doesn't make it portable.

---

## Comment 69

> Username: felix-salfelder  
> Created_at: 2019-01-27 19:13:41 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-457945360  

On Sat, Jan 26, 2019 at 05:58:12PM -0800, Marshall Clow wrote:  
> But that doesn't make it portable.  
  
thanks. now, it seems to be more portable. travis is happy with it, at  
least.

---

## Comment 70

> Username: felix-salfelder  
> Created_at: 2019-02-05 11:36:38 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-460606137  

any clues what might be wrong with msvc? the output is a bit cryptic, but it says "Assertion failed: vector iterator not dereferencable" in one of the logs... thanks

---

## Comment 71

> Username: jeremy-murphy  
> Created_at: 2022-03-06 23:26:19 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-1060062812  

@felix-salfelder, I really appreciate how long you persisted with this bug fix.   
We had some issues with the CI and a lack of maintainers for a while but now things are (slowly) moving ahead.  
If you have time to resolve the conflicts, then I'll look at it again, although not in the next week or so.  
I have the magical maintainer hat now, so I can actually merge it in when it's ready!

---

## Comment 72

> Username: felix-salfelder  
> Created_at: 2022-03-07 20:05:06 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-1061083397  

On Sun, Mar 06, 2022 at 03:26:32PM -0800, Jeremy W. Murphy wrote:  
> @felix-salfelder, I really appreciate how long you persisted with this bug fix.   
> We had some issues with the CI and a lack of maintainers for a while but now things are (slowly) moving ahead.  
> If you have time to resolve the conflicts, then I'll look at it again, although not in the next week or so.  
> I have the magical maintainer hat now, so I can actually merge it in when it's ready!  
  
Thanks for keeping up. Yes it's been a while...  
  
Lots of whitespace conflicts, but not much else. I think a rebase will  
be easier and less messy, would that be acceptable?  
(No worries -- It won't happen this week anyway.)  
  
Why has the bucket_sorter been classified "pending" in the first place?  
Is there another issue with it that I have missed?

---

## Comment 73

> Username: jeremy-murphy  
> Created_at: 2022-03-07 22:32:08 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-1061207742  

> Thanks for keeping up. Yes it's been a while... Lots of whitespace conflicts, but not much else. I think a rebase will be easier and less messy, would that be acceptable? (No worries -- It won't happen this week anyway.) Why has the bucket_sorter been classified "pending" in the first place? Is there another issue with it that I have missed?  
  
Sure, whatever is easier for you. It's been so long that I looked at this in any detail that I will essentially be looking at it afresh anyway.  
  
I don't actually know the history of the "pending" stuff. I _guess_ it was for data structures that they thought should be moved into separate, more general-purpose Boost libraries.

---

## Comment 74

> Username: felix-salfelder  
> Created_at: 2022-03-14 11:18:16 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-1066661828  

On Mon, Mar 07, 2022 at 02:32:31PM -0800, Jeremy W. Murphy wrote:  
> Sure, whatever is easier for you. It's been so long that I looked at  
> this in any detail that I will essentially be looking at it afresh  
> anyway.  
  
I ended up doing it manually (whitespace all over the place), but I kept  
the history, just in case I got it wrong.  
  
The merge commit looks messy. But old and new diff actually should be  
identical.  
  
The old diff  
git diff -w c583bfa197ba...e62d657318dd8ea2  
  
The new diff  
git diff -w master...bucket_sorter

---

## Comment 75

> Username: jeremy-murphy  
> Created_at: 2022-03-16 22:46:21 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-1069715815  

Several checks fail -- any idea why?

---

## Comment 76

> Username: felix-salfelder  
> Created_at: 2022-03-16 22:49:42 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-1069718473  

On Wed, Mar 16, 2022 at 03:46:31PM -0700, Jeremy W. Murphy wrote:  
> Several checks fail -- any idea why?  
  
Yes, my bad. Leftover commas & stuff.  
  
Worth a couple more cycles before I give up..

---

## Comment 77

> Username: jeremy-murphy  
> Created_at: 2022-03-21 00:01:02 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-1073377647  

Thanks. I think the current CI failures are unrelated to your changes, although I'll have to confirm that before merging anything.

---

## Comment 78

> Username: felix-salfelder  
> Created_at: 2022-03-23 19:37:33 UTC  
> Url: https://github.com/boostorg/graph/pull/86#issuecomment-1076746613  

On Sun, Mar 20, 2022 at 05:01:12PM -0700, Jeremy W. Murphy wrote:  
> Thanks. I think the current CI failures are unrelated to your changes,  
> although I'll have to confirm that before merging anything.  
  
OK, there is no rush. Please do not change whitespace again, while this  
might need another rebase..

---
