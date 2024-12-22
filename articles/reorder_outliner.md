---
title: "ノードの親子関係を解除するときにoutlinerの並び順を壊さない方法"
emoji: "🔰"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Maya]
published: false
---

# 概要
最近アウトライナーで親子関係を解除すると、並び順が降順に代わってしまうことが多く、
なんだかなという気持ちになる事が多いので、この機にスクリプトを作ってみようと思います。
アウトライナーだと、表示状態によって親子関係を解除することが難しいこともあるので、
そういう煩わしさも同時に解決できるかなと思います。

# はじめに
色々調べていたら、Maya Advent Callender 2022で@9boz(kubo)さんが記事を2つ書かれていたので、
それを応用させてもらいながら設計していこうと思います。

https://qiita.com/9boz/items/c78177d5d537c4ff1cca


# 設計
やりたいことを整理すると、以下の2つにわけることができます。
・親子関係を解除して並列化する
・アウトライナーで親だったノードの下に昇順に配置する

そして、これらを手順に分解すると以下のように考えることができます。
1. 親ノードの更に親のノードを取得する
2. 親ノードが階層内でどの順位にいるかを取得する
3. 親を2.で取得したノードに変更する
4. reorderを使用して、アウトライナーの順番を入れ替える

この手順に沿って、コードを作成していきます。



# 1. 親ノードの更に親のノードを取得する
親子関係を解消したいということは、2つ上の階層に親を変更するということです。
つまり、親ノードの親ノードが新しい親ということで.....日本語難しいですねｗ

そういえば、対象とするノードについて、設計の時点で明確化していませんでした。
一先ずは、【選択している子ノード(複数可)で、親は共通】とします。

コードは以下の通りです。
```py:
# 対象ノードを取得
targets = cmds.ls(sl=True)
# 現在の親ノードを取得
parent = cmds.listRelatives(target[0], parent=True)
# 新しい親ノードを取得
grand_parent = cmds.listRelatives(parent[0], parent=True)


```

