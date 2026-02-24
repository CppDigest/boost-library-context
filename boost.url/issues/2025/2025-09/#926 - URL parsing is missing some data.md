# #926 - URL parsing is missing some data [Closed]

> Username: 0xdddddddd  
> Created at: 2025-09-14 15:23:57 UTC  
> Updated at: 2026-02-20 23:41:30 UTC  
> Closed at: 2026-02-20 23:41:30 UTC  
> Url: https://github.com/boostorg/url/issues/926  

The current 1.89 version of boost:: urls:: url-view parsing is missing the URL. Resolution of Path and Filename  
  
example:` rtmp://push-rtmp-hs-f5.douyincdn.com/thirdgame?stream-117965406598857482?arch_hrchy=w1&exp_hrchy=w1&expire=1758426355&sign=7dbc2a8011a0faf01a5a22420b981d0c&t_id=037-20250914114554B4B6BF65BAD334798EBA-pgR29B&volcSecret=7dbc2a8011a0faf01a5a22420b981d0c&volcTime=1758426355`  
Unable to extract Path:/thirdgame and Filename: thirdgame
