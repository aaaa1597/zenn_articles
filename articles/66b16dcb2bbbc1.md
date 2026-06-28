---
title: "7-⑥[AI][Kaggle][python]Kaggle入門(SQL入門 6.最終回 データの結合)"
emoji: "🙄"
type: "idea"
topics:
  - "ai"
  - "python"
  - "kaggle"
  - "sql入門"
published: true
published_at: "2026-02-16 19:29"
---

[Kaggle入門7(SQL入門 1.はじめての SQL と BigQuery)](https://zenn.dev/rg687076/articles/d7cf749c7ba539)
[Kaggle入門7(SQL入門 2.Select, From & Where)](https://zenn.dev/rg687076/articles/99be07789eaed5)
[Kaggle入門7(SQL入門 3.Group By, Having & Count)](https://zenn.dev/rg687076/articles/b84a13c92064b3)
[Kaggle入門7(SQL入門 4.Order By)](https://zenn.dev/rg687076/articles/02aaa0eb377da9)
[Kaggle入門7(SQL入門 5.As & With)](https://zenn.dev/rg687076/articles/f48d56953670b0)
[Kaggle入門7(SQL入門 6.最終回 データの結合)](https://zenn.dev/rg687076/articles/66b16dcb2bbbc1)

← [Kaggle入門6(特徴エンジニアリング)](https://zenn.dev/rg687076/articles/96f85e2e4dadc6)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;[Kaggle入門8(高度なSQL)](https://zenn.dev/rg687076/articles/9034c3f8cadc88) →

## Abstract
Kaggle「SQL入門の[データの結合](https://www.kaggle.com/code/dansbecker/joining-data)」の翻訳と実行方法の解説

## データの結合
データソースを組み合わせる。ほぼすべての実世界のデータ問題にとって重要です。

## 理論編
### はじめに(Introduction)
あなたは、1つのテーブルから好きな形式でデータを取得するためのツールを身につけました。ですが、欲しいデータが複数のテーブルに分かれている場合はどうすればよいでしょうか？

そこで登場するのが **JOIN** です！**JOIN**は、実践的なSQLのワークフローにおいて非常に重要な機能です。では、始めましょう。

### Example
3つの列を持つ架空の **pets** テーブルを使います。

* ID - ペットのID番号
* Name - ペットの名前
* Animal - 動物の種類

さらに、**owners** という別のテーブルも追加します。このテーブルにも3つの列があります。

* ID - 飼い主のID番号（ペットのIDとは別）
* Name - 飼い主の名前
* Pet_ID - その飼い主が所有するペットのID番号（petsテーブルのIDと一致）

![](https://storage.googleapis.com/zenn-user-upload/fde74744a916-20260214.png)

特定のペットに関する情報を取得するには、
**petsテーブルのID列** と **ownersテーブルのPet_ID列** を照合します。

![](https://storage.googleapis.com/zenn-user-upload/cdb2ba98de01-20260214.png)

たとえば、

* petsテーブルでは、Dr. Harris Bonkers が ID 1 のペットであることが分かります。
* ownersテーブルでは、ID 1 のペットの飼い主が Aubrey Little であることが分かります。

この2つの事実を組み合わせると、
Dr. Harris Bonkers の飼い主は Aubrey Little であることが分かります。

幸いにも、どの飼い主がどのペットに対応するかを手作業で調べる必要はありません。次のセクションでは、**JOIN**を使って pets テーブルと owners テーブルの情報を結合した新しいテーブルを作成する方法を学びます。

### JOIN
**JOIN**を使うと、ペットの名前と飼い主の名前の2列だけを持つテーブルを作成できます。
![](https://storage.googleapis.com/zenn-user-upload/a52e5660032d-20260214.png)

両方のテーブルの情報は、
**petsテーブルのID列** と **ownersテーブルのPet_ID列** が一致する行同士を対応させることで結合します。

クエリでは、**ON** 句によって、各テーブルのどの列を使って結合するかを指定します。
ID列は両方のテーブルに存在するため、どちらを使うのかを明確にする必要があります。

* `p.ID` は pets テーブルの ID 列
* `o.Pet_ID` は owners テーブルの Pet_ID 列

を指します。

> 一般的に、テーブルを結合する際には、各列がどのテーブルに属しているのかを明示するのが良い習慣です。そうすれば、後からクエリを読み返すたびにスキーマを確認する必要がなくなります。

今回使用しているJOINの種類は **INNER JOIN** と呼ばれます。
これは、結合に使用する列の値が両方のテーブルに存在する場合にのみ、その行が最終的な出力テーブルに含まれる、という意味です。

例えば、Tom の ID番号 4 が pets テーブルに存在しなかった場合、このクエリの結果は 3行だけになります。

JOINには他にも種類がありますが、**INNER JOIN**は非常によく使われるので、最初に学ぶには最適です。

---

### Example: 各ソフトウェアライセンスごとに、何個のファイルが含まれているか？

GitHub は、ソフトウェアプロジェクトを共同開発するための最も人気のある場所です。
GitHubの**リポジトリ**（**repo**）は、特定のプロジェクトに関連するファイルの集合です。

GitHub上の多くのリポジトリは、特定の法的ライセンスのもとで公開されています。このライセンスは、ソフトウェアの利用方法に関する法的制限を定めています。

この例では、各ライセンスのもとで公開されているファイル数を調べます。

データベース内の2つのテーブルを使用します。

1つ目は **licenses** テーブルで、
各GitHubリポジトリ名（repo_name列）と、それに対応するライセンスが含まれています。
最初の5行を以下に示します。
```python
from google.cloud import bigquery

# Create a "Client" object
client = bigquery.Client()

# Construct a reference to the "github_repos" dataset
dataset_ref = client.dataset("github_repos", project="bigquery-public-data")

# API request - fetch the dataset
dataset = client.get_dataset(dataset_ref)

# Construct a reference to the "licenses" table
licenses_ref = dataset_ref.table("licenses")

# API request - fetch the table
licenses_table = client.get_table(licenses_ref)

# Preview the first five lines of the "licenses" table
client.list_rows(licenses_table, max_results=5).to_dataframe()
```
| id | repo_name | license |
|:---|:---|:---|
| 0 | autarch/Dist-Zilla-Plugin-Test-TidyAll | artistic-2.0 |
| 1 | thundergnat/Prime-Factor | artistic-2.0 |
| 2 | kusha-b-k/Turabian_Engin_Fan | artistic-2.0 |
| 3 | onlinepremiumoutlet/onlinepremiumoutlet.github.io | artistic-2.0 |
| 4 | huangyuanlove/LiaoBa_Service | artistic-2.0 |

2つ目は **sample_files** テーブルで、
各ファイルがどのGitHubリポジトリに属しているか（repo_name列）などの情報が含まれています。
この表の最初の数行を以下に示します。
```python
# Construct a reference to the "sample_files" table
files_ref = dataset_ref.table("sample_files")

# API request - fetch the table
files_table = client.get_table(files_ref)

# Preview the first five lines of the "sample_files" table
client.list_rows(files_table, max_results=5).to_dataframe()
```
| id | repo_name | ref | path | mode | id_hash | symlink_target |
|:---|:---|:---|:---|:---|:---|:---|
| 0 | EOL/eol | refs/heads/master | generate/vendor/railties | 40960 | 0338c33fb3fda57db9e812ac7de969317cad4959 | /usr/share/rails-ruby1.8/railties |
| 1 | np/ling | refs/heads/master | tests/success/merger_seq_inferred.t/merger_seq... | 40960 | dd4bb3d5ecabe5044d3fa5a36e0a9bf7ca878209 | ../../../fixtures/all/merger_seq_inferred.ll |
| 2 | np/ling | refs/heads/master | fixtures/sequence/lettype.ll | 40960 | 8fdf536def2633116d65b92b3b9257bcf06e3e45 | ../all/lettype.ll |
| 3 | np/ling | refs/heads/master | fixtures/failure/wrong_order_seq3.ll | 40960 | c2509ae1196c4bb79d7e60a3d679488ca4a753e9 | ../all/wrong_order_seq3.ll |
| 4 | np/ling | refs/heads/master | issues/sequence/keep.t | 40960 | 5721de3488fb32745dfc11ec482e5dd0331fecaf | ../keep.t |

---
次に、両方のテーブルの情報を使って、各ライセンスごとのファイル数を求めるクエリを書きます。

```python
# Query to determine the number of files per license, sorted by number of files
query = """
        SELECT L.license, COUNT(1) AS number_of_files
        FROM `bigquery-public-data.github_repos.sample_files` AS sf
        INNER JOIN `bigquery-public-data.github_repos.licenses` AS L 
            ON sf.repo_name = L.repo_name
        GROUP BY L.license
        ORDER BY number_of_files DESC
        """

# Set up the query (cancel the query if it would use too much of 
# your quota, with the limit set to 10 GB)
safe_config = bigquery.QueryJobConfig(maximum_bytes_billed=10**10)
query_job = client.query(query, job_config=safe_config)

# API request - run the query, and convert the results to a pandas DataFrame
file_count_by_license = query_job.to_dataframe()
```

/opt/conda/lib/python3.7/site-packages/google/cloud/bigquery/client.py:440: UserWarning: Cannot create BigQuery Storage client, the dependency google-cloud-bigquery-storage is not installed.
  "Cannot create BigQuery Storage client, the dependency "
  「BigQuery ストレージ クライアントを作成できません。依存関係があります」

It's a big query, and so we'll investigate each piece separately.
これは大きなクエリなので、一つ一つを個別に調査することにします。
![](https://storage.googleapis.com/zenn-user-upload/3e5b2fd5800d-20260214.png)


まずは**JOIN**部分です。
ここでは、データの取得元と結合方法を指定しています。
**ON**句で、両テーブルの repo_name 列の値が一致する行同士を結合することを指定しています。

次に、**SELECT** と **GROUP BY** です。
**GROUP BY** によって、ライセンスごとにデータをグループ化します。その後、各ライセンスに対応する sample_files テーブルの行数を COUNT(1) で数えます。（COUNT(1) で行数を数えられることを思い出してください。）

最後に、**ORDER BY **によって、ファイル数の多いライセンスが上に来るように結果を並び替えています。

大きなクエリでしたが、その結果、各ライセンスのもとでコミットされたファイル数をまとめた分かりやすいテーブルが得られました。

```
# Print the DataFrame
file_count_by_license
```
| id | license | number_of_files |
|:---|:---|:---|
| 0 | mit | 20,560,894 |
| 1 | gpl-2.0 | 16,608,922 |
| 2 | apache-2.0 | 7,201,141 |
| 3 | gpl-3.0 | 5,107,676 |
| 4 | bsd-3-clause | 3,465,437 |
| 5 | agpl-3.0 | 1,372,100 |
| 6 | lgpl-2.1 | 799,664 |
| 7 | bsd-2-clause | 692,357 |
| 8 | lgpl-3.0 | 582,277 |
| 9 | mpl-2.0 | 457,000 |
| 10 | cc0-1.0 | 449,149 |
| 11 | epl-1.0 | 322,255 |
| 12 | unlicense | 208,602 |
| 13 | artistic-2.0 | 147,391 |
| 14 | isc | 118,332 |

**JOIN**句は頻繁に使うことになります。練習を重ねるうちに、どんどん効率よく書けるようになりますよ。

## 実践編
### はじめに(Introduction)
Stack Overflowは、技術的な質問のための非常に人気のあるQ&Aサイトです。
SQL（あるいは他のプログラミング言語）を使い続ける中で、あなた自身もきっと利用することになるでしょう。

それらのデータは公開されています。
それを使って、どんな面白いことができそうだと思いますか？

ひとつのアイデアとしては、次のようなサービスを作ることが考えられます。
特定の技術について関連する質問に回答することで専門性を示しているStack Overflowユーザーを特定し、その専門家を、より深いサポートのために雇えるようにするサービスです。

この演習では、このようなサービスの基盤となり得るSQLクエリを書いていきます。

いつものように、先へ進む前に以下のセルを実行してフィードバックシステムをセットアップしてください。
![](https://storage.googleapis.com/zenn-user-upload/cbb13cfd04da-20260214.png)

次のセルを実行して、`stackoverflow` データセットを取得してください。
![](https://storage.googleapis.com/zenn-user-upload/9134fd451deb-20260214.png)

### 演習(Exercises)
#### 1）データを探索する

    クエリや**JOIN**句を書く前に、どんなテーブルが利用可能かを確認したくなるでしょう。

ヒント：コマンドを思い出せないときはタブ補完が便利です。
`client.` と入力してからTabキーを押してみてください（Tabを押す前にピリオドを忘れずに）。

![](https://storage.googleapis.com/zenn-user-upload/31711033d7d2-20260214.png)

答え
![](https://storage.googleapis.com/zenn-user-upload/7b5abc807df4-20260214.png)

#### 2）関連するテーブルを確認する

特定のトピックについて質問に回答している人に興味がある場合、`posts_answers` テーブルは自然な出発点です。
次のセルを実行し、出力を確認してください。
```python
# Construct a reference to the "posts_answers" table
answers_table_ref = dataset_ref.table("posts_answers")

# API request - fetch the table
answers_table = client.get_table(answers_table_ref)

# Preview the first five lines of the "posts_answers" table
client.list_rows(answers_table, max_results=5).to_dataframe()
```

||id|title|body|accepted_answer_id|answer_count|comment_count|community_owned_date|creation_date|favorite_count|last_activity_date|last_edit_date|last_editor_display_name|last_editor_user_id|owner_display_name|owner_user_id|parent_id|post_type_id|score|tags|view_count|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|18|None|<p>For a table like this:</p>\n\n<pre><code>CR...|None|None|2|NaT|2008-08-01 05:12:44.193000+00:00|None|2016-06-02 05:56:26.060000+00:00|2016-06-02 05:56:26.060000+00:00|Jeff Atwood|126039|phpguy|<NA>|17|2|59|None|None|
|1|165|None|<p>You can use a <a href="http://sharpdevelop....|None|None|0|NaT|2008-08-01 18:04:25.023000+00:00|None|2019-04-06 14:03:51.080000+00:00|2019-04-06 14:03:51.080000+00:00|None|1721793|user2189331|<NA>|145|2|10|None|None|
|2|1028|None|<p>The VB code looks something like this:</p>\...|None|None|0|NaT|2008-08-04 04:58:40.300000+00:00|None|2013-02-07 13:22:14.680000+00:00|2013-02-07 13:22:14.680000+00:00|None|395659|user2189331|<NA>|947|2|8|None|None|
|3|1073|None|<p>My first choice would be a dedicated heap t...|None|None|0|NaT|2008-08-04 07:51:02.997000+00:00|None|2015-09-01 17:32:32.120000+00:00|2015-09-01 17:32:32.120000+00:00|None|45459|user2189331|<NA>|1069|2|29|None|None|
|4|1260|None|<p>I found the answer. all you have to do is a...|None|None|0|NaT|2008-08-04 14:06:02.863000+00:00|None|2016-12-20 08:38:48.867000+00:00|2016-12-20 08:38:48.867000+00:00|None|1221571|Jin|<NA>|1229|2|1|None|None|


まだ、特定のトピックに関する質問に回答したユーザーをどのように見つけるかは明確ではありません。
しかし、`posts_answers` には `parent_id` 列があります。Stack Overflowのサイトに慣れているなら、`parent_id` は各投稿が回答している「質問」を指していると推測できるかもしれません。

次に、`posts_questions` を確認してください。
```python
# Construct a reference to the "posts_questions" table
questions_table_ref = dataset_ref.table("posts_questions")

# API request - fetch the table
questions_table = client.get_table(questions_table_ref)

# Preview the first five lines of the "posts_questions" table
client.list_rows(questions_table, max_results=5).to_dataframe()
```

||id|title|body|accepted_answer_id|answer_count|comment_count|community_owned_date|creation_date|favorite_count|last_activity_date|last_edit_date|last_editor_display_name|last_editor_user_id|owner_display_name|owner_user_id|parent_id|post_type_id|score|tags|view_count|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|320268|Html.ActionLink doesn’t render # properly|<p>When using Html.ActionLink passing a string...|<NA>|0|0|NaT|2008-11-26 10:42:37.477000+00:00|0|2009-02-06 20:13:54.370000+00:00|NaT|None|<NA>|Paulo|<NA>|None|1|0|asp.net-mvc|390|
|1|324003|Primitive recursion|<p>how will i define the function 'simplify' ...|<NA>|0|0|NaT|2008-11-27 15:12:37.497000+00:00|0|2012-09-25 19:54:40.597000+00:00|2012-09-25 19:54:40.597000+00:00|Marcin|1288|None|41000|None|1|0|haskell|lambda|functional-programming|lambda-c...|497|
|2|390605|While vs. Do While|<p>I've seen both the blocks of code in use se...|390608|0|0|NaT|2008-12-24 01:49:54.230000+00:00|2|2008-12-24 03:08:55.897000+00:00|NaT|None|<NA>|Unkwntech|115|None|1|0|language-agnostic|loops|11262|
|3|413246|Protect ASP.NET Source code|<p>Im currently doing some research in how to ...|<NA>|0|0|NaT|2009-01-05 14:23:51.040000+00:00|0|2009-03-24 21:30:22.370000+00:00|2009-01-05 14:42:28.257000+00:00|Tom Anderson|13502|Velnias|<NA>|None|1|0|asp.net|deployment|obfuscation|4823|
|4|454921|Difference between "int[] myArray" and "int my...|<blockquote>\n <p><strong>Possible Duplicate:...|454928|0|0|NaT|2009-01-18 10:22:52.177000+00:00|0|2009-01-18 10:30:50.930000+00:00|2017-05-23 11:49:26.567000+00:00|None|-1|Evan Fosmark|49701|None|1|0|java|arrays|798|



各質問がどのトピックや技術に関するものかを識別するフィールドはありますか？
もしあるなら、特定のトピックに関する質問に回答したユーザーのIDをどのように見つけられるでしょうか？
少し考えてから、次のセルを実行して解答を確認してください。

解答：
`posts_questions` には `tags` という列があり、各質問がどのトピック／技術に関するものかが記載されています。

`posts_answers` には `parent_id` という列があり、各回答がどの質問に対するものか、その質問のIDを示しています。また、`posts_answers` には `owner_user_id` という列もあり、その質問に回答したユーザーのIDが格納されています。

この2つのテーブルをJOINすることで、次のことができます。

* 各回答に対応する質問のタグを特定し、
* 目的のタグに関する質問へ回答したユーザーの `owner_user_id` を取得する。

これが、これからのいくつかの問題で実際に行う内容です。


#### 3）適切な質問を選ぶ

このデータの多くはテキストです。

このコースの最後に、テキストに適用できるもうひとつのテクニックを紹介します。

**`WHERE`** 句では、**`LIKE`** を使って特定の文字列を含む行だけに結果を絞ることができます。
たとえば、チュートリアルの `pets` テーブルの3行目だけを取得するには、画像内のクエリを使えます。

![](https://storage.googleapis.com/zenn-user-upload/1a01227589a1-20260214.png)

また、**`%`** を「任意の文字数のワイルドカード」として使うこともできます。
そのため、次のようにしても3行目を取得できます。

query = """
        SELECT * 
        FROM `bigquery-public-data.pet_records.pets` 
        WHERE Name LIKE '%ipl%'
        """

今度は自分でやってみましょう。`posts_questions` テーブルから、`id`、`title`、`owner_user_id` 列を取得するクエリを書いてください。

条件：

* `tags` 列に `"bigquery"` という単語を含む行に限定する
* `"bigquery"` 以外の文字が含まれていてもよい（例：`"bigquery-sql"` というタグも含める）

![](https://storage.googleapis.com/zenn-user-upload/6c4eb44c468e-20260214.png)

ヒントと答え
![](https://storage.googleapis.com/zenn-user-upload/5635f7ecba0e-20260214.png)

#### 4）はじめてのJOIN
これで、特定のトピック（今回は `"bigquery"`）に関する質問を選択するクエリができました。次は、**JOIN**を使ってそれらの質問に対する回答を見つけることができます。

下記条件で、`posts_answers` テーブルから、`id`、`body`、`owner_user_id` 列を返すクエリを書いてください。
条件：
* `"bigquery"` をタグに含む質問への回答のみ
* 結果には、その条件に当てはまる「各回答」ごとに1行ずつ含める

質問のタグは `posts_questions` テーブルの `tags` 列から取得できます。

チュートリアルのJOIN例も参考にしてください。
![](https://storage.googleapis.com/zenn-user-upload/3d49f1e67b5b-20260214.png)

ヒントと答え
![](https://storage.googleapis.com/zenn-user-upload/2a911329ab11-20260214.png)

#### 5）問いに答える

必要なマージ（結合）はできました。

しかし、あなたが欲しいのは「多くの質問に回答しているユーザーの一覧」です。そのためには、さらに一歩進んだ処理が必要です。次の条件を満たす新しいクエリを書いてください：

* `"bigquery"` を含むタグの質問に **少なくとも1回は回答している** 各ユーザーについて1行
* 結果は次の2列を持つ：
  * `user_id`：`posts_answers` の `owner_user_id`
  * `number_of_answers`：そのユーザーが `"bigquery"` 関連質問に投稿した回答数

![](https://storage.googleapis.com/zenn-user-upload/e04a68950af5-20260214.png)

ヒントと答え
![](https://storage.googleapis.com/zenn-user-upload/8fd0369e22bb-20260214.png)

#### 6）より汎用的なサービスを構築する

これまでに行ったことを、ウェブサイトのバックエンドから呼び出せる「任意のトピックに対して専門家を取得する汎用関数」に変換するにはどうすればよいでしょうか？

少し考えてから、解答を確認してください。

```python
def expert_finder(topic, client):
    '''
    Returns a DataFrame with the user IDs who have written Stack Overflow answers on a topic.

    Inputs:
        topic: A string with the topic of interest
        client: A Client object that specifies the connection to the Stack Overflow dataset

    Outputs:
        results: A DataFrame with columns for user_id and number_of_answers. Follows similar logic to bigquery_experts_results shown above.
    '''
    my_query = """
               SELECT a.owner_user_id AS user_id, COUNT(1) AS number_of_answers
               FROM `bigquery-public-data.stackoverflow.posts_questions` AS q
               INNER JOIN `bigquery-public-data.stackoverflow.posts_answers` AS a
                   ON q.id = a.parent_Id
               WHERE q.tags like '%{topic}%'
               GROUP BY a.owner_user_id
               """
               
    # Set up the query (a real service would have good error handling for 
    # queries that scan too much data)
    safe_config = bigquery.QueryJobConfig(maximum_bytes_billed=10**10)      
    my_query_job = client.query(my_query, job_config=safe_config)
    
    # API request - run the query, and return a pandas DataFrame
    results = my_query_job.to_dataframe()

    return results
```

### Congratulations!
これで、BigQueryとSQLを効果的に使うための主要な要素はすべて習得しました。
あなたのSQLスキルは、世界最大級のデータセットを活用するために十分なレベルに達しています。
新しく手に入れた力を試してみたくなりませんか？Kaggleには、BigQueryデータセットが公開されていますよ。
