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

- 1000以上のオブジェクトが一つのブックに。。
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

1. git clone
1. 引数に接続先DBを指定してboot:run
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

(画面キャプチャ)

---

# 拍手どうぞ

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

# コード例

- ざっくりいうとXMLにSQLとマッピング定義を書きます
- ifや繰り返しの制御文も書けます（今日は紹介しません）
  - MyBatisの強力さはマッピングにあると思うので

---

# model

```java
public class User {
  private int id;
  private String name;
  private String sex;
  // アクセサは省略
}
```

---

# interface

```java
@Repository
@Mapper
public interface UserRepository {
  List<User> selectBy(int id);
}
```
---

# Result Map

```xml
<resultMap id="userResultMap" type="User">
  <id property="id" column="user_id" />
  <result property="name" column="user_name"/>
  <result property="sex" column="user_sex"/>
</resultMap>
```

---

# Statement

```xml
<select id="selectUsers" resultMap="userResultMap">
  select
    user_id, 
    user_name,
    user_sex
  from 
    some_table
  where 
    id = #{id}
</select>
```

---

# めんどくさそうと感じたのは正解です

---

# Auto Mappingを使う

- MyBatisが列名と同じ名前を持つプロパティを探す
- 列名はエイリアスで揃えることができる

---

# Result Map

- autoMapping属性をつけてやればよい
- グローバルに設定することも可能

```xml
<resultMap id="userResultMap" type="User" autoMapping="true" />
```

---

# Statement

- エイリアスで、同名で取れるようにしてあげればよい

```xml
<select id="selectUsers" resultMap="userResultMap">
  select
    user_id     as id,
    user_name   as name
    sex         as sex
  from 
    some_table
  where
    id = #{id}
</select>
```

---

# 便利そうな雰囲気が出てきました！

---

# ネストしたオブジェクト

- 1 対 1

---

# User 

| user_id | user_name| user_sex | address_id |
|---|---|---|---|
|1|yamada|male|11|
|2|sato|female|12|

# Address

|address_id| address_city |
|---|---|
|11|tokyo|
|12|osaka|

---

# こんな感じで取る

| user_id | user_name| user_sex |address_id| address_city |
|---|---|---|---|---|---|
|1|yamada|male|11|tokyo|
|2|sato|female|12|osaka|

---

# model

```java
public class User {
    private Address address;
    private int id;
    private String name;
    private String sex;
    // アクセサは省略
}

public class Address {
    private int id;
    private String city;
    // アクセサは省略
}
```

---

# Result Map

- オブジェクトを一意にするカラムをidで定義
- resultMapは個別に定義する（再利用も可能）
- associationで紐づきを定義
- columnPrefixを使うとSQLで名前重複を回避できる

```xml
<resultMap id="userResultMap" type="User" autoMapping="true" columnPrefix="user_">
  <id column="id"/>
  <association property="address" resultMap="addressResultMap"  columnPrefix="address_"/>
</resultMap>
<resultMap id="addressResultMap" type="Address" autoMapping="true">
  <id property="id" column="id"/>
</resultMap>
```

---

# Statement

- エイリアスで、同名で取れるようにしてあげればよい
- columnPrefixが対象のオブジェクトに紐付けてくれる

```xml
<select id="selectUsers" resultMap="userResultMap">
  select
    some_table.user_id    as user_id,
    some_table.user_name  as user_name,
    some_table.sex        as user_sex,
    address.address_id    as address_id,
    address.address_city  as address_city
  from 
    some_table
    inner join 
    address on some_table.address_id = address.address_id
  where
    some_table.id = #{id}
</select>
```

---

# ネストしたオブジェクト

- 1 対 多

---

# User 

| user_id | user_name| user_sex | address_id |
|---|---|---|---|
|1|yamada|male|11|
|2|sato|female|12|

# Hobby

|hobby_id| hobby_name |
|---|---|
|21|football|
|22|runnning|
|23|video game|
|24|programming|

# User_Hobby

| user_id | hobby_id |
|---|---|
| 1 | 21 |
| 1 | 23 |
| 1 | 24 |
| 2 | 22 |
| 2 | 24 |

---

# こんな感じで取る

| user_id | user_name| user_sex |hobby_id| hobby_name |
|---|---|---|---|---|---|
|1|yamada|male|21|football|
|1|yamada|male|23|video game|
|1|yamada|male|24|programming|
|2|sato|female|22|runnning|
|2|sato|female|24|programming|

---

# model

```java
public class User {
    private Address address;
    private int id;
    private String name;
    private String sex;
    private List<Hobby> hobbies;
    // アクセサは省略
}

public class Hobby {
    private int id;
    private String name;
    // アクセサは省略
}
```

---

# Result Map

- collectionタグで紐付ける
- idタグの定義が階層を特定する

```xml
<resultMap id="userResultMap" type="User" autoMapping="true" columnPrefix="user_">
  <id column="id"/>
  <collection property="hobbies" ofType="Hobby" resultMap="hobbyResultMap" columnPrefix="hobby_"/>
</resultMap>
<resultMap id="hobbyResultMap" type="Hobby" autoMapping="true">
  <id property="id" column="id"/>
</resultMap>
```

---

# Statement

