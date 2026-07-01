---
title: "14-①[AI][Kaggle][python]Kaggle入門(ゲームAIと強化学習入門 1.ゲーム開始)"
emoji: "✨"
type: "idea"
topics:
  - "ai"
  - "python"
  - "kaggle"
  - "ゲームaiと強化学習入門"
published: true
published_at: "2026-04-06 19:47"
---

[Kaggle入門14(ゲームAIと強化学習入門 1.ゲーム開始)](https://zenn.dev/rg687076/articles/49e1d162bfdeec)
[Kaggle入門14(ゲームAIと強化学習入門 2.1歩先読み)](https://zenn.dev/rg687076/articles/767ddfb1a82be9)
[Kaggle入門14(ゲームAIと強化学習入門 3.N歩先読み)](https://zenn.dev/rg687076/articles/e5a1c9762f270d)
[Kaggle入門14(ゲームAIと強化学習入門 4.最終回 深層強化学習)](https://zenn.dev/rg687076/articles/0978c30a568247)

← [Kaggle入門13(機械学習の説明可能性)](https://zenn.dev/rg687076/articles/89fe247589e22a)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[Kaggle実践1『Titanic生存者予測』](https://zenn.dev/rg687076/articles/zenn_260627_0000_00_create_local_titanic_env) →

# ゲームAIと強化学習入門
古典的なアルゴリズムと最先端のアルゴリズムを駆使して、独自のビデオゲームボットを構築しましょう。

## Abstract
- Kaggle「ゲームAIと強化学習入門 の[ゲーム開始](https://www.kaggle.com/code/alexisbcook/play-the-game)」の翻訳と実行方法の解説

## ゲーム開始
最初のゲームプレイエージェントを作成してください。

## 理論編
### はじめに(Introduction)
[Connect Four](https://en.wikipedia.org/wiki/Connect_Four)は、2人のプレイヤーが交互に縦のグリッドに色付きのディスクを落としていくゲームです。各プレイヤーは異なる色（通常は赤か黄色）を使い、ゲームの目的は4つのディスクを一直線に並べることです。

![](https://storage.googleapis.com/zenn-user-upload/f3e7b204c941-20260321.gif)

このコースでは、ゲームをプレイするための独自の知的エージェントを作成します。

- 最初のレッスンでは、ゲーム環境のセットアップ方法と最初のエージェントの作り方を学びます。
- 次の2つのレッスンでは、ゲームAIを構築するための従来の手法に焦点を当てます。これらのエージェントは、多くの初心者プレイヤーに勝てるほど賢くなります！
- 最後のレッスンでは、強化学習分野の最先端アルゴリズムを試します。作成するエージェントは、人間のように経験を積みながら徐々にプレイ戦略を身につけていきます。

### コンペへの参加(Join the competition)
コースを通して、他のユーザーが作成したエージェントと対戦し、自分のエージェントの性能をテストします。

コンペに参加するには、[コンペページ](https://www.kaggle.com/c/connectx/overview)を新しいウィンドウで開き、「Join Competition」ボタンをクリックします。（「Join Competition」ではなく「Submit Agent」ボタンが表示されている場合は、すでに参加済みなので再度参加する必要はありません。）
![](https://storage.googleapis.com/zenn-user-upload/504ee46f0e4e-20260321.png)

すると、ルール同意ページに移動します。参加するにはコンペのルールに同意する必要があります。これらのルールでは、1日に提出できる回数や最大チーム人数など、コンペ特有の詳細が定められています。そして、「I Understand and Accept」をクリックして、ルールに従うことを示します。

### はじめに(Getting started)
ゲーム環境には、あらかじめ実装されたエージェントが用意されています。これらのデフォルトエージェントの一覧を見るには、次を実行します。
```python
from kaggle_environments import make, evaluate

# Create the game environment
# Set debug=True to see the errors if your agent refuses to run
env = make("connectx", debug=True)

# List of available default agents
print(list(env.agents))
```
**結果:**
Loading environment lux_ai_s2 failed: No module named 'vec_noise'
['random', 'negamax']

「ランダム」エージェントは、有効な移動の集合から（一様分布に従って）ランダムに選択します。→ [Connect Four](https://en.wikipedia.org/wiki/Connect_Four)では、列にまだディスクを置くスペースがある場合、その手は有効とみなされます（つまり、盤面が7行なら、その列のディスクが7未満である必要があります）。

以下のコードセルでは、このエージェント同士で1試合プレイします。
```python
# Two random agents play one game round
env.run(["random", "random"])

# Show the game
env.render(mode="ipython")
```
![](https://storage.googleapis.com/zenn-user-upload/66092dc1b8b6-20260321.gif =300x)

上のプレイヤーを使うと、ゲームの詳細を確認できます。すべての手が記録され、再生可能です。さっそく試してみましょう！

すぐに分かりますが、この情報はエージェントを改善するアイデアを考える上で非常に役立ちます。

### エージェントの定義(Defining agents)
コンペに参加するには、自分でエージェントを作成します。

エージェントは、obsとconfigの2つの引数を受け取るPython関数として実装します。戻り値は選択した列を表す整数で、インデックスは0から始まります。つまり、0〜6のいずれかの値を返します。

理解を助けるために、いくつかの例から始めます。
- 1つ目のエージェントは、先ほどの「random」と同じ動きをします。
- 2つ目のエージェントは、有効かどうかに関係なく常に中央の列を選びます。無効な手を選ぶと、その時点で負けになる点に注意してください。
- 3つ目のエージェントは、最も左の有効な列を選びます。

```python
import random
import numpy as np

# Selects random valid column
def agent_random(obs, config):
    valid_moves = [col for col in range(config.columns) if obs.board[col] == 0]
    return random.choice(valid_moves)

# Selects middle column
def agent_middle(obs, config):
    return config.columns//2

# Selects leftmost valid column
def agent_leftmost(obs, config):
    valid_moves = [col for col in range(config.columns) if obs.board[col] == 0]
    return valid_moves[0]
```

では、obsとconfigとは具体的に何でしょうか？

#### obs

obsには2つの情報が含まれています。
- obs.board：ゲーム盤（各マスに対応する要素を持つPythonリスト）
- obs.mark：エージェントに割り当てられた駒（1または2）

obs.boardはディスクの位置を示すPythonリストで、1行目、2行目…の順に並びます。プレイヤー1のディスクは1、プレイヤー2のディスクは2で表されます。例えば、このゲーム盤の場合：
![](https://storage.googleapis.com/zenn-user-upload/0a9d88e5c102-20260321.png)
obs.board は [0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0, 0, 0, 2, 2, 0, 0, 0, 0, 2, 1, 2, 0, 0, 0, 0, 1, 1, 1, 0, 0, 0, 0, 2, 1, 2, 0, 2, 0] になります。

#### config
configには3つの情報が含まれています。

- config.columns：列数（[Connect Four](https://en.wikipedia.org/wiki/Connect_Four)では7）
- config.rows：行数（[Connect Four](https://en.wikipedia.org/wiki/Connect_Four)では6）
- config.inarow：勝利に必要な連続した駒の数（[Connect Four](https://en.wikipedia.org/wiki/Connect_Four)では4）

ここで、先ほど定義した3つのエージェントを確認してみましょう。コードの内容をしっかり理解しておいてください！

### エージェントの評価(Evaluating agents)
カスタムエージェント同士を1試合プレイさせるには、これまでと同様にenv.run()を使います。
```python
# Agents play one game round
env.run([agent_leftmost, agent_random])

# Show the game
env.render(mode="ipython")
```
![](https://storage.googleapis.com/zenn-user-upload/bf1f210bc99e-20260321.gif =300x)

1試合の結果だけでは、エージェントの性能を判断するには不十分です。より正確に評価するために、複数試合の平均勝率を計算します。公平性のため、各エージェントが先手を取る回数は半分ずつになります。

これを行うには、get_win_percentages() 関数（非表示のコードセルで定義されています）を使用します。この関数の詳細を表示するには、下の「コード」ボタンをクリックしてください。
```python
def get_win_percentages(agent1, agent2, n_rounds=100):
    # Use default Connect Four setup
    config = {'rows': 6, 'columns': 7, 'inarow': 4}
    # Agent 1 goes first (roughly) half the time          
    outcomes = evaluate("connectx", [agent1, agent2], config, [], n_rounds//2)
    # Agent 2 goes first (roughly) half the time      
    outcomes += [[b,a] for [a,b] in evaluate("connectx", [agent2, agent1], config, [], n_rounds-n_rounds//2)]
    print("Agent 1 Win Percentage:", np.round(outcomes.count([1,-1])/len(outcomes), 2))
    print("Agent 2 Win Percentage:", np.round(outcomes.count([-1,1])/len(outcomes), 2))
    print("Number of Invalid Plays by Agent 1:", outcomes.count([None, 0]))
    print("Number of Invalid Plays by Agent 2:", outcomes.count([0, None]))
```
ランダムエージェントに対して、どちらのエージェントの方が優れたパフォーマンスを発揮すると思いますか？常に中央でプレイするエージェント（agent_middle）でしょうか、それとも最も左の有効な列を選択するエージェント（agent_leftmost）でしょうか？さあ、調べてみましょう！

```python
get_win_percentages(agent1=agent_middle, agent2=agent_random)
```
Agent 1 Win Percentage: 0.65
Agent 2 Win Percentage: 0.0
Number of Invalid Plays by Agent 1: 35
Number of Invalid Plays by Agent 2: 0

```python
get_win_percentages(agent1=agent_leftmost, agent2=agent_random)
```
Agent 1 Win Percentage: 0.82
Agent 2 Win Percentage: 0.18
Number of Invalid Plays by Agent 1: 0
Number of Invalid Plays by Agent 2: 0

最も左の有効な列を選ぶエージェントが最も良い成績を出しているようです！

## 実践編
### 導入(Introduction)
あなたはランダムなエージェントの定義方法をすでに見てきました。この演習では、いくつか改良を加えていきます。

まずは、以下のコードセルを実行してフィードバックシステムをセットアップしてください。
```python
from learntools.core import binder
binder.bind(globals())
from learntools.game_ai.ex1 import *
```
 
### 1) より賢いエージェント(A smarter agent)
複雑な戦略を考えなくても、勝てる手がある場合にそれを選ぶだけで性能を向上させることができます。

この演習では、次のようなエージェントを作成します：
* 勝てる手がある場合は、その手を選ぶ（複数ある場合はどれでもよい）
* それ以外の場合は、ランダムな手を選ぶ

この目的を助けるために、以下のコードセルでいくつかの補助関数が提供されています。
```python
import numpy as np

# Gets board at next step if agent drops piece in selected column
def drop_piece(grid, col, piece, config):
    next_grid = grid.copy()
    for row in range(config.rows-1, -1, -1):
        if next_grid[row][col] == 0:
            break
    next_grid[row][col] = piece
    return next_grid

# Returns True if dropping piece in column results in game win
def check_winning_move(obs, config, col, piece):
    # Convert the board to a 2D grid
    grid = np.asarray(obs.board).reshape(config.rows, config.columns)
    next_grid = drop_piece(grid, col, piece, config)
    # horizontal
    for row in range(config.rows):
        for col in range(config.columns-(config.inarow-1)):
            window = list(next_grid[row,col:col+config.inarow])
            if window.count(piece) == config.inarow:
                return True
    # vertical
    for row in range(config.rows-(config.inarow-1)):
        for col in range(config.columns):
            window = list(next_grid[row:row+config.inarow,col])
            if window.count(piece) == config.inarow:
                return True
    # positive diagonal
    for row in range(config.rows-(config.inarow-1)):
        for col in range(config.columns-(config.inarow-1)):
            window = list(next_grid[range(row, row+config.inarow), range(col, col+config.inarow)])
            if window.count(piece) == config.inarow:
                return True
    # negative diagonal
    for row in range(config.inarow-1, config.rows):
        for col in range(config.columns-(config.inarow-1)):
            window = list(next_grid[range(row, row-config.inarow, -1), range(col, col+config.inarow)])
            if window.count(piece) == config.inarow:
                return True
    return False
```


`check_winning_move() 関数`は、4つの引数を取ります：最初の2つ（obs と config）はこれまでと同じです。
* `col` は有効な列(移動先)
* `piece` はエージェントの印、または対戦相手の印

この関数は、指定した列に駒を落としたことで勝利する場合(エージェントまたは対戦相手のどちらかが勝利する場合)は True を返し、それ以外は False を返します。エージェントが次の一手で勝てるかどうかを確認するには、`piece=obs.mark` を設定してください。

この問題を解くには、`agent_q1()` を定義する必要があります。その際、`check_winning_move()` を使うことが推奨されています。

```python
import random

def agent_q1(obs, config):
    valid_moves = [col for col in range(config.columns) if obs.board[col] == 0]
    # Your code here: Amend the agent!
    for col in valid_moves:
        if check_winning_move(obs, config, col, obs.mark):
            return col
    return random.choice(valid_moves)
    
# Check your answer
q_1.check()
```
![](https://storage.googleapis.com/zenn-user-upload/ff0ad4df9a7c-20260322.png)

```python
# Lines below will give you a hint or solution code
q_1.hint()
q_1.solution()
```
ヒント
![](https://storage.googleapis.com/zenn-user-upload/ad1a62e3b37b-20260322.png)
check_winning_move() 関数を使用し、piece=obs.mark を設定してください。関数に col 引数として列を指定することで、エージェントが特定の列に駒を置いてゲームに勝利できるかどうかを確認できます。

答え
![](https://storage.googleapis.com/zenn-user-upload/a688ae118705-20260322.png)

### 2) さらに賢いエージェント(An even smarter agent)
前の問題では、勝てる手を選ぶエージェントを作りました。この問題ではさらに改良し、対戦相手の勝利も防げるようにします。

エージェントは以下のように行動します：

* 勝てる手があれば、それを選ぶ
* そうでなければ、相手が次のターンで勝てる手がある場合、それを防ぐ手を選ぶ
* どちらもない場合は、ランダムに手を選ぶ

相手が勝てる手を持っているか確認するには、`check_winning_move()` を使い、`piece` に対戦相手の印を渡します。
```python
def agent_q2(obs, config):
    # Your code here: Amend the agent!
    valid_moves = [col for col in range(config.columns) if obs.board[col] == 0]
    for col in valid_moves:
        if check_winning_move(obs, config, col, obs.mark):
            return col
    for col in valid_moves:
        if check_winning_move(obs, config, col, obs.mark%2+1):
            return col
    return random.choice(valid_moves)

# Check your answer
q_2.check()
```
![](https://storage.googleapis.com/zenn-user-upload/1b0f4a1230d6-20260322.png)
```python
# Lines below will give you a hint or solution code
q_2.hint()
q_2.solution()
```
ヒント
![](https://storage.googleapis.com/zenn-user-upload/068ab012518f-20260322.png)
先ほど作成したエージェントのコードから始めます。相手が次の手で勝てるかどうかを確認するには、同じ check_winning_move() 関数を使用し、piece=obs.mark%2+1 を設定します。

答え
![](https://storage.googleapis.com/zenn-user-upload/4dec3614c307-20260322.png)

### 3) 先を読む(Looking ahead)
ここまでで、勝てる手があれば必ず選ぶエージェントを作りました。そして、相手の勝利も防ぐ手を選ぶ機能も作りました。

一見するとかなり強そうですが、それでも負ける可能性があります。なぜでしょうか？
```python
q_3.hint()
```
ヒント
![](https://storage.googleapis.com/zenn-user-upload/0b3a1513315d-20260322.png)

答え
![](https://storage.googleapis.com/zenn-user-upload/a7dba9dd32e1-20260322.png)
エージェントはまだゲームに負ける可能性があります。それは、
もし、相手プレイヤーが次の手で2つ以上の列のいずれかにディスクを落とすことで勝利できるように盤面を操作している場合や、エージェントが選択できる手が、一度プレイすると相手プレイヤーが次の手で勝利できる手しかない場合です。

### 4) 自分のエージェントを作る(Create your own agent)
`my_agent()` 関数を編集して、自分独自のエージェントを作成してください。これまでに作ったエージェントをコピーして使っても構いません。

注)必要なインポートや補助関数もすべて含める必要があります。演習で最初に作成したエージェントでどのように記述するかの例については、この[ノートブック](https://www.kaggle.com/code/alexisbcook/create-a-connectx-agent)を参照してください。
![](https://storage.googleapis.com/zenn-user-upload/f774d60736bc-20260322.png)

![](https://storage.googleapis.com/zenn-user-upload/3af44e4243e6-20260322.png)

次のコードセルを実行して、エージェントがランダムなエージェントと対戦する様子をご覧ください。コードセルを再度実行すれば、もう一度プレイできます！
```python
from kaggle_environments import evaluate, make

env = make("connectx", debug=True)
env.run([my_agent, "random"])
env.render(mode="ipython")
```
出力:
[kaggle_environments.envs.open_spiel.open_spiel] INFO: Successfully loaded OpenSpiel environments: 6.
[kaggle_environments.envs.open_spiel.open_spiel] INFO:    open_spiel_chess
[kaggle_environments.envs.open_spiel.open_spiel] INFO:    open_spiel_connect_four
[kaggle_environments.envs.open_spiel.open_spiel] INFO:    open_spiel_gin_rummy
[kaggle_environments.envs.open_spiel.open_spiel] INFO:    open_spiel_go
[kaggle_environments.envs.open_spiel.open_spiel] INFO:    open_spiel_tic_tac_toe
[kaggle_environments.envs.open_spiel.open_spiel] INFO:    open_spiel_universal_poker
[kaggle_environments.envs.open_spiel.open_spiel] INFO: OpenSpiel games skipped: 0.
![](https://storage.googleapis.com/zenn-user-upload/50275dbb1bbb-20260322.gif =300x)


### 5) コンペに提出する(Submit to the competition)
いよいよコンペへの提出です。次のコードセルを実行して、エージェントを送信ファイルに書き込みます。
```python
import inspect
import os

def write_agent_to_file(function, file):
    with open(file, "a" if os.path.exists(file) else "w") as f:
        f.write(inspect.getsource(function))
        print(function, "written to", file)

write_agent_to_file(my_agent, "submission.py")

# Check that submission file was created
q_5.check()
```
![](https://storage.googleapis.com/zenn-user-upload/ded54bd42e3f-20260322.png)

以下の手順に従ってください：

1. 右上の「Save Version」ボタンをクリック → POPUPが表示されます。
2. 「Save and Run All」を選択 → 保存
3. 左下にウィンドウが生成 → 実行完了後「Save Version」ボタンの右の数字をクリック → バージョン一覧を開く → 最新バージョンの「…」から「Open in Viewer」を選択 → これにより、同じページの表示モードに切り替わる。手順に戻るには、下にスクロールする必要があります。
4. 上部の「Data」タブを開く。提出ファイルを選び、「Submit」をクリック

これで提出完了です！

パフォーマンスを改善したい場合は、「Edit」ボタンからコードを修正し、同じ手順を繰り返してください。

スコアや対戦結果は「My Submissions」で確認できます。

次の章[2. ["1歩先読み"](https://zenn.dev/rg687076/articles/767ddfb1a82be9)]へ