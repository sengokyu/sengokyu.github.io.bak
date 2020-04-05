---
date: '2020-02-23T13:41:53+09:00'
layout: post
published: false
qiita_article_id: e2b612ff01e8d2d08007
tags:
- Windows
- DirectX
- DirectShow.NET
title: '[Microsoft] DirectShow Source Filter メモ'
updated: '2020-02-23T13:41:53+09:00'

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
  
