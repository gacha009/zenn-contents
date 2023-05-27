---
title: "ツールのUIを作成する"
---


先ほどのチャプターで、UIの設計を行いました。
このチャプターでは、設計したUIを作成する方法を解説していきます。

## はじめに
このチャプターでは、qtDesignerを使ってUIを作成していきます。
作るための考え方を一緒に解説していきますので、必ずしもqtDesignerを使う必要はありません。
ですが、もし良かったら、いい機会ですので挑戦してみてください。
[qtDesignerをインストールする方法の記事](#https://zenn.dev/gacha0923/articles/qtdesigner-install)


## GUI
最終的には、下図のようなUIを作成することを目指します。
![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-12-22-56-19.png)



# UIを作成する
では、早速UIを作成していきましょう。

#### 0. Qt Designerを立ち上げる
![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-06-22-46-46.png =500x)
   注意していただきたいのが、exe fileの名前は、**designer.exe**だということです。
   qtDesigner.exeで検索しても引っかからない可能性があるので、注意してください。
   私の記事を参考にしてインストールなさっている場合、
   exe fileは、`C:\Python27\Lib\site-packages\PySide`以下にあると思いますので、確認してみてください。

#### 1. MainWindowを作成する
   初めにNew Formというwindowが出てきます。
   templates\forms以下の**Main Window**を選択して、**Create**を押してください。
   すると、下図のようなWindowが出現します。
   ![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-06-22-50-22.png =500x)
   これが土台になります。この中に、button等のwidgetを追加していきます。


#### 2. 移動フレーム数を指定するためのWidgetを追加する
UIで数字を指定するときは、qtDesinerの**Line Edit**というwidgetが使えます。
![](/images/edit-keyframes-in-a-scene/03_create_ui/about_line_edit.png) 
Text Editでも良いのですが、複数行になることはないので、LineEditで十分です。

lineEditのみだと、何のための入力欄かわからないので、displayWidgets>Label を使って
何のためのモノなのかを解説しておきます。
![](/images/edit-keyframes-in-a-scene/03_create_ui/line_edit_widget.png)*lineEdit*
![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-01-18-30-47.png =300x)*label+lineEdit*

この組み合わせはよく使うので、覚えておくといいと思います。
スクリプト実行時に、この中に入力されている数値を読み取るように、スクリプトを書き換えることで、キーフレームの移動数を自分で指定することが出来ます。

#### 3. スクリプトを実行するためのボタンを追加する
UIからスクリプトを実行したいので、button widgetを追加します。
button widgetは、Buttons>Push Button を使います。
![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-06-23-45-57.png)

#### 4. 任意の名前で保存する
最後に、File>Save As...からファイルを保存します。
保存する場所は、ツールのスクリプトと同じ場所が望ましいです。
理由は、スクリプトでUIファイルを呼び出す時に、その方が楽だからです。


# Layoutを調整する
widgetをきれいに整列させたり、windowを大きくしようとした時にバラバラになったりしないように、widgetをLayoutするのも、UIを作るうえでは大事な作業です。


#### 1. LabelとLine Editを整列させる
2.で作成したLabelとLine Editを選択した状態で、一番左の川みたいなアイコンをクリックしてください。
![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-07-00-05-33.png)
すると、二つのwidgetが赤い枠で囲まれた状態になると思います。
![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-06-23-55-54.png)

#### 2. 先ほどのLayoutとbuttonを整列させる
3.で作成したbuttonと先ほどのLayoutを選択した状態で、左から2番目の三のようなアイコンをクリックしてください。
![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-07-00-07-02.png)

すると、すべてのwidgetが赤い枠で囲まれた状態になります。
![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-07-00-08-06.png)

#### 3.全体をwindowにLayoutする
これだけだと、windowを縮小した際に、widgetが見えなくなってしまいます。
連動させるために、Lay Out in a Gridを使います。
mainWindowの空いているところをクリックした後、グリッド上のボタンをクリックします。
そのあとに、一番右のAdjust Sizeというボタンをクリックすると、ちょうどいいウィンドウのサイズになってくれます。
![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-07-00-13-20.png)

右側のObject Inspectorは、下図のようになっていると思います。
![](/images/edit-keyframes-in-a-scene/03_create_ui/2023-04-07-00-14-40.png)


これでUIは、完成です！
お疲れ様です！...と言いたいところですが、次はこれをMayaに読み込ませ、ボタンを押したときの処理を設定する作業が必要です。
もう少しお付き合い下さいませ。
