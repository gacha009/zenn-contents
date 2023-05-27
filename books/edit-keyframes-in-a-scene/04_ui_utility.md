---
title: "UIをMayaで読み込む(Utility編)"
---

前回のチャプターで作成したUIは、そのままだとMayaでは読み込めません。
これ以降のチャプターでは、作成したUIをMayaで呼び出す為の準備を行っていきます。

:::details このチャプターの目次
[uiファイルのコンバート](#uiファイルのコンバート)
:::

## 結論
Mayaで読み込むために必要なことは、下記の二つです。
- .uiファイルのコンバート
- windowとして表示するためのClassの作成

両方一気に説明しようと思ったのですが、
長くなってしまったので、一つずつ解説していこうと思います。


# uiファイルのコンバート
先ほど作ったUIは、xmlファイルです。このままだとmayaで読み込むことができません。
ですので、mayaが読み込めるようにpythonファイルに変換する必要があります。
以下のスクリプトは、xmlファイルのコンバートを行う為のモノになります。
こちらを、最初に作ったui_utility.pyにコピーして保存してください。

```py:ui_utility.py
# -*- coding: utf-8 -*-
""" 
UI に関するファンクション
"""
from .common import *
from PySide2 import QtWidgets
from pyside2uic import compileUi
from xml.etree.ElementTree import parse
from StringIO import StringIO


def ui_compiler(ui_fullpath):
    """
    uiのコンパイル
    
    return:
        form_class
            uiのform class
        base_class
            uiのbase class
    """
    doc = parse(ui_fullpath)
    widget_class_name = doc.find('widget').get('class')
    form_class_name = doc.find('class').text

    with open(ui_fullpath, 'r') as f:
        o = StringIO()              # 空のStringIOオブジェクトを作成
        g = {}
        compileUi(f, o, indent=4)   # .uiファイルをpythonにコンバート
        code = o.getvalue()         # コンバートfileの中身全体を取得
        pyc = compile(code, '<string>', 'exec') # uiをコンパイル

        exec pyc in g               # 値を参照しながら、ファイルを実行(同時に値を格納)
        
        form_class = g['Ui_{}'.format(form_class_name)]
        base_class = getattr(QtWidgets, widget_class_name)

        return form_class, base_class
    return None, None
```
uiファイルのform_classとbase_classを取得するために、uiファイルを読み込んで、pythonファイルに変換しています。
スクリプトの中身を詳しく解説しようとすると、今回のチャプターでは足りないので、今回は一部だけ解説いたします。
下図が、pythonでコンバートされたUIの中身の一部です。
![](/images/edit-keyframes-in-a-scene/04_ui_utility/2023-05-27-14-29-14.png)

windowとして表示するためのClassは、この`Ui_MainWindow`を継承する必要があります。
また、setupUi()をClass内で呼び出しますので、引数になっている`MainWindow`にウィジェットを指定しなければなりません。

`form_class`と`base_class`は、それらをそれぞれ指定しています。
中身を確認してみましょう。
`form_class = <class 'Ui_MainWindow'>`
`base_class = <class 'PySide2.QtWidgets.QMainWindow'>` となっていると思います。


::::message
returnは、ui_compilerが呼び出されたときに、返すモノを指定できる命令です。
::::

次は、windowとして表示するために、Classを作っていきます。
