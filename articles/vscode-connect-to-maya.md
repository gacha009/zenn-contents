---
title: "Visual Studio CodeをMayaと繋げてデバックする方法"
emoji: "🔰"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Debug] [Maya]
published: false
---

# 概要

本記事は、mayaのツールをvscodeで書いている人向けになります。
実は、vscodeは、mayaと繋げると、debugがとっても楽になります。
個人的な感想ですが、debugを効率的に行えるようにしたら、scriptEditorにとりあえず書いておこう...みたいなことが少なくなった気がします。

なんか、もうちょっと効率よくMayaでdebug出来ないかな...と思っている方は、ぜひこの記事を試してみてください。

# 結論

mayaとの連携方法は、2通りあります。
1.  [Maya Code]という、vscodeに用意されたExtensionを使う方法
2.  setting.jsonを設定する方法

1.は、設定が簡単で、mayaのscript editorのようなDebugが可能です。
2.は、break pointが設定できるので、error時の原因追及に便利です。

それぞれ詳しく見ていきましょう。
