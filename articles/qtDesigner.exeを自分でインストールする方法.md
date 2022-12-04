---
title: "qtDesigner.exeを自分でインストールする方法"
emoji: "⛳"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [zenn]
published: false
---

# 概要

maya2019までは、mayaのインストール時にqtDesigner.exeも同梱されていましたが、
maya2020以降は、qtDesigner.exeは同梱されなくなりました。
筆者は、qtDesignerでGUIを作成していたので、この変化に困り果ててしまいました..。

この記事は、そんな方々向けにqtDesignerのインストールの方法をまとめたものになります。


# 結論

qtDesigner.exeを手に入れるには、以下の手順が必要です
1. pythonをダウンロード
2. 環境変数のPATHを通す
3. pyside2かqt5をインストール

では、一つずつ解説していきます。


# 1. pythonをダウンロード

お使いのmayaに対応したpythonを公式サイトからダウンロードしてください。

::::message
maya2020~maya2022までは、python2.7, maya2023は、python3.nのはずなので
ご自身のmayaをきちんと確認してくださいませ。
::::

# 2. 環境変数のPATHを通す

pipをインストールするためにコマンドプロンプトを使うので、
環境変数にpython pathを通す必要があります。
環境変数名は、**PATH**です。

python環境の構築・環境変数については、下記を参考にしています。
📝[【Python環境構築】環境変数について解説！](https://web-camp.io/magazine/archives/19240)


# 3. pyside2かqt5をインストール

コマンドプロンプトを使って、pyside2か、qt5をインストールしていきます。

## pyside2をインストール

Windowsキーを押して、cmdと入力 → Enterを押すとコマンドプロンプトが起動します。
そこに、下記のように入力します。

``` :コマンドプロンプト
pip install pyside2
```
<!-- ディレクトリを移動しなくても問題ないのかを確認して、問題なければ下記を表示-->
::::message
コマンドプロンプトが `C:\User\(username)>`となっている状態でも
そのまま上記を入力して頂いて大丈夫です
::::