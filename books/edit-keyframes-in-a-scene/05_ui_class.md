---
title: "UIをMayaで読み込む(Class編)"
---

このチャプターでは、Mayaで、windowとして表示するための方法を解説していきます。

:::details このチャプターの目次
[windowとして表示するためのClassの作成](#windowとして表示するためのclassの作成)
[moduleの読み込み](#moduleの読み込み)
[class作成の前準備](#class作成の前準備)
[classの作成](#classの作成)
[UIのelementと命令](#uiのelementと命令)
:::

## はじめに
MayaでUIを読み込むためには、classを設定しなければなりません。
ひとつ前のチャプターは、このclassを設定するための準備になります。
では、早速Classを作っていきましょう。

# windowとして表示するためのClassの作成
最初に作ったファイルの内の、interface.pyに書き込んでいきます。
以下のスクリプトを、interface.pyにコピーして保存してください。

```py:interface.py
# -*- coding: utf-8 -*-
import maya.cmds as cmds
import os
import sys
import uiutility
import functions
reload(uiutility)
reload(functions)
sys.dont_write_bytecode = True

# about ui
BASEFOLDER = os.path.dirname(__file__)
UI_NAME = 'mainwindow.ui'
UI_PATH = os.path.join(BASEFOLDER, UI_NAME)
uiutility.close_qt_window(UI_NAME)

FORM_CLASS, BASE_CLASS = uiutility.ui_compiler(UI_PATH)

class MainWindow(FORM_CLASS, BASE_CLASS):

    def __init__(self, parent=None):

        # Mayaに帰属させる
        parent = uiutility.get_maya_window()
        # initialize
        super(FORM_CLASS, self).__init__(parent)
        self.setupUi(self)
        # UIの名前を設定
        self.setObjectName(UI_NAME)
        self.setWindowTitle(UI_NAME)
        
        self._connect_widgets()
        self.show()
    
    def _connect_widgets(self):
        self.pushButton.clicked.connect(self.move_allkeys)
    

    def move_allkeys(self):
        move_frame = self.lineEdit.text()
        functions.main(int(move_frame))
```

このようにClassを作ることで、MAYAでUIを呼び出すことが出来ます。
一つずつ解説していきます。

---
#### moduleの読み込み
```py
# -*- coding: utf-8 -*-
import maya.cmds as cmds
import os
import sys
import uiutility
import functions
reload(uiutility)
reload(functions)
```

ここでは、モジュールの読み込みをしています。
`import`だけだと、一回だけしか読み込まないので、スクリプトを編集した場合に、それを反映してくれません。`reload`は、実行時に再度読み込みを行ってくれるので、スクリプトの編集中は記述しておくことをお勧めします。

---
#### Class作成の前準備
```py
# about ui
BASEFOLDER = os.path.dirname(__file__)
UI_NAME = 'mainwindow.ui'
UI_PATH = os.path.join(BASEFOLDER, UI_NAME)
uiutility.close_qt_window(UI_NAME)

FORM_CLASS, BASE_CLASS = uiutility.ui_compiler(UI_PATH)
```
#### 目的
ここの記述は、最終行の`FORM_CLASS, BASE_CLASS = uiutility.ui_compiler(UI_PATH)`を行うことを目的としています。
`FORM_CLASS` と `BASE_CLASS` は、UIでMayaを呼び出すために必要な素材です。
最終行では、ひとつ前のチャプターで保存した`uiutility.py`の`ui_compiler(ui_fullpath)`という関数を呼び出し、`FORM_CLASS` と `BASE_CLASS` の値を取得しています。

`ui_compiler(ui_fullpath)`を実行するためには、引数(`ui_fullpath`)を指定しなくてはなりません。
最終行までの記述は、一部を除いて`ui_fullpath`を求める為の過程だと思ってください。

:::message
`ui_fullpath`は、uiファイルのフルパスを指定します。
:::

#### スクリプトの解説
`__file__`は、実行しているファイル自身です。今回の場合は、interface.pyになります。
なので、`BASEFOLDER = os.path.dirname(__file__)`は、interface.pyがあるディレクトリのパスを取得しています。

`UI_NAME`は、先ほど作成した.uiファイルの名前を指定します。
私は`mainwindow.ui`という名前で保存しましたので、そのように記述しています。

`UI_PATH = os.path.join(BASEFOLDER, UI_NAME)`は、`BASEFOLDER`と`UI_NAME`を連結させています。
つまり、`interface.py があるディレクトリのパス(BASEFOLDER)` + `UIのファイル名(UINAME)` = **uiのfullpath** となります。

一部を除いて、と記載したのは、`uiutility.close_qt_window(UI_NAME)`は、pathを求めるものではないからです。
これは、もし`UI_NAME`と同じ名前のUIが既に起動していた場合に、そのUIを削除する為の記述です。これを書いておくことで、**windowの重複を防いでいます。**

:::message
 `uiutility.ui_compiler(UI_PATH)`と記載していますが、
これは、`uiutility.ui_compiler(ui_fullpath=UI_PATH)`と同様になります。
:::

---
#### Classの作成
```py
class MainWindow(FORM_CLASS, BASE_CLASS):

    def __init__(self, parent=None):
        # Mayaに帰属させる
        parent = uiutility.get_maya_window()
        # initialize
        super(FORM_CLASS, self).__init__(parent)
        self.setupUi(self)
        # UIの名前を設定
        self.setObjectName(UI_NAME)
        self.setWindowTitle(UI_NAME)
        
        self._connect_widgets()
        self.show()
```


準備ができたので、Classを作っていきます。
きちんと理解するのはUIをバリバリ使い始めてからでもいいと思うので、ここではざっくり解説します。
まずは、これを型として覚えて、ツール作りに慣れていく方が大事です。

#### スクリプトの解説
1行目は、classの名前と何を継承するのかを設定しています。
ここでは、`FORM_CLASS`と`BASE_CLASS`を継承させています。

`def`と、ついているものは、**関数**といいます。
`__init__`は、classを宣言するときは必須になります。これは、classの初期化を行う関数なので、classを呼び出すと同時に`__init__`内の処理も行われます。

`super`の行は、classの初期化を行っています。
`FORM_CLASS`と`BASE_CLASS`の記述が逆になったりすると初期化がうまくいきませんので、気を付けてください。
`self.setupUi(self)`で、uiの中身を呼び出しています。

`parent = uiutility.get_maya_window()`は、UIをMayaに帰属させています。
qtDesignerでuiを作る場合は、pysideという言語を使用することになるので、Mayaの子供としてUIの親子関係を設定する必要があります。(設定しないと、UIが消えてしまいます)

コメントに`# UIの名前を設定`とある2行は、UIのオブジェクト名とウィンドウに表示されるタイトルを設定しています。
`self._connect_widgets()`は、後で説明するので、飛ばします。

**最後に、**`self.show()`で、ウィンドウを表示させます。

---
#### UIのelementと命令
```py
    def _connect_widgets(self):
        self.pushButton.clicked.connect(self.move_allkeys)
    
    def move_allkeys(self):
        move_frame = self.lineEdit.text()
        functions.main(int(move_frame))
```

UIを呼び出す処理は、先ほど完了しました。
でもこのままでは、ボタンを押しても何の反応もありません。
なので、次は、ボタンを押したときの処理を設定します。

#### スクリプトの解説
`_connect_widgets`は、ボタンの動作について、処理を設定する関数です。関数の名前は任意です。
`self.pushButton.clicked.connect(self.move_allkeys)`は、ボタンを押したときの処理を設定しています。ボタンを押すと、()内の`self.move_allkeys`が実行されます。

`self.move_allkeys`を()内に設定すると、関数の`def move_allkeys(self)`が実行されます。
`move_allkeys()`の中身は、呼び出された(実行された)時点のlineEditの状態を読み取り、
数値に変換して、functionsのmain関数を実行する。というものです。

これで、動かしたい数値を設定してボタンを押せば、シーン内のすべてのキーフレームを動かすことのできるGUIの完成です！
