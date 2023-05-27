---
title: "UIをMayaで呼び出す"
---

最後に、Mayaで作成したwindowを呼び出していきます。


## 実行方法
スクリプトエディタで、下記のスクリプトを実行すると、UIを呼び出すことができます。
```py
from move_keyframes import interface
reload(interface)

interface.MainWindow()
```
では、細かく解説していきます。

# 実行までのプロセス

#### 1. スクリプトエディタを開く
MayaでUIを読びだすためには、ScriptEditorのpythonタブを利用します。
ヘルプラインの横にあるアイコンをクリックすると、ScriptEditorが開きます。
![](/images/edit-keyframes-in-a-scene/06_run_in_maya/2023-05-08-00-24-21.png)

#### 2. pythonタブにスクリプトをcopy&pasteする
pythonタブをクリックして、スクリプトをcopy&pasteしてください。
![](/images/edit-keyframes-in-a-scene/06_run_in_maya/2023-05-08-00-30-11.png)

#### 3. スクリプトを実行する
▷が２つついているボタンをクリックします。そうすると、スクリプトが実行されます。
![](/images/edit-keyframes-in-a-scene/06_run_in_maya/2023-05-08-00-39-54.png)
✅ windowが出てくれば、OKです。

#### 4. 最後に
最後に、実際に動くか確認してみましょう。
sphereか何かを作成し、動かしたいフレーム数を設定後、runボタンを押してみてください。

![](/images/edit-keyframes-in-a-scene/06_run_in_maya/2023-05-08-00-52-57.png)
✅ Script Editorに、`success: move all keyframes.`と表示され、keyframeが指定のframe数動いていれば、OKです。

::::message
reload(interface) 行について、
今後ファイルを編集する予定がないのであれば、削除していただいて構いません。
::::