# #598 - Replace CMakeSettings.json with CMakePresets.json [Open]

> Username: mloskot  
> Created at: 2021-04-23 06:05:29 UTC  
> Updated at: 2021-04-23 06:07:45 UTC  
> Url: https://github.com/boostorg/gil/issues/598  

https://devblogs.microsoft.com/cppblog/cmake-presets-integration-in-visual-studio-and-visual-studio-code/  
  
> CMakePresets.json will be a recommended alternative to CMakeSettings.json.  
> Visual Studio will  never read from both CMakePresets.json and CMakeSettings.json at the same time.  
  
It's a good idea to replace our [example/CMakeSettings.json](https://github.com/boostorg/gil/blob/0dc68398b8c01fc0907906be145b6e8dbf77e753/example/cmake/CMakeSettings.json) with the presets file.
