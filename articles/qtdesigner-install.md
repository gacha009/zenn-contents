---
title: "qtDesigner.exeを自分でインストールする方法"
emoji: "🔰"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Maya, GUI]
published: true
---

# 概要

maya2019までは、mayaのインストール時にqtDesigner.exeも同梱されていましたが、
maya2020以降は、qtDesigner.exeは同梱されなくなりました。[^1]
筆者は、qtDesignerでGUIを作成していたので、この変化に困り果ててしまいました..。

この記事は、そんな方々向けにqtDesignerのインストールの方法をまとめたものになります。


# 結論

qtDesigner.exeを手に入れるには、以下の手順が必要です
1. pythonをダウンロード
2. 環境変数のPATHを通す
3. pysideかqt5をインストール

では、一つずつ解説していきます。


# 1. pythonをダウンロード

お使いのmayaに対応したpythonを[公式サイト](https://www.python.org/downloads/)からダウンロードしてください。

#### 1.1 インストーラーを取得
ページに行くと、たくさんのインストーラーがあります。
今回は、私の環境を例として進めていきたいと思います。
- windows 64bit
- maya2020
    * サポートしているversionは、python2.7.11

調べてみるとインストーラーは
python2.7.11のWindows x86-64 MSI installer になるようです。

::::message
maya2022以降は、python3がデフォルトになっているはずなので、
ご自身のmayaがサポートするpythonのversionをきちんと確認してください。
::::

#### 1.2 pythonをインストールする
1. 1.1でダウンロードしたファイルを実行します。
2. setup windowが出現するので【Install for all users】を有効にして、**Next**を押します。
3. install directoryは、既定(default)のままでいいので、**Next**を押します。
4. カスタマイズ設定も既定(default)のままでいいので、**Next**を押します。
5. インストールが始まります。
6. インストール終了を知らせるwindowが出るので、**Finish**を押して閉じます。

✅ Cドライブにpython27というフォルダが出来ていたらOKです。


# 2. 環境変数のPATHを通す
pipをインストールするためにコマンドプロンプトを使います。
コマンドプロンプトでpythonを扱うには、環境変数にpython pathを通す必要があります。

#### 1.1 .exe file の場所を確認
python実行時は、python.exeが必要になるので、pathは、この.exeファイルに通します。
.exeファイルは、pythonをインストールしたディレクトリに保存されています。
![](/images/qtDesigner-install/2022-12-11-17-13-16.png)

もう一つ、scriptsフォルダ以下にもpythonの管理システムが入っていて
そこにもpathを通す必要があるようです。
ひとまず、[C:\Python27]と[C:\Python27\scripts]をメモ帳にでも残しておきます。

#### 1.2 パスを通す
1. Windowsキーを押して【システム環境変数を編集】と打ち、Enterを押します。
2. システムのプロパティというwindowが立ち上がるので、詳細タブ>環境変数をクリックします。
![](/images/qtDesigner-install/2022-12-11-17-40-05.png =400x)
4. [環境変数]という名前のwindowが立ち上がるので、下側にあるシステム環境変数一覧の中にある[Path]をダブルクリック(又は、選択して編集をクリック)します。
6. [環境変数名の編集]というwindowが表示されるので、
    [新規(N)]を押して、[C:\Python27]をペーストします。
7. もう一度[新規(N)]を押して、今度は[C:\Python27\scripts]をペーストします。
8. OKを押して、windowを閉じます。


#### 1.3 コマンドプロンプトで確認
Windowsキーを押して [cmdと入力 → Enter] を押すと、コマンドプロンプトが起動します。
コマンドプロンプトでpythonと入力して、Enterを押します。
✅ 画像のように表示されていれば、環境変数のパスが通っています。
![](/images/qtDesigner-install/2022-12-11-18-32-37.png)

確認したら、[exitと入力 → Enter]でコマンドプロンプトを終了します。


# 3. pysideかqt5をインストール

コマンドプロンプトを使って、pysideか、qt5をインストールします。
今回は、pysideをインストールする方法を試します。

#### pysideをインストール

先程と同様に、
Windowsキーを押して[cmdと入力 → Enter]から再度コマンドプロンプトを起動します。
起動出来たら、下記のように入力し、Enterを押します。

``` :コマンドプロンプト
pip install pyside
```
<!-- ディレクトリを移動しなくても問題ないのかを確認して、問題なければ下記を表示-->
::::message
コマンドプロンプトが `C:\User\(username)>`となっている状態でも
そのまま上記を入力して頂いて大丈夫です
::::

コマンドプロンプトに下記のように表示されたら、無事インストール出来ています。
``` :コマンドプロンプト
Collecting pyside
  Using cached PySide-1.2.4-cp27-none-win_amd64.whl (45.0 MB)
Installing collected packages: pyside
Successfully installed pyside-1.2.4
```

# 4. 最後にdesigner.exeを確認
最後に[C:\Python27\Lib\site-packages\PySide]の中を確認しましょう。
designer.exeがあれば、OKです。ダブルクリックすると、QtDesignerが立ち上がります。
インストール作業はこれで完了です!! お疲れ様でした!!!
![](/images/qtDesigner-install/2022-12-18-15-28-33.png)




# おまけ
#### pipをアップグレード
pysideをインストール後に、pipをアップグレードしてください。というログが出たら
指示通り、pipをアップグレードします。
``` : アップデート指示ログ
You are using pip version 7.1.2, however version 22.3.1 is available.
You should consider upgrading via the 'python -m pip install --upgrade pip' command.
```
指示通りに入力してアップグレードしようとすると、syntaxErrorがでます。
これは、最新のpipがpython3系に対して用意されているモノだからのようです。
なので、以下の方法のどちらかでアップグレードします。

**方法1: pipのversionを指定してupgrade**
``` :
pip install --upgrade pip=20.3.4
```
**方法2: pipを再インストール**
``` :
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py
python get-pip.py
```




-----
#### 参考にさせていただいたサイト様
- [Python2.7のインストール：Python環境構築ガイド](https://www.python.jp/install/windows/install_py2.7.html)
- [Phthon+Pyside+QtDesignerで実行ファイルを作るまで](https://qiita.com/Fo-Ta/items/6158e57c76dffb63f6c8)
- [Python2.7にpipをインストールする](https://qiita.com/sg0hsmt/items/f8fc8d587bff816654a8)


[^1]: https://www.qt.io/ja-jp/blog/qt-offering-changes-2020