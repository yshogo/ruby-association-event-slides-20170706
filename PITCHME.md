---?image=assets/images/crystal-logo.png&size=45% auto

---

- 資料は公開済みです
  - [https://gitpitch.com/at-grandpa/ruby-association-event-slides-20170706#](https://gitpitch.com/at-grandpa/ruby-association-event-slides-20170706#)
  - [#ruby_a](https://twitter.com/hashtag/ruby_a?src=hash)
- 実況ツイートなど大歓迎です
- GitPitch便利
  - スマホでもそれなりに見れます
  - 青文字のリンクはクリックできます
- 個人の調査・見解の範囲です


---

###### Matzさんの後で緊張しています🌀

---

<img src="assets/images/icon_512.jpg" width="30%">

- twitter: [@at_grandpa](https://twitter.com/at_grandpa)
- github: [@at-grandpa](https://github.com/at-grandpa)

---

## I love Ruby ❤️

---

<span style="font-size: 40px;font-weight: 600;">[圧倒亭グランパのブログ](http://at-grandpa.hatenablog.jp/)</span>

<img src="assets/images/blog-title-04-method.png" width="50%">

<span style="font-size: 25px;font-weight: 600;">[【ruby】 メソッド探索から見る、モジュール・特異メソッド・特異クラス](http://at-grandpa.hatenablog.jp/entry/2016/02/14/090544)</span>


---

<img src="assets/images/blog-title-05-keyword.png" width="55%">

<span style="font-size: 20px;font-weight: 600;">[
【ruby】キーワード引数のメソッド呼び出しは遅い！しかし2.2.0-preview2 以降で劇的に改善されていた話](http://at-grandpa.hatenablog.jp/entry/2016/03/07/091805)</span>


---

## I love Crystal lang ❤️

---

<img src="assets/images/blog-02-clim.png" width="70%">

###### [１秒でも早くCLIツールを作りたい by Crystal](http://at-grandpa.hatenablog.jp/entry/clim)


---

<img src="assets/images/blog-03-des.png" width="70%">

<span style="font-size: 35px;font-weight: 600;">[ちょっとしたdocker環境を素早く作れるツールを作った](http://at-grandpa.hatenablog.jp/entry/2017/06/22/090935)</span>


---

### Crystal勉強会 #4 in 渋谷

<img src="assets/images/blog-04-study-crystal.png" width="80%">

<span style="font-size: 25px;font-weight: 600;">http://at-grandpa.hatenablog.jp/entry/2016/12/01/194854</span>

---

### 技術書典２

<img src="assets/images/blog-05-techbookfest2.png" width="45%">


---?image=assets/images/crystal-web-site-top.png&size=cover

### Crystalのこれまでの歩みと
### v1.0 に向けたロードマップ
##### 2017.07.06 @at_grandpa


---

#### Crystalを
### ご存知の方🙋


---

### 触ったことある方🙋

---

### ライブラリを作ったことある方🙋

---

### Contributorの方🙋

---

<span style="font-size: 30px;">今日の目的</span>

### Crystalに興味を持っていただく⭐️

---

## 今日話すこと 🙆

- Crystalとはどんな言語なのか
- Crystalの現状はどうなのか

---

## 今日話さないこと 🙅

- パフォーマンスの詳細
  - なぜパフォーマンスが出るのか
  - 他の言語と比べてどうか
- 細かな仕組みの話
  - 型チェックはどういう仕組みか
  - Concurrencyはどう実現されているか

<span style="font-size: 40px;">etc...</span>

---

## Crystalとはどんな言語なのか

---

## GOALS

- Have a syntax similar to Ruby (but compatibility with it is not a goal) |
- Statically type-checked but without having to specify the type of variables or method arguments. |
- Be able to call C code by writing bindings to it in Crystal. |
- Have compile-time evaluation and generation of code, to avoid boilerplate code. |
- Compile to efficient native code. |

<span style="font-size: 20px;">crystal-lang/crystal - https://github.com/crystal-lang/crystal/blob/master/README.md</span>

---

### Syntax

```crystal
class MyStrPrinter
  def initialize(@greeting : String = "", @name : String = "")
  end

  def print
    puts "#{@greeting}, #{@name}."
  end
end

s = MyStrPrinter.new(name: "Taro", greeting: "Hello")
s.print  # => Hello, Taro.
```
@[2-3](インスタンス変数に直接代入される)
@[10](通常の変数名を使ったキーワード引数)

---

### Short one-argument syntax

<span style="font-size: 20px;"> Blocks and Procs - https://crystal-lang.org/docs/syntax_and_semantics/blocks_and_procs.html</span>

```crystal
puts ["abc", "def", "ghi"].map do |arg|
  arg.upcase.reverse
end
# => ["CBA", "FED", "IHG"]

# 上と同じ
puts ["abc", "def", "ghi"].map(&.upcase.reverse)
```
@[1-4](block引数が１つ & メソッド呼び出しのみ)
@[6-7](省略記法でも連続でメソッドが呼び出せる)


---

### Union types

<span style="font-size: 20px;">Union types - https://crystal-lang.org/docs/syntax_and_semantics/union_types.html</span>

```crystal
if 1 + 2 == 3
  a = 1
else
  a = "hello"
end

a      # : Int32 | String
a.to_s # => String
a + 1  # Error, because String#+(Int32) isn't defined
```
@[1-5](ifの分岐で型が異なる)
@[7](Int32 | String という型を持つ)
@[8](両方の型に存在するメソッドは呼べる)
@[9](片方にしか存在しないメソッドは呼べない)


---

# 😅

```
undefined method 'to_i' for Nil (compile-time type is (Array(Array(Bool | Float64 | Int32 | Int64 | MySQL::Types::Date | Slice(UInt8) | String | Time | Nil)) | Nil)) (did you mean 'to_s'?)
```

<span style="font-size: 1px;">　</span>

- 変数のスコープは小さくする |
- 変数の型を明示的に書く |

---

### Macros

<span style="font-size: 20px;">Macros - https://crystal-lang.org/docs/syntax_and_semantics/macros.html</span>

```crystal
class MacroSample
  macro define_print(name, type, default)
    property {{name.id}} : {{type}} = {{default}}

    def print
      puts {{name.id}}
    end
  end

  define_print(name: "hoge", type: String, default: "")
end

# compile時に生成されるコード
property(hoge : String = "")
def print
  puts(hoge)
end
```
@[2-8](propertyとメソッドのmacro)
@[10](macro呼び出し)
@[12-17](compile時に生成されるコード)

---

### その他

- Generics
- abstract class
- Concurrency
  - Fiber
  - Event loop
  - Channel

<span style="font-size: 40px;">Docs - https://crystal-lang.org/docs/</span>

---

## $ crystal tool

---

## $ crystal tool context

```
$ crystal tool context --cursor context.cr:4:1 context.cr
1 possible context found

| Expr    | Type   |
--------------------
| a       | String |
| b       | Int32  |
| puts(a) |  Nil   |
```

- カーソル位置のスコープが対象
- 変数やメソッドの型を表示

---

## $ crystal tool hierarchy

```
class Animal; end
class Cat < Animal; end
class Mike < Cat; end
```
```
$ crystal tool hierarchy hierarchy.cr -e Animal
- class Object (4 bytes)
  |
  +- class Reference (4 bytes)
     |
     +- class Animal (4 bytes)
        |
        +- class Cat (4 bytes)
           |
           +- class Mike (4 bytes)
```

- 継承関係を図で表示
- -e オプションでクラス名指定

---

## $ crystal tool format

```crystal
# before
def hoge; "hoge"; end

puts [1,2,   3  ]. map(&.   to_s)
```

```crystal
# after
def hoge
  "hoge"
end

puts [1, 2, 3].map(&.to_s)
```

- 空白と改行の整形

---

## $ crystal tool expand

```
$ crystal tool expand --cursor macro.cr:10:3 macro.cr
1 expansion found
expansion 1:
   define_getter(year, month, date)

~> def year
     @year
   end
   def month
     @month
   end
   def date
     @date
   end
```

- カーソル位置のMacrosを展開

---

## Library

---

<img src="assets/images/shards-2000-over.png" width="50%">

<span style="font-size: 60px;font-weight: 600;">2000 packages 🎉</span>

---

## shards

- rubyの`bundler`的存在
- `shards.yml`に設定記述
- `shards.lock`を生成
- githubのリポジトリ指定
  - branchやタグを指定可能

---

###### ライブラリをinstallするまでの流れ

```
$ crystal init app kemal_test
      create  kemal_test/.gitignore
      create  kemal_test/.editorconfig
      create  kemal_test/LICENSE
      create  kemal_test/README.md
      create  kemal_test/.travis.yml
      create  kemal_test/shard.yml
      create  kemal_test/src/kemal_test.cr
      create  kemal_test/src/kemal_test/version.cr
      create  kemal_test/spec/spec_helper.cr
      create  kemal_test/spec/kemal_test_spec.cr
Initialized empty Git repository in /path/to/kemal_test/.git/
$ cd kemal_test
$ vim shard.yml
$ cat shard.yml
name: kemal_test
version: 0.1.0

authors:
  - at-grandpa

targets:
  kemal_test:
    main: src/kemal_test.cr

crystal: 0.23.0

license: MIT

dependencies:
  kemal:
    github: kemalcr/kemal
    branch: master

$ shards install
Updating https://github.com/kemalcr/kemal.git
Updating https://github.com/luislavena/radix.git
Updating https://github.com/jeromegn/kilt.git
Installing kemal (master)
Installing radix (0.3.8)
Installing kilt (0.4.0)
$ ls -1 lib
kemal
kilt
radix

$ vim src/kemal_test.cr
$ cat src/kemal_test.cr
require "./kemal_test/*"
require "kemal"

get "/" do
  "Hello World!"
end

Kemal.run

$ crystal run src/kemal_test.cr
[development] Kemal is ready to lead at http://0.0.0.0:3000
```
@[1](`Crystal init`コマンドで雛形を生成)
@[2-12](雛形が生成される)
@[13]()
@[14](`shards.yml`を編集)
@[15]()
@[16-34]()
@[30-33](`kemal`を記述)
@[35](依存ライブラリをinstall)
@[36-41](install完了)
@[42-46](libディレクトリに存在)
@[47](ソースコードを編集)
@[48]()
@[49-57](`require`できる)
@[58-59](実行できる)

---

### ライブラリを紹介しているサイト


---

### AWESOME CRYSTAL

<img src="assets/images/awesome-crystal.png" width="60%">

<span style="font-size: 30px;">http://awesome-crystal.com/</span>

---

### CrystalShards

<img src="assets/images/crystalshards-xyz.png" width="60%">

<span style="font-size: 30px;">http://crystalshards.xyz/</span>

---

### 有名どころ

- [kemal](http://kemalcr.com/)
  - Sinatraライクな web framework
- [amber](http://www.ambercr.io/)
  - Railsライクな web framework
  - CLIツールもある
- [sidekiq.cr](http://www.mikeperham.com/2016/05/25/sidekiq-for-crystal/)
  - sidekiq作者が自ら制作

---

### 注目

- [graphql-crystal](https://github.com/ziprandom/graphql-crystal)
  - まだbeta版
- [scry](https://github.com/kofno/scry)
  - LSP対応の code analysis server

---

---

## Crystalの現状はどうなのか

---

#### Crystal 言語周辺の 2017年は？（妄想）

<img src="assets/images/qiita-crystal-2017.png" width="55%"></br>
<span style="font-size: 30px;">author: [@yahhonob](https://twitter.com/yahhonob)さん</span></br>
<span style="font-size: 20px;">http://qiita.com/yahhonob/items/a24dee215c70425196d3</span>

---

## Crystalの歴史

---

#### Initial commit

<img src="assets/images/initial-commit.png" width="80%">

###### 2011.06.21

<span style="font-size: 20px;">https://github.com/asterite/crystal/commit/50db22908a11b070c56524cc9bbd74332ad3a7c7</span>

---

#### First blog post

<img src="assets/images/manas-blog-hello-world.png" width="70%">

###### 2013.07.10

<span style="font-size: 20px;">https://crystal-lang.org/2013/07/10/hello-world.html</span>

---

#### The compiler written in Crystal can compile itself

<img src="assets/images/manas-blog-good-by-ruby-thursday.png" width="50%">

###### 2013.11.14

<span style="font-size: 20px;">https://crystal-lang.org/2013/11/14/good-bye-ruby-thursday.html</span>

---

#### First official release

<img src="assets/images/first-official-release.png" width="80%">

###### 2014.06.19

<span style="font-size: 20px;">https://github.com/crystal-lang/crystal/releases/tag/0.1.0</span>

---

#### Crystal reached 4000 stars on GitHub

#### 🌟 🌟 🌟 🌟

###### 2015.12.22

<span style="font-size: 20px;">https://github.com/crystal-lang/crystal/stargazers</span>

---

#### First Meetup in Argentina

<img src="assets/images/manas-blog-meet-up-in-buenos-aires.png" width="60%">

###### 2016.02.04

<span style="font-size: 20px;">https://www.meetup.com/Crystal-Language-Buenos-Aires/events/227938900/</span>

---

## Crystalの成長スピードは？

---

## Github Star history 📈

<span style="font-size: 30px;font-weight: 600;">http://www.timqian.com/star-history/</span>

- Star推移は言語の成長と相関がありそう
- 他の言語と比べてみる

---

<img src="assets/images/github-star-crystal.png" width="70%">

<span class="star-graph star-graph-crystal">Crystal</span>

---
<!-- .slide: data-background-transition="zoom" -->

<img src="assets/images/github-star-crystal-elixir.png" width="70%">

<span class="star-graph star-graph-elixir">Elixir, </span><span class="star-graph star-graph-crystal">Crystal</span>

---

<img src="assets/images/github-star-crystal-elixir-rust.png" width="70%">

<span class="star-graph star-graph-rust">Rust, </span><span class="star-graph star-graph-elixir">Elixir, </span><span class="star-graph star-graph-crystal">Crystal</span>

---

<img src="assets/images/github-star-crystal-elixir-rust-go.png" width="70%">

<span class="star-graph star-graph-rust">Rust, </span><span class="star-graph star-graph-elixir">Elixir, </span><span class="star-graph star-graph-go">Go, </span><span class="star-graph star-graph-crystal">Crystal</span>

---

<img src="assets/images/github-star-crystal-elixir-rust-go-swift.png" width="70%">

<span class="star-graph star-graph-rust">Rust, </span><span class="star-graph star-graph-elixir">Elixir, </span><span class="star-graph star-graph-go">Go, </span><span class="star-graph star-graph-swift">Swift, </span><span class="star-graph star-graph-crystal">Crystal</span>

---

- 新しい世代の中では１〜２年遅れていそう
- キラーアプリ/キラーライブラリがない |
  - kemal?
- 実用事例が少ない |
- 触る人は徐々に増え始めている印象 |
  - 海外の CodeCamp 開催頻度
  - blogのpost頻度
  - twitterの投稿頻度
  - 新規ライブラリの登場頻度
- 日本だとちょっと下火？ |

---

### なかなか突出できてないが
### 言語としては順当に成長している

---

## 1.0のリリースに向けて

---

###### Crystal new year resolutions for 2017: 1.0

<img src="assets/images/manas-blog-new-year-resolutions.png" width="80%">

<span style="font-size: 20px;">https://crystal-lang.org/2016/12/29/crystal-new-year-resolutions-for-2017-1-0.html</span>

---

### Parallelism

- 1.0に向けた一番大きな機能 |
- Threadクラスは既に存在しているが「Don't use this class」になっている |
- multithread-enabled Fibers を使用するためのWiki |
  - [Crystal-lang wiki - Threads support](https://github.com/crystal-lang/crystal/wiki/Threads-support)
  - 本番運用はNG、実験的ならOK
  - Schedulerがreentrant-safeではない
  - その対応をsingle-msqueueブランチで開発中
    - 最終コミットは３ヶ月前

---

### Windows support

- Windowsのサポートへの期待は大きい |
- Cross-platform desktop apps |
- WindowsサポートへのRoadmap |
  - [WIP - Windows #3582](https://github.com/crystal-lang/crystal/pull/3582)
- 直近でも議論が行われている |

---

### Type system

- 型推論のさらなるブラッシュアップ |
- Generics周りで再レビューが必要 |

---

### Incremental compilation

- Crystalはコンパイル時間が遅い印象がある |
  - RustやGoと比べられがち
  - RedditやGoogle-Groupでも話題に挙がる
- 特にMacros周りが遅い |
  - コンパイル時にコードを生成しているので
- v1.0にはmustではないという認識 |
  - 後回しになるかも

---

### Macros

- 1.0以降で Breaking-changes 入れたくない！ |
  - AST触ったりできるので
  - compile-processにフックできたりするので
  - 影響範囲大


---

### Syntax

- 1.0以降ではSyntaxは固定する |
- 今後は大きな変更はないと思うが、さらにしっかりとレビューした上で1.0を迎える |

---

### For newcomers

- 最近Webサイトが新しくなった |
- Crystal core team による Q&A session |
  - youtubeライブ配信
  - [録画したものはこちら](https://www.youtube.com/watch?v=E25AGpYyQw0)
- manページの追加 |
  - v0.23.0から

---

## Growing 😄

---

## まとめ

- 爆発的な普及ではないが、順当に成長してそう
- どこか大きな実用事例が欲しい
- Parallelismの完成度が一つの鍵だと思う
  - パフォーマンスと安定性
- 注目している人は確実に増えている
- 理解しやすさは参入障壁を下げそう
  - コンパイラを読める人も増えそう

---

### Let's Contribute to Crystal 💻


---

## Happy Crystalling 🎉

```
$ brew update
$ brew install crystal-lang
```
