awesome-java
============

A curated list of awesome Java frameworks, libraries and software. Inspired by [awesome-python](https://github.com/vinta/awesome-python).

カジュアルに Wiki 感覚で使えばいいです。コミット権限は欲しい人いたら言ってくれればあげます。#java-casual@freenode というところでやってます。文体とか、後で更新した人が勝ち、ぐらいのノリでいいと思います。

## Setup(Macの場合)

    $ brew tap caskroom/cask
    $ brew install brew-cask
    $ brew cask install java

[Eclipse Luna (4.4) ](https://www.eclipse.org/downloads/) をダウンロードする。

## JDBC

JDBC は Perl で言うところの DBI にあたる仕様です。バージョンが 4.1 が最新な気がしています(2014年7月時点)。

### Query logging

発行されたクエリをログに出したい、みたいなときにどうすればいいかという話。

#### Wrapper を使う

JDBC の Driver の interface を実装して、Wrapper として動作するものがいくつかある。
しかし Maven に登録されてて動作がまともにするものが少なかったりする。

今日試した感じだと、log4jdbc が唯一まともに動いたし、機能的には十分だと思う。ただし、PreparedStatement の実装が toString を実装してないので、普段から使ってるとデバッグするときだるかったりする。

log4jdbc は Maven Central に上がってないようにみえるが、実はあがってるので使えばよろしい。

    "jdbc:log4jdbc:h2:mem:test;DATABASE_TO_UPPER=FALSE"

とかすると、いいかんじにログが slf4j に出ます。

プログラマブルインターフェースもあって、

    conn = new net.sf.log4jdbc.ConnectionSpy(conn);

とかすると、明示的にラップした区間だけで、この機能を有効にすることができます。便利。

つまるところ、汎用的にどこでも使える手段を使いたいという場合じゃなければ、この log4jdbc を使う必要はなく、Driver のロギング機能を使えば良い。自分たちで運用するタイプの Web 屋さんの場合には、log4jdbc を使う場面はあまりなさそうである。

#### Driver の機能を使う

MySQL Connector/J にせよ H2 にせよ、実は Driver 本体にロギング機能がついてるので、気にせずにそれを使えばよろしい。

H2 については、[Trace Options](http://www.h2database.com/html/features.html#trace_options) を見れば良い。

    jdbc:h2:mem:test;TRACE_LEVEL_FIlE=4;TRACE_LEVEL_SYSTEM_OUT=3

とかすれば、いい感じにログがでる!

Connector/J については[5.1 Driver/Datasource Class Names, URL Syntax and Configuration Properties for Connector/J](http://dev.mysql.com/doc/connector-j/en/connector-j-reference-configuration-properties.html) を参照の上、logger オプションを指定すればよい。

## Logger

Logger は java.util.Logging と log4j と log4j2 と slf4j+logback という4つの派閥があって、非常にややこしい。

が、結局のところ API とバックエンドの組み合わせの問題になっていて、それぞれがそれぞれのデータをバックエンドとして受け取れるみたいなことになってるので、とにかくいっぱい jar をググって出てきた通りにうけとってぶっこんでいけばいい。ということな気がしている。

## CLI

 * [jpbetz/cli-util](https://github.com/jpbetz/cli-util)
  * サブコマンドが実行できるのが便利
* [picocli](http://picocli.info) アノテーションに基づくコマンドドライン解釈。オプションとパラメーター両方、強い型付け。サブコマンド実行可能。使い方のヘルプはANSI色使用可能。ソースファイルが一つのため、依存関係を避けたい方はソースとしてインクルード可能。
