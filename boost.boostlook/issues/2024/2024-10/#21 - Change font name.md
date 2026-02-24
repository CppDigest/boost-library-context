# #21 - Change font name [Closed]

> Username: rbbeeston  
> Created at: 2024-10-01 00:47:46 UTC  
> Updated at: 2024-10-14 15:54:04 UTC  
> Closed at: 2024-10-14 15:54:04 UTC  
> Url: https://github.com/boostorg/boostlook/issues/21  

in boostlook some fonts are not loading, and the em tag is using an algorythmic italic  
  
Please update font name "Noto Sans Italic" to "Noto Sans Display"  so it uses the italic font as part of the normal font.  
  
Also check that in the standalone viewer that the fonts are being loaded

---

## Comment 1

> Username: Nerixyz  
> Created at: 2024-10-14 11:20:27 UTC  
> Url: https://github.com/boostorg/boostlook/issues/21#issuecomment-2410913519  

Could fonts be specified to be `font-display: swap` instead of `block` (http://github.com/boostorg/boostlook/blob/develop/boostlook.css#L153)? Currently, while fonts are loading, no text is displayed. `swap` would show a fallback font while loading.

---

## Comment 2

> Username: rbbeeston  
> Created at: 2024-10-14 15:34:26 UTC  
> Url: https://github.com/boostorg/boostlook/issues/21#issuecomment-2411613948  

@Nerixyz I would be ok with this, we also have a competing desire to be aware when the fonts are not loading correctly   
(at least in staging)    
  
Added to #1335