```xml
<select id="selectUsers" resultMap="userResultMap">
  select
    some_table.user_id    as user_id,
    some_table.user_name  as user_name,
    some_table.sex        as user_sex,
    hobby.hobby_id        as hobby_id,
    hobby.hobby_name      as hobby_name
  from 
    some_table
    inner join 
    user_hobby on some_table.user_id = user_hobby.user_id
    inner join
    hobby on user_hobby.hobby_id = hobby.hobby_id
  where
    some_table.id = #{id}
</select>
```

---

# どんなときにMyBatis？

- SQLベースのマッピングが強力
- マッピング定義の記述コストを下げる工夫がされている
- つまり、SQLを書きたい時に向いている

---

# SQLを書きたい時？

- データモデルが一定の規模以上になり、ドメインモデルとの差分が大きくなっている
- データベースの変更コストが高い

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

# それでも

- それでもデータ構造に引きずられなくて済むのは大きな利点
- DB定義変更のためにドメインモデルに手を入れたり、DTOやEntityのような一時受けクラスが不要になる
  - (余談ですが)そうなってきたらORマッパーの載せ替えを検討した方がいいと思う
- データモデルにドメインモデルが引きずられるのは本末転倒な気がする

---

# MyBatisという選択肢を手に入れよう

---

# Spock

- Java/Groovyアプリケーションのためのテスティングフレームワーク
- Groovyで書きます（Javaでも書けるけど）

---

# Spockは最高です

- 今日一番言いたいことといっても過言ではない
- shishamoはGitHubにGroovyプロジェクトと思われている

---

# Spockのいいところ

- given/when/then等のブロック
- 強力なMock機構
- Data Tablesによるパラメータライズドテスト
- 低い学習コスト
- and more

---

# given/when/then等のブロック

```groovy
def "HashMap accepts null key"() {
  given:
  def map = new HashMap()

  when:
  map.put(null, "elem")

  then:
  notThrown(NullPointerException)
}
```

---

# 強力なMock機構

```groovy
given:
def subscriber = Mock(Subscriber)
subscriber.receive("message1") >> "ok"

and:
def publisher = new Publisher();

when:
publisher.send("message1")

then:
1 * subscriber.receive("message1")
```

---

# Data Tables

```groovy
def "maximum of two numbers"() {
  expect:
  Math.max(a, b) == c

  where:
  a | b || c
  1 | 3 || 3
  7 | 4 || 7
  0 | 0 || 0
}
```

---

# Groovyの柔軟性

- Javaに比べると「何でもあり」な感覚に陥る
- privateコンストラクタも特にMockingなしに呼び出せる程
  - ※例なので、それを呼び出すのがよいというわけではない

---

# インスタンス生成

```java
public class Foo {
  private long id;
  private String name;
  // アクセサ省略
}
```

---

# インスタンス生成

- 特にコンストラクタがなくても値セットで生成できる

```groovy
def foo = new Foo(id: 1, name: "sample")
```

---

# JUnit？

- JUnitは偉大
- テストでやりたいことは JUnit/Mockito/PowerMock があればほぼ実現できる

---

# なぜSpockなのか？

---

## MockitoでのMocking

```java
LinkedList mockedList = mock(LinkedList.class);
when(mockedList.get(0)).thenReturn("first");
```

## SpockでのMocking

```groovy
def mockedList = Mock(LinkedList);
mockedList.get(0) >> "first";
```
---

## JUnitでのパラメータライズドテスト

- JUnit5でだいぶ楽になった模様
- JUnit4だとTheoriesを使ってもっと大変

```java
@ParameterizedTest
@ValueSource(strings = { "Hello", "World" })
void testWithStringParameter(String argument) {
    assertNotNull(argument);
}
```

# SpockのData Tables

```groovy
def "with string parameter"() {
  expect:
  argument != null

  where:
  argument | _
  "Hello"  | _
  "World"  | _
}
```

---

# JUnit

- やりたいことはできるけど、覚えることが多い
- テストを書いているときに、何かを調べていることが多い
  - voidメソッドのMockどうやって書くんだっけ。。
  - パラメータライズドテストのアノテーションなんだっけ。。
  - etc

---

# Spock

- 記法がシンプル
- 本質的な「どうやって何をテストする」を考える時間が増える
- いい意味で雑に書ける
  - テストを書くのが大変だと、書く気がなくなる

---

# いろいろ言いましたが

- 触ってみてはじめて良さが分かると思います
- 以前導入に反対したことがあります
  - 別言語で新しいフレームワーク導入怖かった
  - そのときはJUnitにしました
  - 今でも冗談でいじられます

---

# Spockに触ってみよう

---

# まとめ

---

# shishamo

- データ構造の可視化はレガシーシステム改善の鍵
- shishamoが少しでもヒントになれば

---

# Spring Boot

- Spring BootでWebアプリ始めるの簡単
- 想像以上に気軽に始められる
- Springに乗っかれるので育てやすい

---

# MyBatis

- MyBatisのマッピングはかなり強力
- データモデルとドメインモデルを切り分けたい時の有力な選択肢

---

# Spock

- やってみると離れられなくなる
- 本当最高なので試してみて下さい

---

# おしまい

> Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can “just run”.

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
