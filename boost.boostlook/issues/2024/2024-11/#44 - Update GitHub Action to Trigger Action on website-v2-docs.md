# #44 - Update GitHub Action to Trigger Action on website-v2-docs [Closed]

> Username: julioest  
> Created at: 2024-11-19 18:53:25 UTC  
> Updated at: 2024-11-26 23:48:22 UTC  
> Closed at: 2024-11-26 23:48:22 UTC  
> Url: https://github.com/boostorg/boostlook/issues/44  

Add an action that triggers a workflow in the website-v2-docs repository whenever the develop branch is updated. This ensures the latest boostlook.css is fetched automatically, keeping the website documentation consistently up-to-date with the most recent styles and changes.

---

## Comment 1

> Username: julioest  
> Created at: 2024-11-23 00:24:11 UTC  
> Url: https://github.com/boostorg/boostlook/issues/44#issuecomment-2495140430  

@sdarwin How does my testing look?   
  
**boostlook action**  
https://github.com/julioest/boostlook/actions/runs/11982192376/job/33409804607  
  
**commit boostlook.css in website-v2**   
https://github.com/julioest/website-v2/commit/6fe928b944ff7f5732236b558434b2f13f55e10c  
  
**website-v2-docs actions**  
https://github.com/julioest/website-v2-docs/actions

---

## Comment 2

> Username: sdarwin  
> Created at: 2024-11-23 00:47:57 UTC  
> Url: https://github.com/boostorg/boostlook/issues/44#issuecomment-2495159255  

Very nice!    
  
A few comments based on https://github.com/julioest/boostlook/blob/develop/.github/workflows/update-website-v2.yml (if that's the file being used).  
  
`name: Install GitHub CLI` is probably not needed. The hosted runner will include `gh`.  
  
`name: Authenticate GitHub CLI`  Perhaps can be removed also.  Earlier you had used this method which may be enough for authentication:  
  
```  
        env:  
          GH_TOKEN: ${{ secrets.WEBSITE_V2_PAT }}  
```  
  
Let's try it out next week.

---

## Comment 3

> Username: sdarwin  
> Created at: 2024-11-23 00:51:14 UTC  
> Url: https://github.com/boostorg/boostlook/issues/44#issuecomment-2495161681  

Edit:   those auth methods could be equally valid. So there is no preference, one or the other.
