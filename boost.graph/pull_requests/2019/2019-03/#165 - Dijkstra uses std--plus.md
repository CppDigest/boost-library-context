# #165 Dijkstra uses std::plus [Merged]

> Username: ahhz  
> Created at: 2019-03-08 18:01:32 UTC  
> Updated at: 2019-04-17 16:42:03 UTC  
> Merged at: 2019-04-17 16:18:31 UTC  
> Closed at: 2019-04-17 16:18:31 UTC  
> Url: https://github.com/boostorg/graph/pull/165  

This commit changes the relax function that dijkstra shortest paths uses to only update the distance and predecessor map of the target vertex of each edge. This has the benefit that from now on we can use std::plus as the default combine function.  
  
This supersedes  #48, see the discussion there

---

## Comment 1

> Username: anadon  
> Created_at: 2019-03-08 18:06:26 UTC  
> Url: https://github.com/boostorg/graph/pull/165#issuecomment-471021486  

@jzmaddock Stuff moved here.

---

## Comment 2

> Username: anadon  
> Created_at: 2019-04-05 20:45:38 UTC  
> Url: https://github.com/boostorg/graph/pull/165#issuecomment-480416363  

@jzmaddock Could you re-run the appveyor test?  It looks like it should have passed.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-04-06 07:16:19 UTC  
> Url: https://github.com/boostorg/graph/pull/165#issuecomment-480481216  

Looks like a SNAFU, restarted.

---

## Comment 4

> Username: ahhz  
> Created_at: 2019-04-06 10:50:15 UTC  
> Url: https://github.com/boostorg/graph/pull/165#issuecomment-480494423  

Thank you for getting rid of that hateful red x

---

## Comment 5

> Username: anadon  
> Created_at: 2019-04-06 14:13:02 UTC  
> Url: https://github.com/boostorg/graph/pull/165#issuecomment-480507367  

It looks like this is clear to merge into develop.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2019-04-12 17:10:57 UTC  
> Url: https://github.com/boostorg/graph/pull/165#issuecomment-482651693  

@Belcourt are you happy with this one now?

---

## Comment 7

> Username: Belcourt  
> Created_at: 2019-04-15 18:24:24 UTC  
> Url: https://github.com/boostorg/graph/pull/165#issuecomment-483364291  

I’m unable to review, I trust your judgment, go for it.  
  
Noel  
  
On Apr 12, 2019, at 11:10 AM, jzmaddock <notifications@github.com<mailto:notifications@github.com>> wrote:  
  
  
@Belcourt<https://github.com/Belcourt> are you happy with this one now?  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/graph/pull/165#issuecomment-482651693>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AA65bZYMyOdYZL2DJM8_hVX7EYLx2s6jks5vgL4igaJpZM4bl2Lr>.

---

## Comment 8

> Username: ahhz  
> Created_at: 2019-04-17 16:42:03 UTC  
> Url: https://github.com/boostorg/graph/pull/165#issuecomment-484168897  

Thanks!

---
