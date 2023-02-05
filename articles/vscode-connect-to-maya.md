---
title: "Visual Studio CodeとMayaを繋げてコードをデバックする方法(MayaCode編)"
emoji: "🔰"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Maya, debug] 
published: true
---

# 概要

本記事は、mayaのツールをVisualStudioCode(以下、vscode)で書いている人向けになります。
vscodeは、ツールを跨いでdebugすることができます。これをリモートデバックというそうです。
この機能を利用して、vscodeとmayaを繋げると、debugがとっても楽になります。
個人的な感想ですが、debugを効率的に行えるようにしたら、scriptEditorにとりあえず書いておこう...みたいなことが少なくなった気がします。

もうちょっと効率よくdebug出来ないかなぁ...と思っている方は、ぜひこの記事を試してみてください。

# 結論
この記事では、[MayaCode]を使ってリモートデバックを行う方法を説明いたします。
[MayaCode]は、設定が簡単で、mayaのscript editorのようなdebugが可能です。

他にも、[debugpy]を利用する方法があります。
break pointが設定できるので、error時の原因追及に便利だと思います。

私は時と場合に応じて両方使い分けています。
皆様も自分に合った方法を探してみてください。


# MayaCode
vscodeには、拡張機能があり、これを、Extensionと呼びます。
この方法では、[MayaCode]というExtensionを利用します。
詳しくは、MayaCodeに概要が書いてあるので、確認してもらえればと思いますが、
mel commandのauto complete機能などがあり、debug機能も、そのうちの一つです。

では、設定をしていきます。まずは、vscodeからです。


# 1. MayaCodeをインストール
1. サイドバーにあるパズルのようなアイコンをクリックし、上のテキストボックスにmayaCodeと打ち込んでください。
![](/images/vscode-connect-to-maya/extension_mayacode.png)

2. installのボタンをクリックし、installが完了したら、一度vscodeを閉じます。
:::message
extensionをinstallした場合は、一度vscodeを閉じないと、反映されません
:::
3. 再度、vscodeを起動したら、スクリプトを開き、右クリックを押してみてください。
pythonなら [**Send Python Code to Maya**]、melなら [**Send MEL Code to Maya**]というコマンドが、context menuの一番上に表示されていればOKです。✅ 
![](/images/vscode-connect-to-maya/send_mel_code_to_maya.png)


# 2. Mayaをvscodeに繋げる
vscodeの設定は終わったので、次はmayaの設定を行います。
設定の確認のために、print("Helloworld")と書かれたpython fileを用意し、
vscodeからmayaに送信されるかをテストします。

1. print("Helloworld")が書かれたスクリプトを用意します
mel/pythonどちらでも構いません。
2. 下記のコードをscript EditorのMELタブにコピーして、実行してください
```
commandPort -name "localhost:7001" -sourceType "mel" -echoOutput;
```
:::message
sourceTypeがmelになっていますが、使っている言語によって変更する必要はありません。
むしろ変更すると動きませんので、注意してください。
:::
3. vscodeに戻って、右クリック>Send Python Code to Mayaを実行します
4. script Editorに**Helloworld**と表示されたら、成功です！

この方法は、commandPortのコードをMayaの起動ごとに行う必要があります。
📝 毎回打つのは面倒なので、shelfに登録しておくと便利です。

# 最後に
結論でも少し書きましたが、[MayaCode]を利用する上でのメリットとデメリットを、私なりに纏めてみました。

#### メリット
- 設定が簡単
- 使い方は、scriptEditorと同じ
- python/mel両方デバックできる

初心者にもとっつきやすいですし、pythonもmelもデバックできるのが、良いところですね。

#### デメリット
- errorが出たときにbreakpointを使えないので、長文デバックには不向き
- __file__等のコードが入っていると、mayaでerrorになってしまう

スクリプト自体を実行しているので、コードに__file__等が入っていると、エラーになってしまいます。

結論でも触れましたが、[別の記事](https://zenn.dev/gacha0923/articles/vscode-connect-to-maya_debugpy)で、debugpyについても記載しています。
Extensionは、uninstallもできるので、もし興味がありましたら確認してみてください。



-----
#### 参考にさせていただいたサイト様
- [Visual Studio Codeの拡張機能「MayaCode」でスクリプトを直接Mayaに送信・実行する方法](https://liquidjumper.com/programming/python/visual-studio-code_mayacode_maya)