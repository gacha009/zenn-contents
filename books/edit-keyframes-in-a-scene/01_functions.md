---
title: "python script を作成する"
---

chapter0で書き出した要素をもとに、スクリプトを作成していきます。


## はじめに
最初に、ツールのフォルダ階層とツールに必要なファイルを作ってしまいます。
ツールの階層は、下図のようになります。
![](/images/edit-keyframes-in-a-scene/01_functions/2023-04-12-23-37-17.png)
mainwindow.ui以外を、画像の通りに %USERPROFILE%/Documents/maya/(maya_version)/scripts 以下に作成してください。
作成時、ファイルの拡張子を`.py`に指定するのを忘れないでください。

## ツールの処理部分
このチャプターでは、**functions.py**を作成します。
中身は以下の通りです。これを先ほど作成した、functions.pyにコピーして上書き保存してください。

```py: functions.py
# -*- coding; utf-8 -*-
import maya.cmds as cmds

start_frame = cmds.playbackOptions(q=True, minTime=True)
end_frame   = cmds.playbackOptions(q=True, maxTime=True)

# 動かしたいframe数
move_frame = 10.0 

# シーン内のアニメーションカーブをすべて取得
anim_curves = cmds.ls(type='animCurve') 

for ac in anim_curves:
    is_ref = cmds.referenceQuery(ac, isNodeReferenced=True)
    
    if is_ref:
        """ animCurveがリファレンスの場合はエラーになるので、対象外にする """
        continue

    cmds.keyframe(
        ac, 
        edit=True, 
        relative=True, 
        option='move', 
        time=(start_frame, end_frame),
        timeChange=move_frame)

print('success: move all keyframes.')
```
以下、処理ごとに解説していきます。


## シーン内のkeyframeを取得する
keyframeは、animCurveというノードで管理されています。
Nodeの取得は、`cmds.ls()`というコマンドを使って行えます。
また、このコマンドは、取得したいタイプを指定できるので、以下のように書くと、シーン内のすべてのanimCurveを取得することが出来ます。

```py:
anim_curves = cmds.ls(type='animCurve') 
```


## 取得したkeyframeを移動させる
keyframeを移動させるには、animCurveに登録されているkeyを編集すればOKです。
よくわからない方は、animCurveノードをMayaで選択して、AttributeEditorを見てみてください。
![](/images/edit-keyframes-in-a-scene/01_functions/2023-05-27-11-11-28.png)


keyを編集するためには、`keyframe()`コマンドを利用します。
先ほど取得したシーン内のすべてのanimCurveに対して編集を行うので、for文を使って処理を行います。
```py:
for ac in anim_curves:
    cmds.keyframe(
            ac, 
            edit=True, 
            relative=True, 
            option='move', 
            time=(start_frame, end_frame),
            timeChange=move_frame)
```
今回は、相対的にキーを動かしたいので、`relative=True`にしておきます。

#### 移動させたいframeの範囲を指定する
keyframeを移動させるには、どの範囲のキーフレームを動かすかを指定する必要があります。
keyframe()コマンドの`time`という引数で、範囲を指定することができます。
今回は、シーン全体のキーフレームを移動させたいので、画像の下線が引いてある数値を指定します。
![](/images/edit-keyframes-in-a-scene/01_functions/timeSlider.png)

下線部分を取得するには、`playbackOptions`を使用します。
```py:
start_frame = cmds.playbackOptions(q=True, minTime=True)
end_frame   = cmds.playbackOptions(q=True, maxTime=True)
```
注意してほしいのですが、`startFrame`と`endFrame`だと、外側のボックスで設定されている数値を取得してしまいます。`minTime`と`maxTime`を使用すると、内側のボックスの数値を取得できます。


## 動かしたいフレーム数を指定する
動かしたいフレーム数は、timeChangeで指定します。
直接入力するより変数化しておいた方が後々便利です。
```py:
move_frame = 10.0 
```

-----
書き出した要素は、これで終わりなのですが、
これだけだと、例えば、リファレンスしているリグに、driven keyが設定してある場合にエラーになってしまうので、フィルターを設定していきます。


## animCurve に対するフィルター
前述したように、リファレンスしているデータにanimCurveがある場合、編集することが出来ないので、for文でエラーを起こしてしまいます。
ですので、animCurveがリファレンスされているものかどうかを確認する必要があります。
```py:
for ac in anim_curves:
    is_ref = cmds.referenceQuery(ac, isNodeReferenced=True)
        
        if is_ref:
            continue
```
もし対象にしているanimCurveがリファレンスされているものであれば、continueを実行して、その後の処理は行わないようにします。


## 処理の最後に
`print()`を利用して、処理が最後まで行われたことを知らせます。
```py:
print('success: move all keyframes.')
```
printだとscriptEditorにしか表示されませんので、`maya.api.OpenMaya.MGlobal.displayInfo()`を使うのもいいかもしれません。

:::message
apiを使う場合は、モジュールのimportが必要です。
:::


## 最後に
シーン内のキーフレームを任意のフレーム数移動させるスクリプトの説明を行ってきました。
要素ごとにスクリプトを組み立てていくイメージはつきましたでしょうか。

スクリプトだけでもいいのですが、何度も使う可能性があるので、次はこのスクリプトのGUIを作っていきます。