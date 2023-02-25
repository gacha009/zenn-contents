---
title: "python script を作成する"
---

:::details このチャプターの目次
[シーン内のkeyframeを取得する](#シーン内のkeyframeを取得する)
:::

chapter0で書き出した要素をもとに、スクリプトを作成していきます。
- シーン内のkeyframeを取得する
- 取得したkeyframeを移動させる
- 動かしたいフレーム数を指定する


# 結論
結果は、以下の通りになります。
一つずつ解説していきます。

```py: script.py
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

print('success: move all keyframe.')
```

# シーン内のkeyframeを取得する
keyframeは、animCurveというノードで管理されています。
```py:
anim_curves = cmds.ls(type='animCurve') 
```
なので、`cmds.ls()`でタイプを指定すると、シーン内のすべてのanimCurveを取得することが出来ます。


# 取得したkeyframeを移動させる
keyframeを移動させるには、animCurveに登録されているkeyを編集すればOKです。
編集するためには、`keyframe`コマンドを利用します。
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
`time`については、シーンのスタートフレームとエンドフレームを指定します。
![](/images/edit-keyframes-in-a-scene/timeSlider.png)


