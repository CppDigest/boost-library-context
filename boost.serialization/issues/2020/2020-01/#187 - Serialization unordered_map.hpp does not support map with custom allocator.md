# #187 - Serialization unordered_map.hpp does not support map with custom allocator [Closed]

> Username: markroizman  
> Created at: 2020-01-09 15:58:40 UTC  
> Updated at: 2020-01-21 22:15:10 UTC  
> Closed at: 2020-01-15 21:20:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/187  

Hello,  
  
It looks like unordered_map.hpp was copied from unordered_set.hpp and type T parameter is missing. The fix is simple - just add and pass T.  
template<  
    class Archive,   
    class Key,   
    **class T,**  
    class HashFcn,   
    class EqualKey,  
    class Allocator  
>  
  
I attached a test case from the serialization tutorial - just replaced list by unordered_map with custom tbb::scalable_allocator.   
[Bug.gz](https://github.com/boostorg/serialization/files/4041235/Bug.gz)  
  
The same is for unordered_multimap.hpp  
  
Thank you,  
Mark

---

## Comment 1

> Username: robertramey  
> Created at: 2020-01-10 05:46:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/187#issuecomment-572883875  

How about tweaking the code, running the tests and submitting a PR?

---

## Comment 2

> Username: robertramey  
> Created at: 2020-01-12 21:16:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/187#issuecomment-573458050  

I'm not seeing that this requires any change to the serialization library itself.  Your change is in the example.  It's only an example which works with either type of collection.  In fact, you could have continued to use the std::list with your custom allocator:  
`//    std::list<bus_stop *> stops;  
    std::list<  
        bus_stop*,  
        tbb::scalable_allocator<bus_stop* >  
    > stops;  
`

---

## Comment 3

> Username: markroizman  
> Created at: 2020-01-14 18:39:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/187#issuecomment-574313792  

Hi Robert,  
  
Unfortunately, I don’t have boost setup to test the change. I don’t have even clean boost sources.  
Everything is supplied by our IT.  
I’ll be relatively big overhead for me to ramp up to the boost environment, install it and run tests for such a small change.  
Please consider asking somebody who can help.  
  
Thanks,  
Mark  
  
  
From: Robert Ramey <notifications@github.com>  
Sent: Friday, January 10, 2020 7:47 AM  
To: boostorg/serialization <serialization@noreply.github.com>  
Cc: Mark Roizman <mroizma@synopsys.com>; Author <author@noreply.github.com>  
Subject: Re: [boostorg/serialization] Serialization unordered_map.hpp does not support map with custom allocator (#187)  
  
  
How about tweaking the code, running the tests and submitting a PR?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_boostorg_serialization_issues_187-3Femail-5Fsource-3Dnotifications-26email-5Ftoken-3DAOHPETBVBXVSZLSDFCESPY3Q5ADTZA5CNFSM4KE2S33KYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEISYHIY-23issuecomment-2D572883875&d=DwMCaQ&c=DPL6_X_6JkXFx7AXWqB0tg&r=as5K4GZ7_E-A2tvnb6JPu1RJBW_HUJn7WqVL78iPGVQ&m=Kzy9IEKFyBwfXOGjOy2jmQTC7PU4dAq3VmpdU1XbVG0&s=Mc61-lf7xTfZYlRQfYqkuGW73MvbvniYoB-P6I0s_kc&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_AOHPETHNS22NCIAHSDV7VA3Q5ADTZANCNFSM4KE2S33A&d=DwMCaQ&c=DPL6_X_6JkXFx7AXWqB0tg&r=as5K4GZ7_E-A2tvnb6JPu1RJBW_HUJn7WqVL78iPGVQ&m=Kzy9IEKFyBwfXOGjOy2jmQTC7PU4dAq3VmpdU1XbVG0&s=Co14scQ2PmcySISU7WbPaA6PP55u05f-0aM0IifQfJE&e=>.

---

## Comment 4

> Username: robertramey  
> Created at: 2020-01-15 02:53:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/187#issuecomment-574471937  

My point above is that no changes are required in the library.  As you note it already supports custom allocators.  The example you site - is an example - not part of the library itself.  So you're all done!

---

## Comment 5

> Username: markroizman  
> Created at: 2020-01-15 09:15:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/187#issuecomment-574567203  

Hi Robert,  
  
Sorry for being unclear.  
There is missing template parameter in serialization/unordered_map.hpp. There must be 5, but in the boost, only 4 are defined and passed through.  
If you try my test case, you get compilation error.  
The fix is simple, you need to add and pass missing T.  
  
Thanks,  
Mark  
  
From C++ reference  
template<  
    class Key,  
    class T,  
    class Hash = std::hash<Key>,  
    class KeyEqual = std::equal_to<Key>,  
    class Allocator = std::allocator< std::pair<const Key, T> >  
> class unordered_map;  
  
In Boost  
template<  
    class Archive,  
    class Key,  
    class HashFcn,  
    class EqualKey,  
    class Allocator  
>  
  
  
From: Robert Ramey <notifications@github.com>  
Sent: Wednesday, January 15, 2020 4:54 AM  
To: boostorg/serialization <serialization@noreply.github.com>  
Cc: Mark Roizman <mroizma@synopsys.com>; Author <author@noreply.github.com>  
Subject: Re: [boostorg/serialization] Serialization unordered_map.hpp does not support map with custom allocator (#187)  
  
  
My point above is that no changes are required in the library. As you note it already supports custom allocators. The example you site - is an example - not part of the library itself. So you're all done!  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_boostorg_serialization_issues_187-3Femail-5Fsource-3Dnotifications-26email-5Ftoken-3DAOHPETHY32B7ACN346RXY63Q5Z3EFA5CNFSM4KE2S33KYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEI636AI-23issuecomment-2D574471937&d=DwMCaQ&c=DPL6_X_6JkXFx7AXWqB0tg&r=as5K4GZ7_E-A2tvnb6JPu1RJBW_HUJn7WqVL78iPGVQ&m=PGkHTbAv3L1K_GifUHS4g-a9v0WL4zXXlTKxHQRJRNA&s=Df97HC6KpZ_ZK8yg_xECwH4a0vnxup7gL0AxfypXgbY&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_AOHPETCCNL7VLHXIGKZI5P3Q5Z3EFANCNFSM4KE2S33A&d=DwMCaQ&c=DPL6_X_6JkXFx7AXWqB0tg&r=as5K4GZ7_E-A2tvnb6JPu1RJBW_HUJn7WqVL78iPGVQ&m=PGkHTbAv3L1K_GifUHS4g-a9v0WL4zXXlTKxHQRJRNA&s=3iYvK22iCLdg_cZ4mQS-HofreM9vJI-3fQM3woAEhvA&e=>.

---

## Comment 6

> Username: robertramey  
> Created at: 2020-01-15 21:20:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/187#issuecomment-574861134  

fixed / tested on my machine.  Will soon upload to develop branch.  So closing this for now.  Thanks for spotting this.

---

## Comment 7

> Username: markroizman  
> Created at: 2020-01-15 21:37:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/187#issuecomment-574867722  

Thank you very much  
Mark  
  
From: Robert Ramey <notifications@github.com>  
Sent: Wednesday, January 15, 2020 11:20 PM  
To: boostorg/serialization <serialization@noreply.github.com>  
Cc: Mark Roizman <mroizma@synopsys.com>; Author <author@noreply.github.com>  
Subject: Re: [boostorg/serialization] Serialization unordered_map.hpp does not support map with custom allocator (#187)  
  
  
Closed #187<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_boostorg_serialization_issues_187&d=DwMCaQ&c=DPL6_X_6JkXFx7AXWqB0tg&r=as5K4GZ7_E-A2tvnb6JPu1RJBW_HUJn7WqVL78iPGVQ&m=Jg5uAVq4ZoWwNxUJ16qi4LLadJEproOr5ZFbxCoXlAI&s=Qze9u_hq3-utVF1Xt0NDiNqeBffB8zKBedijfD8ratw&e=>.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_boostorg_serialization_issues_187-3Femail-5Fsource-3Dnotifications-26email-5Ftoken-3DAOHPETAFPXAECW3TX237CSTQ554YLA5CNFSM4KE2S33KYY3PNVWWK3TUL52HS4DFWZEXG43VMVCXMZLOORHG65DJMZUWGYLUNFXW5KTDN5WW2ZLOORPWSZGOV74WBOA-23event-2D2952356024&d=DwMCaQ&c=DPL6_X_6JkXFx7AXWqB0tg&r=as5K4GZ7_E-A2tvnb6JPu1RJBW_HUJn7WqVL78iPGVQ&m=Jg5uAVq4ZoWwNxUJ16qi4LLadJEproOr5ZFbxCoXlAI&s=LAhveCThas5XrvZLyhfF1qzgAZbARP4jPjjfSUvS_VU&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_AOHPETHNBGSEW3XEMJHKZHTQ554YLANCNFSM4KE2S33A&d=DwMCaQ&c=DPL6_X_6JkXFx7AXWqB0tg&r=as5K4GZ7_E-A2tvnb6JPu1RJBW_HUJn7WqVL78iPGVQ&m=Jg5uAVq4ZoWwNxUJ16qi4LLadJEproOr5ZFbxCoXlAI&s=FMNxC037ZNl45mb5G9VyfeCQi-AtjuHnYgZP23GWQW0&e=>.

---

## Comment 8

> Username: markroizman  
> Created at: 2020-01-21 15:07:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/187#issuecomment-576724202  

Hi Robert,  
  
I’ve just found out that the same problem exists in boost/serialization/boost_unordered_map.hpp  
And this file has additional problem – wrong #define, which is the same as in unordered_map.hpp but should have 2x SERIALIZATION_  
#define BOOST_SERIALIZATION_ SERIALIZATION_UNORDERED_MAP_HPP  
  
Please check.  
Thanks,  
Mark  
  
From: Mark Roizman  
Sent: Wednesday, January 15, 2020 11:37 PM  
To: boostorg/serialization <reply@reply.github.com>  
Subject: RE: [boostorg/serialization] Serialization unordered_map.hpp does not support map with custom allocator (#187)  
  
Thank you very much  
Mark  
  
From: Robert Ramey <notifications@github.com<mailto:notifications@github.com>>  
Sent: Wednesday, January 15, 2020 11:20 PM  
To: boostorg/serialization <serialization@noreply.github.com<mailto:serialization@noreply.github.com>>  
Cc: Mark Roizman <mroizma@synopsys.com<mailto:mroizma@synopsys.com>>; Author <author@noreply.github.com<mailto:author@noreply.github.com>>  
Subject: Re: [boostorg/serialization] Serialization unordered_map.hpp does not support map with custom allocator (#187)  
  
  
Closed #187<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_boostorg_serialization_issues_187&d=DwMCaQ&c=DPL6_X_6JkXFx7AXWqB0tg&r=as5K4GZ7_E-A2tvnb6JPu1RJBW_HUJn7WqVL78iPGVQ&m=Jg5uAVq4ZoWwNxUJ16qi4LLadJEproOr5ZFbxCoXlAI&s=Qze9u_hq3-utVF1Xt0NDiNqeBffB8zKBedijfD8ratw&e=>.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_boostorg_serialization_issues_187-3Femail-5Fsource-3Dnotifications-26email-5Ftoken-3DAOHPETAFPXAECW3TX237CSTQ554YLA5CNFSM4KE2S33KYY3PNVWWK3TUL52HS4DFWZEXG43VMVCXMZLOORHG65DJMZUWGYLUNFXW5KTDN5WW2ZLOORPWSZGOV74WBOA-23event-2D2952356024&d=DwMCaQ&c=DPL6_X_6JkXFx7AXWqB0tg&r=as5K4GZ7_E-A2tvnb6JPu1RJBW_HUJn7WqVL78iPGVQ&m=Jg5uAVq4ZoWwNxUJ16qi4LLadJEproOr5ZFbxCoXlAI&s=LAhveCThas5XrvZLyhfF1qzgAZbARP4jPjjfSUvS_VU&e=>, or unsubscribe<https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_notifications_unsubscribe-2Dauth_AOHPETHNBGSEW3XEMJHKZHTQ554YLANCNFSM4KE2S33A&d=DwMCaQ&c=DPL6_X_6JkXFx7AXWqB0tg&r=as5K4GZ7_E-A2tvnb6JPu1RJBW_HUJn7WqVL78iPGVQ&m=Jg5uAVq4ZoWwNxUJ16qi4LLadJEproOr5ZFbxCoXlAI&s=FMNxC037ZNl45mb5G9VyfeCQi-AtjuHnYgZP23GWQW0&e=>.

---

## Comment 9

> Username: robertramey  
> Created at: 2020-01-21 22:15:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/187#issuecomment-576909997  

OK  - fixed and bumped serialization library version
