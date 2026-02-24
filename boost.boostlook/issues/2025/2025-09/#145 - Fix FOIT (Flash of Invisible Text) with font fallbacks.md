# #145 - Fix FOIT (Flash of Invisible Text) with font fallbacks [Closed]

> Username: julioest  
> Created at: 2025-09-29 19:17:15 UTC  
> Updated at: 2025-10-09 18:43:05 UTC  
> Closed at: 2025-10-09 18:43:05 UTC  
> Url: https://github.com/boostorg/boostlook/issues/145  

### Description  
On initial load, text is invisible until .woff fonts download (FOIT). We need to adjust CSS so system fonts show immediately, then swap to custom fonts.
