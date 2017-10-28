@title[Title]
# `SpringBoot` とMyBatisでデータベースを可視化する

JJUG CCC 2017 Fall (2017/11/18)

---

@title[Title]

# 自己紹介

## @su_kun_1899

![icon](https://pbs.twimg.com/profile_images/751787309322821633/BqUIbey1_400x400.jpg)

PaDDエンジニア。モブプログラマー。心はいつでもスクラムマスター。  
もっと楽しく仕事をする「PaDD」こと「Passion Driven Development」を心情に、自分が今いる現場を少しでも改善しようと日々奮闘してます。

---

@title[Title]
# 今日話すこと

- レガシーシステムと戦う切り口として、データ構造の可視化が大切
- MySQLのデータ構造を可視化するSpring Bootアプリケーションを作った
- Spring Boot, MyBatis, Spockを使ってみた

---

# レガシーシステム？

- 複雑に肥大化
- 不十分な構成管理
- 再現性のない環境
- 整備されていないテスト
- 失われた（サポートの切れた）技術
- etc

---

# 要は

よく分からないし、どっかをいじったらどっかが壊れそうなシステム  
（もしかしたらもう壊れてるのかも。。）

---

# どう戦うか？

- よく分からないものを分かるようにする

---

# データから切り込む

- システムは多くの場合インプットとアウトプットで成り立つ
- データがシステムの成果物であることが多い
- アプリケーションには設計や思想が現れる
  - 設計が歪んでいる場合、かえってノイズになることが多い

---

# 最終的に実現したいこと、がデータに表れていることが多い

---

# どう戦うか？

- データ構造を抑える

---

# そうだ、ER図だ

---

# エンティティ定義書.xls

- 一部最新でないものがあります
- 一部扱っていないテーブルがあります
- 一部(ry

---

# ドキュメントは古くなる

- ドキュメントは元々システム開発のインプット（設計書）としての役割が大きかった
- システムはより育てるものへとシフトしている
- リリースサイクルが上がる一方の中、システムに追従していくことは高コスト
- そして置いていかれる。。

---

# 信じられるのはコードだけ

今どうなっているか、が唯一の真実

---

# でも

```sql
desc hoge_table;
show create table hoge;
```

DBにログインして都度構造を確認するのはツライ。。

---

# ここまでのまとめ

- システム開発ではデータ構造を抑えるのが重要（ぽい）
- ドキュメントで表現するのは高コスト（ぽい）
- どうやってデータ構造を抑えていくのがいいだろうか

---

# データ構造をどう抑えるか

- メタ情報からデータ構造を可視化する

---

# Information Schema

- table, view, column, procedureの情報を読み取り専用Viewとして提供
- 標準SQLで定義されている

```sql
=> SELECT count(table_name) FROM information_schema.tables;
  count 
 -------
     99
 (1 row)

 => SELECT column_name, data_type, column_default, is_nullable
       FROM information_schema.columns WHERE table_name='alpha';
  column_name | data_type | column_default | is_nullable 
 -------------+-----------+----------------+-------------
  foo         | integer   |                | YES
  bar         | character |                | YES
 (2 rows)
```

(引用元)
https://en.wikipedia.org/wiki/Information_schema

---

# SchemaSpy

- マイクロサービスアーキテクチャで知った
- Information Schemaの情報を元にHTMLを生成

(参考)
https://qiita.com/su-kun1899/items/b106a1a643bf49df164d

---

# SchemaSpy

- めっちゃよい

（画面キャプチャ）

---

# SchemaSpyの問題点

- HTML生成によるタイムラグ
- デプロイ場所
  - S3にホスティングしてた
- 並列コストが高め
  - 並行開発って普通にあるよね。。
  - 最新だけでなく開発中のものも見たい
- 他のER逆生成ツールも大概同様の問題
  - MySQL Workbenchとか（すごいんですけどね）

---

# shishamo爆誕

（よーさんが作ったアイコン画像）

---

# shishamo

- MySQLのメタ情報を可視化するWebアプリケーション
- オンラインでInformation Schemaにクエリを発行する
- ししゃもはイルカの餌らしいので、MySQLのマスコットにあやかって命名

---

# shishamoの強み

- ~~HTML生成によるタイムラグ~~
- ~~デプロイ場所~~
- ~~並列コストが高め~~  
- リアルタイムにメタ情報を可視化できる
- Javaさえあればどこでも動く（やや誇張だけど）
  - ローカルでもテスト環境でも本番でも

---

# shishamo

(画面キャプチャかデモの動画か)

1. MySQLがある
1. cloneして起動
1. boot:run
1. ブラウザアクセス

---

# READMEより

```console
$ git clone git@github.com:su-kun1899/shishamo.git
$ cd shishamo/
$ ./mvnw spring-boot:run \
    -Dspring.datasource.url=jdbc:mysql://<Your mysql host: localhost>>:<Your mysql port: 3306>/<Your mysql schema> \
    -Dspring.datasource.schema=<Your mysql schema> \
    -Dspring.datasource.username=<Your mysql user> \
    -Dspring.datasource.password=<Your mysql password>
```

---

# shishamo

拍手どうぞ

---

# shishamoの弱み

- クエリ発行によるDBへの負荷
- 静的ファイルより応答性能は劣る

---

# ここまでのまとめ

- 「今どうなってるか」を正確に表すドキュメントは強い
- shishamo便利ですので使ってみて下さい！

---

# shishamoを支える技術

- Spring Boot
- MyBatis
- Spock

---

# shishamo最大の強み

- Javaさえあればどこでも動く

---

# Spring Bootのおかげ

---

# Spring Boot

> Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can “just run”.

---

# 簡単・お手軽でありながら高機能・多機能なSpringに乗っかっている

- あるといいながある

---

# 小さくはじめて大きく育てられる

- shishamoは現時点では大したことはしていない
- 機能追加したくなった時に、Springの世界が助けてくれる（と思う）

---

# ちょっと作ってみようがハイクオリティ

- 雑に作ったツールが思いの外広まってカオス化したことないですか？

---

# Spring Bootで始めよう

---

# MyBatis

カスタムSQL、ストアドプロシージャ、高度なマッピング処理に対応した優れた永続化フレームワーク

---

# MyBatis

- SQLベースのマッピングが強力
- SQLを書きたい時に向いている

---

# コード例

```java
// TODO
```

---

# SQLを書きたい時？

- データモデルが一定の規模以上になり、ドメインモデルとの差分が大きくなっている
- データベースの変更コストが高い（あるいはかけられない）

---

# shishamoの場合

- Information Schemaに対するクエリ
- アプリケーションの都合でデータモデルを変更できない

---

# 本質的にデータモデルとドメインモデルは別物である

- データモデルからドメインモデルに詰め替えるような処理が想定されるならMyBatisを選択する価値はある
- アプリケーションレイヤで扱うオブジェクトがDB構造と一致する（ようにする）のであれば別の選択肢の方がいい
  - いわゆるORマッパー 

---

# MyBatisはそれなりにコストがかかる

- SQLを書かなくてはいけない
- 学習コスト
  - Mapper XMLでのカスタムSQL
  - SQLに制御構造を埋め込む

---

# 本質的にデータモデルとドメインモデルは別物である

- それでもデータ構造に引きずられなくて済むのは大きな利点
- DB定義変更のためにドメインモデルに手を入れたり、DTOやEntityのような一時受けクラスが不要になる
  - (余談ですが)そうなってきたらORマッパーの載せ替えを検討した方がいいと思う

---

# 余談

---

# まとめ

- Spring BootでWebアプリ始めるの簡単
- MyBatisのマッピングはかなり強力
- Spock最高

---

# ネタ

- ハッシュタグタイトルに入れよう
  - フィードバック下さい

- SpringBootの話はあんまりしません
  - 踏み込んだ話というよりはひとつの活用事例として

- ドメインモデルとデータモデルに差分が殆ど無いような場合
- あえて用意するほどでもない場合
- 一定規模以下でSQLを書くコストの方が高い場合

- はじめるのが楽
- Web APIを用意するのが楽
- 情報が多い

- タイトル
- 自己紹介
- 今日伝えたいこと
  - 可視化大事
  - 世界は便利になっている
  - なんか作ってみよう
- 今日話すこと
  - Spring BootでDBの可視化ツールを作った
    - なぜ作ったのか
    - どうやって作ったのか
    - 作ってみてどうだったのか
- データベースの可視化
  - レガシーシステムではデータ構造を抑えるのが肝
  - descやshow create tableを手打ちする生活
- SchemaSpy
  - マイクロサービスアーキテクチャ読んで知った
- Spring Boot
- My Batis
- Spock
- Maven
- IntelliJ IDEA
- オチ
  - Kotlinで書き換えるプルリクが来た
