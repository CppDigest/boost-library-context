# #916 Github Actions: commitbot runs conditionally on boostorg/boost repo [Merged]

> Username: sdarwin  
> Created at: 2024-05-28 20:34:26 UTC  
> Updated at: 2024-05-28 23:00:53 UTC  
> Merged at: 2024-05-28 23:00:53 UTC  
> Closed at: 2024-05-28 23:00:53 UTC  
> Url: https://github.com/boostorg/boost/pull/916  

Nearly all developers who fork the superproject would not need their own version of commit-bot running.     
  
Usually it doesn't work. The token is missing. Or, if it is configured to run successfully, that will cause divergent commits in the fork.    
  
As shown, what this pull request does is have the commit-bot check it's in "boostorg/boost".  @pdimov please merge this, whenever convenient. It should be observed over the subsequent few hours to be sure the bot continues to run.

---
