---
date: '2015-06-08T13:18:40+09:00'
layout: post
published: true
qiita_article_id: 6163a38f0493065905eb
tags:
- Excel
- VBA
- ExcelVBA
- Visio
title: Visioファイル内の図形を列挙する。Excel VBAを使って。
updated: '2015-06-08T13:20:49+09:00'

---
# 概要  
  
Visioでぺたぺた設計書を作りました。  
見積り根拠とするために、設計書内の図形を数えたくなりました。[^1]  
  
[^1]: ほんとは、先に一覧ファイルを作ってからVisioに接続するのが美しいやりかた。 :sweat_smile:   
  
VBAでできそうだったので、Excelを使ってやってみました。  
  
# ソース  
  
## メインルーチン  
  
本体部分です。  
  
ファイルを選択し、Visioを起動し、図形を列挙してワークシートに記録します。  
  
例外処理とかエラー処理とか抜けてますが、とりあえずということで。 :smile:   
  
**CountVisioShapes.bas**  
```vbnet:CountVisioShapes.bas
Option Explicit

Public Sub CountVisioShapes()
    Dim fn As Variant
    
    Dim visioApp As visio.Application
    Dim visioDoc As visio.Document
    Dim visioPag As visio.Page
    Dim visioShp As visio.Shape
    
    Dim rs As New ResultSheet
    
    fn = Application.GetOpenFilename(FileFilter:="Visioファイル,*.vsdx", MultiSelect:=False)
    
    Set visioApp = CreateObject("Visio.Application")

    visioApp.Documents.OpenEx fn, visOpenRO + visOpenHidden
    
    Set visioDoc = visioApp.Documents.Item(1)

    For Each visioPag In visioDoc.Pages
        For Each visioShp In visioPag.Shapes
            rs.putPageName visioPag.Name
            rs.putShapeName visioShp.Name
            rs.putShapeText visioShp.Text
            rs.putMasterShape visioShp.MasterShape.Text
            rs.nextRow
        
        Next
    Next
    
    visioDoc.Close
    visioApp.Quit
    
    Set visioDoc = Nothing
    Set visioApp = Nothing
End Sub
```  
  
## ワークシートのラッパ  
  
ワークシートへ値を格納するのを簡単にするためのクラスです。  
  
**ResultSheet.cls**  
```vbnet:ResultSheet.cls
Option Explicit

Private currentRow As Long
Private startCol As Long
Private mySheet As Worksheet

Private Sub Class_Initialize()
    With Application.ActiveCell
        currentRow = .Row
        startCol = .Column
    End With
    
    Set mySheet = Application.ActiveSheet
    
    putHeaderRow
End Sub

Private Sub putHeaderRow()
    putCommon 0, "ページ名"
    putCommon 1, "シェイプ名"
    putCommon 2, "シェイプテキスト"
    putCommon 3, "マスターシェイプテキスト"
    nextRow
End Sub

Public Sub nextRow()
    currentRow = currentRow + 1
End Sub

Public Sub putPageName(s As String)
    putCommon 0, s
End Sub

Public Sub putShapeName(s As String)
    putCommon 1, s
End Sub

Public Sub putShapeText(s As String)
    putCommon 2, s
End Sub

Public Sub putMasterShape(s As String)
    putCommon 3, s
End Sub

Private Sub putCommon(sft As Integer, s As String)
    mySheet.Cells(currentRow, startCol + sft).Value = s
End Sub
```  
  
## 起動ボタン  
  
メインルーチンを起動するためのボタンを作りました。  
  
![excel-button.PNG](/assets/images/4976091d-eaf1-5dd0-6163-6cd97bab6a11.png)  
  
ボタンクリック時にメインルーチンを呼ぶようにします。  
  
****  
```vbnet:
Option Explicit

Private Sub SelectVisioFileButton_Click()
    Call CountVisioShapes.CountVisioShapes
End Sub
```  
  
# 実行結果  
  
作成したボタンを押すとファイル選択ダイアログが表示されますので、ファイルを選択するとアクティブセルに結果が出力されます。  
  
![countvisioshapes.PNG](/assets/images/f940a746-068b-a930-1c33-d6d9e3045aad.png)  
  
# 保存  
  
作成したファイルは「マクロ有効テンプレート」として保存しておくと便利です。  
