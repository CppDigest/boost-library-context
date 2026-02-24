# #676 - Nsphere Usage [Closed]

> Username: DinosL  
> Created at: 2020-03-05 16:09:30 UTC  
> Updated at: 2020-04-19 03:15:33 UTC  
> Closed at: 2020-04-19 03:14:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/676  

Dear all,  
  
I am trying to use nsphere to query an rtree with a circle with a standard radius. First of all, I was unable to import nsphere. I looked at the directory where boost is installed and I couldn't find a folder named "extensions". How can I use nsphere? Is there another way to achieve my goal without using nsphere?  
  
Thank you in advance

---

## Comment 1

> Username: mloskot  
> Created at: 2020-03-05 16:10:42 UTC  
> Updated at: 2020-03-05 16:11:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/676#issuecomment-595311101  

Please, ask questions via the mailing list, https://lists.boost.org/geometry/ or https://lists.boost.org/boost-users/

---

## Comment 2

> Username: awulkiew  
> Created at: 2020-03-05 16:42:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/676#issuecomment-595327630  

@mloskot Why do you think such questions should be asked at the mailing list? We had several questions at GitHub already and I think this is a good place for them since the Geometry mailing list is more or less dead.

---

## Comment 3

> Username: DinosL  
> Created at: 2020-03-05 16:44:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/676#issuecomment-595328932  

> @mloskot Why do you think such questions should be asked at the mailing list? We had several questions at GitHub already and I think this is a good place for them since the Geometry mailing list is more or less dead.  
  
Thank you. Mailing lists are a thing of the past and not user friendly.

---

## Comment 4

> Username: awulkiew  
> Created at: 2020-03-05 16:54:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/676#issuecomment-595333681  

@DinosL Extensions only exist in the develop branch of Boost.Geometry so you either has to clone the git repo or copy the code from the develop branch and make sure it works well with your version of Boost.  
  
A workaround would be to create a box enclosing the circle and use `bgi::satisfies` to filter out the elements that are outside of it while performing the query, so something like:  
```  
template <typename T> inline T sqr(T const& v) { return v * v; }  
  
// ...  
  
box_type box{{center_x - r, center_y - r}, {center_x + r, center_y + r}};  
double r_sqr = sqr(r);  
rtree.query(bgi::intersects(box) && bgi::satisfies([](auto const& pt_elem) {  
    return sqr(bg::get<0>(pt_elem) - center_x) + sqr(bg::get<1>(pt_elem) - center_y) <= r_sqr;  
}), std::back_inserter(result));  
```

---

## Comment 5

> Username: mloskot  
> Created at: 2020-03-05 16:54:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/676#issuecomment-595333866  

@awulkiew I applied what I know as common for Boost. My bad, I'll reopen.  
  
@DinosL   
> Mailing lists are a thing of the past and not user friendly.  
  
No, they are not.  
GitHub is very unfriendly if it comes to discussions as they do not offer threading what is a major PITA if threads get long and eventually tracking discussion flow becomes impossible.  
  
It may be friendly from your POV, from POV of a 'one night git', when one jumps in, asks question, gets solution and disappears, then why bother subscribing. Then, mind you, maintainers are left with GitHub polluted with non-issues garbage that obscures any search attempt. There is [StackOverflow with boost-geometry](https://stackoverflow.com/questions/tagged/boost-geometry) tag for that.

---

## Comment 6

> Username: DinosL  
> Created at: 2020-03-05 17:02:20 UTC  
> Updated at: 2020-03-05 17:03:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/676#issuecomment-595337766  

> @awulkiew I applied what I know as common for Boost. My bad, I'll reopen.  
>   
> @DinosL  
>   
> > Mailing lists are a thing of the past and not user friendly.  
>   
> No, they are not.  
> GitHub is very unfriendly if it comes to discussions as they do not offer threading what is a major PITA if threads get long and eventually tracking discussion flow becomes impossible.  
>   
> It may be friendly from your POV, from POV of a 'one night git', when one jumps in, asks question, gets solution and disappears, then why bother subscribing. Then, mind you, maintainers are left with GitHub polluted with non-issues garbage that obscures any search attempt. There is [StackOverflow with boost-geometry](https://stackoverflow.com/questions/tagged/boost-geometry) tag for that.  
  
That's why I wrote user friendly and not moderator friendly.  
  
@awulkiew Thank you for your suggestion. Maybe I missed the part that say about the develop branch. If so, my bad for bothering.  I want my solution to be as fast as possible so I will benchmark both techniques if possible. I will then post my results and be like the one night git dude that texts the next morning :)

---

## Comment 7

> Username: awulkiew  
> Created at: 2020-03-05 17:15:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/676#issuecomment-595344720  

@mloskot Right, I get your point. Indeed SO would probably be better for this kind of questions. However in this case the question is about the extensions which are a part of the develop branch, so not something for a regular user.  
  
AFAIU the problem is that currently Issues are replacing other means of communication and are used for at least 3 purposes traditionally handled separately:  
- bug reporting system, (instead of trac)  
- communication with the users, (instead of Boost-users mailing list)  
- proposals, discussions regarding development (instead of Geometry mailing list)  
  
Which of these do you think are valid and which are not?  
Do we have a clear policy for handling GitHub issues written somewhere?  
  
We could encourage using Gitter for the users but current practice shows that it's for discussing GSoC (maybe?).  
  
Btw, @DinosL you can see here how a discussion about the NSphere became a discussion about the GitHub issues policy proving @mloskot 's point. ;)

---

## Comment 8

> Username: mloskot  
> Created at: 2020-03-05 17:39:07 UTC  
> Updated at: 2020-03-05 17:40:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/676#issuecomment-595355684  

@awulkiew  
> the problem is that currently Issues are replacing other means of communication  
  
Yes, I get that.  I'm either not convinced about such direction (due to GitHub lacking features) or I'm too allergic to treating open sources projects as software vendors.   To me, GitHub is a space where software development happens. Often such questions do not come from long-time users who also attempt to answer other questions, bring ideas, etc. A value that such non-issue ad-hoc questions bring to a project is negative, in majority those are developers' energy and timer sucker.    
Although, in my early forties, I may have become a grumpy fart, I'm not alone thinking that is one of problems of today open source ecosystem.  
  
For such questions, there is the StackOverflow, where, by the way we both are active, where also Sehe (Boost fellow) answers lots of questions, so SO has high potential for providing an answer.  
  
> Which of these do you think are valid and which are not?  
> Do we have a clear policy for handling GitHub issues written somewhere?  
  
Ideally, if all of them happened on GitHub as in a single place of truth. I just can't help the fact GitHub is too simplistic for discussions, for my taste. Imagine Boost runs reviews as comments on GitHub issue :) However, perhaps this is a non-issue for Geometry, perhaps discussions are not too long or too complex. I'm ready to accept that and adjust my perspective :-)  
  
I don't have any policies, I just have learned when in Rome, dance like Romans do. So, I've been dancing as Boost folks do for longer than a decade.  
  
BTW, the list may be quiet but there are 250 people subscribed.  
  
> We could encourage using Gitter   
  
Although it's great for real time discussions, I have one problem with Gitter: lack of searchable archives. That is why I proposed to move Boost.GIL chats to #boost-gil at cpplang.slack.com.
