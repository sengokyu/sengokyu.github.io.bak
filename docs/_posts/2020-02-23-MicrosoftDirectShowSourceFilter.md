---
'created_at: ': '2020-02-23T13:41:53+09:00'
layout: post
published: 'true'
tags: !!python/object/apply:builtins.map
- !!python/name:__main__.%3Clambda%3E ''
- !!python/object/apply:builtins.iter
  args:
  - - name: Windows
      versions: []
    - name: DirectX
      versions: []
    - name: DirectShow.NET
      versions: []
  state: 0
title: "[Microsoft] DirectShow Source Filter \u30E1\u30E2"
'updated_at: ': '2020-02-23T13:41:53+09:00'

---
https://stackoverflow.com/questions/1801881/how-to-make-a-video-mini-filter-driver  
  
Question:   
  
> I need to create an application/driver in Windows that captures the video stream from a webcam, performs some modifications and passes it back on to the system (so it streams out to say skype).  
  
Answor:  
  
> Look at the DirectShow. You need to create a source filter (which gets data from the camera source filter and modifies it).  
  
---  
  
How to Write a Source Filter for DirectShow  
  
https://docs.microsoft.com/en-us/windows/win32/directshow/how-to-write-a-source-filter-for-directshow  
  
----  
  
DirectShow.NET  
  
https://sourceforge.net/projects/directshownet/  
  
