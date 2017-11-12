slidenumbers: true
autoscale: true
<!-- Theme: Courier の水色 -->

## SpringBootとMyBatisで<br/>データベースを可視化する

### 2017/11/18 JJUG CCC 2017 Fall

### #ccc_l1

---

![right 100%](https://www.gravatar.com/avatar/25f2433d54eaed08557621c1da4fb7db?s=300)

# 自己紹介

## @su\_kun\_1899

PaDDエンジニア。モブプログラマ。エモ枠。心はいつでもスクラムマスター。
<br/><br/>
株式会社エス・エム・エスで介護事業所向けのWebサービス開発してます。

---

# 今日話すこと

- 前半
  - レガシーシステムとデータ構造の可視化
  - shishamoというWebアプリの紹介
- 後半
  - shishamoを支える技術
  - Spring Boot
  - MyBatis
  - Spock

※あまり技術を深掘りした話はしません。何かを始めるきっかけやヒントとして助けになれば。

^Bootは情報も多いので、MyBatisを少し厚めに。使い所なんかも話せればなと。

---

# [fit]フィードバック下さい
## <br/>#ccc_l1

![inline right 80%](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/ojigi_boy.png)

---

# [fit]Introduction

---

# [fit]開発してる時何見てますか？

---

![](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/monitor.png)

![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/ide.jpg)
![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/browser.jpg)![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/document.jpg)

^ドキュメントブラウザで見てるとかはあるかもしれないけど、大体こんな感じなんじゃないかなー、と。

---

# [fit]DB定義ってどうやって確認してますか？

---

![](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/monitor.png)

![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/table.jpg)![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/column.jpg)
![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/index.jpg)![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/relation.jpg)![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/etc.jpg)

^これはあんまりイメージがない
MySQLワークベンチとか、OBBRとか、IDE組み込みのソフト？
何かしらのドキュメント？
DB直接見に行っちゃう？

---

# [fit]shishamoというツールを作りました

![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/shishamo.png)

https://github.com/su-kun1899/shishamo

---

# shishamo?

- MySQLのメタ情報を可視化するWebアプリケーション
- オンラインでInformation Schemaにクエリを発行する
- ししゃもはイルカの餌らしいので、MySQLのマスコットにあやかって命名

^あのバンドには関係ありません

---

# demo

---

# 拍手どうぞ

---

# 改めまして

---

## SpringBootとMyBatisで<br/>データベースを可視化する

### 2017/11/18 JJUG CCC 2017 Fall

### #ccc_l1

---

# [fit]**shishamoは**レガシーシステム**と戦うために生まれました**

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

![150%](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/junk_kikai.png)

^設計書読んでもよく分からん、アプリのコードはカオス、ドメイン知識も足りない

---

> どう戦うか？
-- よく分からないものを分かるようにする

---

# データから切り込む

- システムは多くの場合インプットとアウトプットで成り立つ
- データがシステムの成果物であることが多い
- アプリケーションには設計や思想が現れる
  - 設計が歪んでいる場合、かえってノイズになることが多い

---

# [fit]「最終的に実現したいこと」は<br/>データに表れていることが多い

![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/in-out.png)

---

> どう戦うか？
-- データ構造を抑える

---

# **そうだ、ER図だ**

---

![](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/exceldoc.png)

# エンティティ定義書.xls

- 1000以上のオブジェクトが一つのブックに。。
- 一部最新でないものがあります
- 一部扱っていないテーブルがあります
- 一部(ry

^マクロの検索機能が提供されていた
重いこと重いこと

---

# ドキュメントは古くなる

- ドキュメントは元々システム開発のインプット（設計書）としての役割が大きかった
- システムはより育てるものへとシフトしている
- リリースサイクルが上がる一方の中、システムに追従していくことは高コスト
- そして置いていかれる。。

---

> 信じられるのは
> コードだけ
-- 今どうなっているか、が唯一の真実

---

# [fit]でも都度DB構造を確認するのはツライ。。

```sql
show create table hoge_table;
```

![inline right 70%](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/pose_atama_kakaeru_man.png)

---

# どうやら

- システム開発ではデータ構造を抑えるのが重要（ぽい）
- ドキュメントで表現するのは高コスト（ぽい）
- どうやってデータ構造を抑えていくのがいいだろうか

---

> データ構造を<br/>どう抑えるか
-- メタ情報からデータ構造を可視化する

---

# Information Schema

- table, view, column, procedureの情報を読み取り専用Viewとして提供
- 標準SQLで定義されている

```sql, [.highlight: 4-7]
SELECT column_name, data_type, column_default, is_nullable
       FROM information_schema.columns WHERE table_name='alpha';

  column_name | data_type | column_default | is_nullable 
 -------------+-----------+----------------+-------------
  foo         | integer   |                | YES
  bar         | character |                | YES
 (2 rows)
```

https://en.wikipedia.org/wiki/Information_schema

^名前、定義、制約。。大体なんでも入ってる
RDBMS毎に差分はある模様

---

# SchemaSpy

- マイクロサービスアーキテクチャで知った
- Information Schemaを元にHTMLを生成

### [fit]**(参考)<br/>https://qiita.com/su-kun1899/items/b106a1a643bf49df164d**

^オライリーの本です
MSA本で紹介されていたのは示唆に富んでいる。（システムの分割の文脈）
何かをしようと思ったらまず現状を可視化することの重要性

---

# SchemaSpy

#### めっちゃよいです

![fit right](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/schemaspy.png)

![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/schemaspy2.png)

^どこかでみたことのある見た目です。
shishamoの発想はSchemaSpyがなければ生まれませんでした。

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

# [fit]shishamo

![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/shishamo.png)

---

# shishamoの強み

- リアルタイムにメタ情報を可視化できる<br/>  -> タイムラグがない
- Javaさえあればどこでも動く（やや誇張だけど）<br/>  -> デプロイ場所を選ばない
- 手軽に増やせる<br/>  -> ローカル、Dev、QA、etc...

^開発者数分起動しても問題ない

---

# すぐ使える

1. git clone
1. 引数に接続先DBを指定してboot:run
1. ブラウザアクセス

```
$ git clone git@github.com:su-kun1899/shishamo.git
$ cd shishamo/
$ ./mvnw spring-boot:run \
    -Dspring.datasource.url=<Your database url> \
    -Dspring.datasource.schema=<Your mysql schema> \
    -Dspring.datasource.username=<Your mysql user> \
    -Dspring.datasource.password=<Your mysql password>
```

---

# Simple

- Table一覧
- Table定義
- Column定義
- Index定義
- Relation
- show create table

---

![inline fit](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/shishamo-table.png)

---

# shishamoの弱み

- クエリ発行によるDBへの負荷
- 静的ファイルより応答性能は劣る
- MySQLでしか使えない（今のところ）

^Postgresql対応しようかなーと思いつつ早幾月。。

---

# [fit]「今どうなってるか」を
# [fit]<br/>正確に表すドキュメントは
# <br/>強い
### <br/><br/>**shishamo使ってみて下さい！**

---

# shishamoを支える技術

- Spring Boot
- MyBatis
- Spock

ここから後半戦。残り時間は大丈夫か？

---

> shishamo<br/><br/>最大の強み
-- Javaさえあればどこでも動く

---

# [fit]Spring Bootのおかげ

---

# Spring Boot

> Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can “just run”.

^公式ドキュメントに書いてある。簡単にスタンドアローン、プロダクションレベルのSpring Appが作れる！

---

# [fit]簡単・お手軽 && 高機能・多機能<br/>Springに乗っかっている

^あるといいながある

---

# [fit]ちょっと作ってみようが高品質

雑に作ったツールが思いの外広まってカオス化したことないですか？

^気軽に作っているものでも、プロダクションレベルで

---

# 小さくはじめて大きく育てる

- shishamoは現時点では大したことはしていない
- 機能追加したくなった時に、Springの世界が助けてくれる（と思う）

^今のところ必要性がないので丁寧に作ってませんが、WebAPI作ったりとか。
認証とか基本的なものもそうだし、Actuatorのような監視用のEndPointも

---

# Spring Bootで<br/>始めよう

---

# MyBatis

カスタムSQL、ストアドプロシージャ、高度なマッピング処理に対応した優れた永続化フレームワーク

---

# Overview

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

# [fit]めんどくさそうと感じたなら正解です

---

# Auto Mappingを使う

- MyBatisが列名と同じ名前を持つプロパティを探す
- 列名はエイリアスで揃えることができる

---

# Result Map

- autoMapping属性をつけてやればよい
- グローバルに設定することも可能

```xml, [.highlight: 1,5]
<resultMap id="userResultMap" type="User" autoMapping="true">
  <!-- <id property="id" column="user_id" /> -->
  <!-- <result property="name" column="user_name"/> -->
  <!-- <result property="sex" column="user_sex"/> -->
</resultMap>
```

---

# Statement

- エイリアスで、同名で取れるようにしてあげればよい

```xml, [.highlight: 3-5]
<select id="selectUsers" resultMap="userResultMap">
  select
    user_id     as id,
    user_name   as name,
    sex         as sex
  from 
    some_table
  where
    id = #{id}
</select>
```

---

# [fit]便利そうな雰囲気が出てきました！

---

# ネストしたオブジェクト

- 1 対 1

---

### Address

|address_id| address_city |
|---|---|
|11|tokyo|
|12|osaka|

### User

| user\_id|user\_name| user\_sex | address\_id |
| --- | ---| --- | --- |
| Tokyo      | Kantō   | Tokyo    | 11 |
| Kanagawa   | Kantō   | Yokohama | 12 |


---

# こんな感じで取る

|user<br/>id|user<br/>name|user<br/>sex|address<br/>id| address<br/>city |
| --- | --- | --- | --- | --- | --- |
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

---

# Result Map

```xml
<!-- User -->
<resultMap id="userResultMap" type="User" 
      autoMapping="true" columnPrefix="user_">
  <id column="id"/>
  <association property="address" 
        resultMap="addressResultMap"  columnPrefix="address_"/>
</resultMap>
<!-- Address -->
<resultMap id="addressResultMap" type="Address" autoMapping="true">
  <id property="id" column="id"/>
</resultMap>
```

^オブジェクトを一意にするカラムをidで定義
resultMapは個別に定義する（再利用も可能）
associationで紐づきを定義
columnPrefixを使うとSQLで名前重複を回避できる

---

# Statement

- エイリアスで、同名で取れるようにしてあげればよい
- columnPrefixが対象のオブジェクトに紐付けてくれる

---

# Statement

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

^エイリアスで、同名で取れるようにしてあげればよい
columnPrefixが対象のオブジェクトに紐付けてくれる

---

# ネストしたオブジェクト

- 1 対 多

---

### User

| user\_id | user\_name| user\_sex | address\_id |
|---|---|---|---|
|1|yamada|male|11|
|2|sato|female|12|

### Hobby

|hobby\_id| hobby\_name |
|---|---|
|21|football|
|22|runnning|
|23|video game|
|24|programming|

---

### User_Hobby

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

^紐付け用のエンティティはいらない
あくまでデータモデルなので

---

# Result Map

- collectionタグで紐付ける
- idタグの定義が階層を特定する

---

# Result Map

```xml
<resultMap id="userResultMap" type="User" autoMapping="true"
       columnPrefix="user_">
  <id column="id"/>
  <collection property="hobbies" ofType="Hobby" 
        resultMap="hobbyResultMap" columnPrefix="hobby_"/>
</resultMap>
<resultMap id="hobbyResultMap" type="Hobby" autoMapping="true">
  <id property="id" column="id"/>
</resultMap>
```

^collectionタグで紐付ける
idタグの定義が階層を特定する

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

# [fit]データモデルとドメインモデルは<br/>本来別物

- データモデルとドメインモデルのミスマッチ解消にMyBatisを選択する価値はある
- アプリケーションレイヤで扱うオブジェクトがDB構造と一致する（ようにする）のであれば別の選択肢の方がいい
  - いわゆるORマッパー

^エンティティがそのままでアプリケーションレイヤで使えなさそうなら

---

# [fit]MyBatisはそれなりにコストがかかる

- SQLを書かなくてはいけない
- 学習コスト
  - Mapper XMLでのカスタムSQL
  - SQLに制御構造を埋め込む

---

# それでも

- データ構造に引きずられなくて済むのは大きな利点
- DB定義変更のためにドメインモデルに手を入れたり、DTOやEntityのような一時受けクラスが不要になる
  - (余談ですが)そうなってきたらORマッパーの載せ替えを検討した方がいいと思う
- データモデルにドメインモデルが引きずられるのは本末転倒な気がする

---

# [fit]MyBatisという選択肢を<br/>手に入れよう

---

# Spock

- Java/Groovyアプリケーションのためのテスティングフレームワーク
- Groovyで書きます（Javaでも書けるけど）

---

# Spockは最高です

- 今日一番言いたいことといっても過言ではない
- shishamoはGitHubではGroovyプロジェクト

![inline 200% right](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/github.png)

---

# Spockのいいところ

- given/when/then等のブロック
- 強力なMock機構
- Data Tablesによるパラメータライズドテスト
- 低い学習コスト
- and more

---

# [fit]given/when/then等のブロック

- setup
- given
- when
- then
- expect
- cleanup
- where

---

# テストの意図が明白

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

# こんなことも

```groovy
// 引数がhelloのときだけ
1 * subscriber.receive("hello")
// 引数がhelloではないときだけ
1 * subscriber.receive(!"hello")
// 引数ひとつならなんでも
1 * subscriber.receive(_)
// 引数いくつでもなんでも
1 * subscriber.receive(*_)
// Stringの引数ひとつならなんでも
1 * subscriber.receive(_ as String)
// predicateも使える（引数のsizeが 3 より大きい）
1 * subscriber.receive({ it.size() > 3 })
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

# [fit]なぜSpockなのか？

---

## MockitoでのMocking

```java
LinkedList mockedList = mock(LinkedList.class);
when(mockedList.get(0)).thenReturn("first");
```

---

## SpockでのMocking

```groovy
def mockedList = Mock(LinkedList);
mockedList.get(0) >> "first";
```
---

## JUnitでのパラメータライズドテスト

```java
@ParameterizedTest
@ValueSource(strings = { "Hello", "World" })
void testWithStringParameter(String argument) {
    assertNotNull(argument);
}
```

- JUnit5でだいぶ楽になった模様
- JUnit4だとTheoriesを使ってもっと大変

---

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

# [fit]Spockに触ってみよう

---

# テスト対象クラス

```java
@Service
public class UserService {
    private UserRepository userRepository;
    // コンストラクタ
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    // テスト対象メソッド
    @Transactional(readOnly = true)
    public List<User> findBy(Sex sex) {
        return userRepository.selectBySex(sex);
    }
}
```

---

# テストクラス

```groovy
class UserServiceSpec extends Specification {
    UserService userService
    UserRepository userRepository;

    def 'Get users by sex'() {
        given: 'Mocking repository'
        def userRepository = Mock(UserRepository)
        userRepository.selectBySex(Sex.MALE) >> {[
            new User(name: 'Taro', sex: Sex.MALE),
            new User(name: 'Jiro', sex: Sex.MALE),
        ]}

        and:
        def userService = new UserRepository(userRepository)

        when:
        def actual = userService.get(Sex.MALE)

        then:
        actual.size() == 2
        actual.get(0).getName() == 'Taro'
        actual.get(1).getName() == 'Jiro'
    }
}
```

---

# given

```groovy
given: 'Mocking repository'
def userRepository = Mock(UserRepository)
userRepository.selectBySex(Sex.MALE) >> {[
    new User(name: 'Taro', sex: Sex.MALE),
    new User(name: 'Jiro', sex: Sex.MALE),
]}

and: 'コンストラクタインジェクション最高'
def userService = new UserRepository(userRepository)
```

^ブロックには名前付けられる
型推論してくれるのでdefでいい
Bootのコンストラクタインジェクションのお陰でフレームワーク依存性無くインスタンス生成

---

# when-then

```groovy
when:
def actual = userService.get(Sex.MALE)

then:
actual.size() == 2
actual.get(0).getName() == 'Taro'
actual.get(1).getName() == 'Jiro'
```

^Groovyは文字列比較はequalsじゃなくてよい
thenブロックは暗黙的にassertされる

---

# いろいろ言いましたが

- 触ってみてはじめて良さが分かると思います
- 以前導入に反対したことがあります
  - 別言語で新しいフレームワーク導入怖かった
  - そのときはJUnitにしました
  - 今でも冗談でいじられます

---

# まとめ

---

# [fit]多くのシステムにはDBがある

![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/application.jpg)
![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/database.jpg)

---

# [fit]DBはシステム全体を支えている

![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/tool.jpg)![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/application.jpg)![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/operation.jpg)
![inline](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/database.jpg)

---

# [fit]DBの寿命はアプリケーションよりも長い

^別にレガシーシステムに限らずそういうもの

---

# [fit]既存のDBを活かした<br/>アプリケーションを<br/>作りたい

^アプリケーションレイヤの置き換え、ツールの開発

---

## DBの直接操作を<br/>やめたい<br/>共有データベースを<br/>やめたい

^そもそも共有データベースをやめたい。API化とか運用ツール

---

### 作るの大変そうだな。。<br/>環境の準備できるかな。。

---

# Spring Boot!

---

### 設計して実装しよう。。<br/>テーブル定義どうなってたっけ？

---

![original 100%](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/shishamo.png)

---

### データ構造とモデルがミスマッチかも？<br/>今のSQL資産を活かせないかな？

---

# MyBatis!!

---

### SQLを書こう。。<br/>リレーションどうなってたっけ？<br/>インデックスは？

---

![original 80%](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/shishamo-table.png)

---

### テストしなくっちゃ。。<br/>楽しくテストを書く方法はないかな？

---

# Spock!!!

---

### テストデータが必要だな。。<br/>どのテーブルにどんなデータが必要だろう？

---

![original 80%](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/shishamo_real.jpg)

---

### そろそろリリースできそうか。。？

---

# [fit]“just run”

---

# 今日言いたかったこと

- SpringBoot + MyBatis + Spock という開発パック
  - SpringBootは簡単で高機能
  - MyBatisのマッピングは強力
  - Spock最高なので試して下さい
- 既存資産を改善するには可視化が大事
  - 「現状」を正確に表す
  - ドキュメントにもコードにも頼りすぎない
  - DBに限った話ではない

^今日お伝えしたかったことのまとめ
shishamoは一つの実例でしかない
ドキュメントは実情と乖離し、コードは手間が多い

---

# 余談

同僚にshishamoを紹介したらすべてKotlinで書き換えるプルリクエストが送られてきたことをここにご報告します。

---

# おしまい

> Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can “just run”.

![inline 50%](https://raw.githubusercontent.com/su-kun1899/shishamo-slide/master/img/shishamo.png)