# 2. 親ノードが階層内でどの順位にいるかを取得する
アウトライナーでの順番を整理するためには、基準となる順位が必要です。
今回の場合、基準となるノードは、対象の親ノードになります。
親ノードの順位を取得するために、まずは @9boz(kubo)さんの作成された [getStartIndex関数](https://qiita.com/9boz/items/f5d1da09c6f7530499ac) を
引数(target)の現在の順位を取得する部分と引数(targets:list[str])内の一番若い数字を返す部分に分割します。

```py:
def get_current_index(target:str)->int:
    """
    階層内におけるtargetの順位を取得して返す
    """
    parent = cmds.listRelatives(target, parent=True)
    
    if parent:
        # 親以下のtransformノードを取得し、targetの順位を取得する
        nodes = cmds.listRelatives(parent[0], type='transform')
        index = nodes.index(target)

    else:
        # 親がworldの場合は、最上位ノード中のtarget順位を取得する
        top_nodes = cmds.ls(assemblies=True)
        index = top_nodes.index(target)
    
    return index


def get_start_index(targets:list[str])->int:
    """
    targetsの階層内の順位を取得して、一番若い順位を返す
    """
    cur_index_list = []
    for target in targets:
        cur_index = get_current_index(target)
        cur_index_list.append(cur_index)
    
    # ソートして一番若い数のみ返す
    cur_index_list.sort()
    return cur_index_list[0]

```
今知りたいのは、親ノードの順位なので、上記の get_current_index関数を利用します。
```py:
# 親の階層内での順位を取得する
parent_cur_index = get_current_index(target=parent[0])
```

# 3.親を1.で取得したノードに変更する
必要な情報は取得できたので、次は階層を変更して並列化します。
ここは、親を変更するだけなので特別なことはしません。
ただし、2つ上の階層が存在しない場合は、worldを親とする必要があるので分岐処理をしておきます。

```py:
for target in targets:
    if grand_parent:
       cmds.parent(target, grand_parent[0])
    else:
        cmds.parent(target, world=True)
```


# 4. reorderを使用して、アウトライナーの順番を入れ替える
階層内の順位を親だったノードを基準に昇順になるように変更していきます。
階層の順位を変更するには、[reorder](https://help.autodesk.com/view/MAYAUL/2024/JPN/?guid=__CommandsPython_index_html)というコマンドを利用します。
順位の変更は、@9boz(kubo)さんの記事を参考に、以下の方法で行います。
1. 親の順位(A)を基準とする
2. 整理対象のオブジェクトを順番に 先頭に移動 -> A + n に移動を繰り返す

ループ処理になりますので、3.のスクリプトと合体させます。
そして、n を取得するために、forループをenumerate用法に変更します。
Aは、2.で取得したparent_cur_indexになります。
```py:

# targetの並び順を整える
targets.sort()

for i, target in enumerate(targets):
    # 初期値を調整
    n = i + 1

    # 階層を変更
    if grand_parent:
       cmds.parent(target, grand_parent[0])
    else:
        cmds.parent(target, world=True)

    # 階層の順番を調整
    cmds.reorder(target[i], front=True) # 先頭に移動
    cmds.reorder(target[i], relative=parent_cur_index + n) # A+nに移動

```
念のため、処理を行う前にsort()で並び順を昇順に整えました。


# 関数化
選択しているノードを基本的には対象とすると思いますが、
汎用的にしておいた方が便利かなと思うので、関数化します。
対象とするノードを引数にして、以下のように纏めました。

```py:
def dissolve_parent_child_relationshop(targets:list[str])->None:
    """
    親子関係を解消して、並列化する。子ノードは親を基準に昇順に並べる
    """
    # targetの並び順を整える
    targets.sort()
    # 現在の親ノードを取得
    parent = cmds.listRelatives(target[0], parent=True)
    # 新しい親ノードを取得
    grand_parent = cmds.listRelatives(parent[0], parent=True)

    # 親の階層内での順位を取得する
    parent_cur_index = get_current_index(target=parent[0])

    # main
    for i, target in enumerate(targets):
    # 初期値を調整
    n = i + 1

    # 階層を変更
    if grand_parent:
       cmds.parent(target, grand_parent[0])
    else:
        cmds.parent(target, world=True)

    # 階層の順番を調整
    cmds.reorder(target[i], front=True) # 先頭に移動
    cmds.reorder(target[i], relative=parent_cur_index + n) # A+nに移動
```

# 最後に
日常のちょっとした不満を解消できる記事にしたいなと思って、今回outlinerの調整を書いてみました。
outlinerの並びを調整したいシーンって私は結構あるので、
今回作った関数と、上に移動・下に移動・複数を上に移動・複数を下に移動。みたいな関数を作って
GUI化したら煩わしいこと減るかなぁ...とか思いました。
派生させたら、階層を記憶して並べ替えとかもできそうかな...??

一度仕組みを理解してしまえばそこまで複雑ではないので、この記事がとっかかりになったら嬉しいです。



-----
#### 参考にさせていただいたサイト様
- https://qiita.com/9boz/items/c78177d5d537c4ff1cca
- https://qiita.com/9boz/items/f5d1da09c6f7530499ac

-----
最終的なコード
:::details result code
```py:
def get_current_index(target:str)->int:
    """
    階層内におけるtargetの順位を取得して返す
    """
    parent = cmds.listRelatives(target, parent=True)
    
    if parent:
        # 親以下のtransformノードを取得し、targetの順位を取得する
        nodes = cmds.listRelatives(parent[0], type='transform')
        index = nodes.index(target)

    else:
        # 親がworldの場合は、最上位ノード中のtarget順位を取得する
        top_nodes = cmds.ls(assemblies=True)
        index = top_nodes.index(target)
    
    return index


def get_start_index(targets:list[str])->int:
    """
    targetsの階層内の順位を取得して、一番若い順位を返す
    """
    cur_index_list = []
    for target in targets:
        cur_index = get_current_index(target)
        cur_index_list.append(cur_index)
    
    # ソートして一番若い数のみ返す
    cur_index_list.sort()
    return cur_index_list[0]


def dissolve_parent_child_relationshop(targets:list[str])->None:
    """
    親子関係を解消して、並列化する。子ノードは親を基準に昇順に並べる
    """
    # targetの並び順を整える
    targets.sort()
    # 現在の親ノードを取得
    parent = cmds.listRelatives(target[0], parent=True)
    # 新しい親ノードを取得
    grand_parent = cmds.listRelatives(parent[0], parent=True)

    # 親の階層内での順位を取得する
    parent_cur_index = get_current_index(target=parent[0])

# main
sels = cmds.ls(sl=True, type='transform')
dissolve_parent_child_relationshop(targets=sels)

```
:::