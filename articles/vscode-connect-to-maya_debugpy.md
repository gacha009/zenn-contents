---
title: "Visual Studio CodeをMayaと繋げてデバックする方法(debugpy編)"
emoji: "🔰"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Maya, debug] 
published: false
---

# 概要
で、[MayaCode]を使用したリモートデバッグの方法を説明いたしました。
本記事では、[debugpy]を利用する方法を説明いたします。

# 結論
debugpyを利用して、リモートデバックをするには、以下の手順が必要です。
1. vscodeのsetting.jsonを設定する
2. debugpyをinstallする
3. mayaのuserSetup.pyを用意する

では、それぞれ詳しく見ていきましょう。


# 1. launch.jsonを作成する
まずは、vscodeの設定です。

1. サイドバーの再生マークに虫がついたアイコン[実行とデバック]をクリックします
![](/images/vscode-connect-to-maya/excute_and_debug.png)
2. **create a launch.json**をクリックします
3. select debuggerというテキストエディタが立ち上がるので、**Remote Attach**を選択
4. localhostと出てくるので、**127.0.0.1**と入力して、Enterを押す
5. テキストエディタに[5678]と打たれているので、何もせずEnterを押す
6. 下記のように書いてある、launch.jsonが出来上がると思います。

```js:launch.json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Remote Attach",
            "type": "python",
            "request": "attach",
            "connect": {
                "host": "127.0.0.1",
                "port": 5678
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "."
                }
            ],
            "justMyCode": true
        }
    ]
}
```
これを、下記のように変更します。
```js:launch.json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Maya: Attach",
            "type": "python",
            "request": "attach",
            "connect": {
                "host": "127.0.0.1",
                "port": 5678
            }
        }
    ]
}
```

これで、vscodeの設定は終わりです。

# 2. debugpyをインストールする
mayaをリモートデバックするためには、debugpyを利用します。

1. コマンドプロンプトを起動します
2. 下記コードを入力し、Enterを押します
```:コマンドプロンプト
pip install debugpy -t %USERPROFILE%Documents\maya\(version)\scripts
```
インストールが完了すると、コマンドプロンプトに下記のように表示されます。
![](/images/vscode-connect-to-maya/success_debugpy.png)

✅ scriptsフォルダ以下に、debugpyフォルダが作成されていたら完了です。

:::message
install 時にCドライブ直下に移動しなくても、結果は変わりません。
:::

# 3. userSetup.pyを作成する
次は、mayaの設定をしていきます。
userSetup.pyにコードを記述することで、vscodeと繋がるように設定します。

1. mayaのファイルパスが通っているフォルダ以下(C:/Users/(username)/document/maya/(version)/scripts)に、userSetup.pyファイルを作成します
2. ファイルに、下記コードを記入して上書きします
```py:userSetup.py
import debugpy
debugpy.configure(
    python='C:/Program Files/Autodesk/(MayaVesion)/bin/mayapy.exe')
debugpy.listen(5678)
```
3. Mayaを再起動します

# 4. 確認
最後に、vscodeとmayaが繋がっているか、確認します。

1. vscodeを開いて、.vscodeがある階層のスクリプトを開きます
2. F5を押し、debug modeにします
3. MayaのscriptEditorを開きます
4. scriptEditorに、2.のスクリプトをimportするコードを記載します
5. prePlay buttonを押します

✅ vscodeの左側にあるxxxに、コードが流れたらOKです

# おまけ
userSetupで、ポートを記載していても、commandPortのコードを実行すると上書きされるようです。
元に戻すには、再起動をしないといけないのですが、
簡単なスクリプトをデバックしたいだけなら、[MayaCode]を利用するなど、場面に併せて私は使っています。

※ポートの上書きをしていいのかはわからないので、不安な方は止めておいた方がいいと思います。

-----
#### 参考にさせていただいたサイト様
- [VSCodeからMaya上をリモートでバッグする(debugpy版)](https://qiita.com/Tack2/items/bf866950dc1f4d6e1c14)
- [Mayaでリモートデバックする方法](https://unpyside.com/blog/2022/12/09/mayapythonadventcalendar2022day9/)

[^1]: ptvsdモジュールを使う方法もありますが、どうやら今後は非推奨のようです。
[microsoft/ptvsd](https://github.com/microsoft/ptvsd)
[Debugging in Maya with debugpy and VSCode](https://www.aleksandarkocic.com/2020/12/25/debugging-in-maya-with-debugpy-and-vscode/)
