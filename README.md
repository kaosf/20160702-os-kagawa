% tokushima.rbの紹介とItamaeを使ってみた話
% ka ([kaosfield](http://www.kaosfield.net))
% 2016-07-02

# はじめに

このスライドは公開されています

[https://kaosf.github.io/20160702-os-kagawa](https://kaosf.github.io/20160702-os-kagawa)

Repository: [kaosf/20160702-os-kagawa - GitHub](https://github.com/kaosf/20160702-os-kagawa)

もう一度見たい内容がある場合は後から参照できます

ゆったりした気持ちで聞いてください

# tokushima.rbとは

地域.rbの徳島版です

毎月最終日曜日にねすとラボという場所で開催しています

申し込みも特に必要ではありません

途中参加・途中休憩・途中退場も完全自由なのでお気軽にお越しください

# tokushima.rbとは

Facebookページ [https://www.facebook.com/groups/tokushima.rb/](https://www.facebook.com/groups/tokushima.rb/)

Conpass [http://tokushimarb.connpass.com/](http://tokushimarb.connpass.com/)

今までの発表やハンズオンの資料は全て公開しています

tokushima.rb - github.io [http://tokushimarb.github.io/](http://tokushimarb.github.io/)

# なにをしているの？

主にもくもく会です

ときどき発表もあります

# 今までの発表

※スライドがあったものだけの紹介になります

<ul>
<li>[RSpec の便利機能と SHIRASAGI に Database Cleaner を導入した話](https://sunny4381.github.io/remark.js/index.html?/slides/2015-03-29/rspec-extension.md)
</li>
<li>[Ruby入門](http://kaosf.github.io/20140831-tokushimarb-slide) by [ka](http://www.kaosfield.net)
</li>
<li>[Ruby超入門](http://kaosf.github.io/20140928-tokushimarb-slide) by [ka](http://www.kaosfield.net)
</li>
<li>[method_missing](http://kaosf.github.io/20141026-tokushimarb-slide) by [ka](http://www.kaosfield.net)
</li>
<li>[Rubyを使ったスマホアプリのUIテスト](https://www.slideshare.net/secret/d0MkzQdiT8cQIK) by [辰濱](https://www.facebook.com/kenichi.tatsuhama)
</li>
<li>[ElixirとPhoenixの紹介](http://kaosf.github.io/20150830-tokushimarb-slide) by [ka](http://www.kaosfield.net)
</li>
<li>[C#er から見た Turbolinks 3](http://www.slideshare.net/dany1468/cer-turbolinks-3) by [dany1468](https://twitter.com/dany1468)
</li>
</ul>

# 発表お待ちしてます

というか面白いRubyネタの発表お待ちしております

「○○使ってみた」とかレベルでも大歓迎

何か罠にハマった経験談はありがたがられます

今個人的に聞きたいのはC拡張の話や Ruby 3.0 の話

# こんな人は是非お越し下さい

Ruby入門したい人

Rails使ってみたけど○○がよく分からない…

gemを作ってみたいんだけどどうすればいいの？

# 次回は7月31日

Facebook [https://www.facebook.com/events/610716995754420/](https://www.facebook.com/events/610716995754420/)

Connpass [http://tokushimarb.connpass.com/event/35019/](http://tokushimarb.connpass.com/event/35019/)

遠路はるばるになる方が多いと思いますがもし余裕があって来ていただければ大歓迎です

# Itamaeの話

(主に)サーバのprovisioningをするツール

もうちょっと砕いて言うとサーバにパッケージをインストールしたり設定ファイルを書き換えたりしてくれるツール

2015年にはもっと流行ってました

今はもう皆普通に使ってる感じです

# 使ってみた感想的な話

入門記事や解説記事が大体2015年の投稿でかなり大量に出回っているので調べて実践してみるにあたってはとても楽でした

大体4日くらいチマチマと練習して昨日本番環境を実際に作ってみました

ベストプラクティス的なものを模索しながら書いてたので何度も何度も書き直しました

今までシェルスクリプトをゴリゴリ書いていたのに比べると良くなったところの方がやっぱり多いです

紹介出来るだけ紹介していきます

# 使い始める

```sh
gem install itamae

cat <<EOF > recipe.rb
package 'nginx'
service 'nginx' do
  action [:enable, :start]
end
EOF

itamae ssh --host 123.456.78.90 recipe.rb
```

これだけでNginxパッケージが動くマシンになりました

# 環境を選ばない

びっくりしたことにArch Linuxのサーバにこの設定でNginxがインストールされました

Itamaeが [specinfra](https://github.com/mizzy/specinfra) というgemを使ってこの辺の面倒は見てもらっているようです

以下 `itamae.gemspec` より抜粋

```ruby
  spec.add_runtime_dependency "specinfra", [">= 2.37.0", "< 3.0.0"]
```

# 前提

当然動く前提はあります

<ul>
<li>マシンにSSHログイン可能である
</li>
<li>sudo出来る
</li>
</ul>

# 前提

Vagrantを使って練習するときはsudoがパスワード無しで実行出来ます

本番環境で実際にやる際にはsudoのためのパスワードを聞いてくれます

面倒くさければ

```sh
sudo visudo
```

で `/etc/sudoers` の編集を開始して末尾に

```
yourname ALL=(ALL) NOPASSWD: ALL
```

という一行を追加して一時的にsudoがパスワード無しでも実行出来るように変更しておけばいいんじゃないでしょうか

SSHのパスワードログインを禁止にするなどして気をつけてからやりましょう

# rootユーザでログイン出来るなら

多分褒められた状態では無いと思いますが

```sh
itamae ssh --host 123.456.78.90 recipe.rb -u root
```

`-u` でユーザを指定出来ます

# 今日話すこと

今更改めて「Itamaeとは何か」「何が特徴か」を解説したりはしません

スライドの後の方で私が実際にこの一週間ほどで何度も何度も参照した記事を紹介しています

それらに目を通して自分で手を動かすのが一番理解というゴールに近い道でしょう

なので私が「使ってみてしんどかったこと」「ハマった罠」「『便利だ…』と感動したこと」などをほぼノンジャンルでまくしたてる形にしたいと思います

# Rubyのインストール

rbenvを使ってインストールします

[k0kubun/itamae-plugin-recipe-rbenv: Itamae plugin to install ruby with rbenv](https://github.com/k0kubun/itamae-plugin-recipe-rbenv)

Bashの人は`.bash_profile`に  
Zshの人は`.zshenv`に

```sh
export RBENV_ROOT=/usr/local/rbenv
export PATH="${RBENV_ROOT}/bin:$PATH"
eval "$(rbenv init -)"
```

を付け加えなければなりません

# レシピ

`recipe.rb`として以下のファイルを用意

```ruby
include_recipe 'rbenv::system'

user node[:server][:user]

execute "add rbenv path" do
  command <<-EOH
cat <<EOS >> /home/#{node[:server][:user]}/.bash_profile
export RBENV_ROOT=/usr/local/rbenv
export PATH="\\${RBENV_ROOT}/bin:\\$PATH"
eval "\\$(rbenv init -)"
EOS
EOH

  not_if "grep -q rbenv /home/#{node[:server][:user]}/.bash_profile"
end
```

# マシンごとの個別設定的なもの

`node.yml` として以下のようなファイルを用意

```yml
server:
  user: myname

rbenv:
  global: 2.3.1
  versions:
    - 2.3.1
  scheme: https
  cache: true
ruby-build:
  revision: c593baacd271f8807cb6ecd16b0bcb70491327a8
rbenv-default-gems:
  default-gems:
    - bundler 1.12.5
  install: true
```

# 実行コマンド

```sh
itamae ssh --host 123.456.78.90 recipe.rb -y node.yml
```

これでOK

# これが面倒くさい

```ruby
execute "add rbenv path" do
  command <<-EOH
cat <<EOS >> /home/#{node[:server][:user]}/.bash_profile
export RBENV_ROOT=/usr/local/rbenv
export PATH="\\${RBENV_ROOT}/bin:\\$PATH"
eval "\\$(rbenv init -)"
EOS
EOH
```

ナニコレって感じですよね

もう二度と考えたくないです

今後はずっと使いまわします

#

ちなみにヒアドキュメントにせず

```ruby
  command "echo \"foooooo\" >> /home/#{node[:server][:user]}/.bash_profile; ... "
```

としても面倒くささはあまり解消されません

# 自作レシピgem？

もしかしたらこういうのをgemにしてレシピ公開するといいのかも…？

# node.yml for rbenv例

```yml
server:
  user: myname

rbenv:
  global: 2.3.1
  versions:
    - 2.3.1
  scheme: https
  cache: true
ruby-build:
  revision: c593baacd271f8807cb6ecd16b0bcb70491327a8
rbenv-default-gems:
  default-gems:
    - bundler 1.12.5
  install: true
```

詳しくはitamae-plugin-recipe-rbenvのREADMEを読むこと

# node.yml for rbenv例

レシピ内で

```ruby
node[:server][:user]
```

などと参照していた情報の出所はこのファイルの内容になります

さっきの例ですと `"myname"`

# packageをインストール

`yum` ないし `apt` (※今はもう `apt-get` は古い) ないし `brew` ないし `yaourt` などでインストールするアレ

# package

```ruby
package 'nginx'
```

最初にも例を出した通りこんな感じでパッケージのインストールが出来ます

# yumのgroupinstallは？

```ruby
package 'Development Tools'
```

これではダメでした

特にこれといって

```ruby
package 'Development Tools'
  awesome_option 'for groupinstall'
end
```

みたいなことも出来ず

# どうしたか

```ruby
execute 'sudo yum -y groupinstall "Development Tools"'
```

`execute` で任意のコマンドが実行できるのでそれでやれば良い

OS依存？そもそも `Development Tools` というパッケージを  
インストールしようとすること自体がOS依存なのだ！と割り切る

参考: [itamae-lesson/recipe.rb at master · hsbt/itamae-lesson](https://github.com/hsbt/itamae-lesson/blob/master/recipe.rb)

# git

アプリケーションサーバ用のリポジトリをBitbucketのprivate repositoryから取ってこようとしました

```ruby
git "/home/user/app" do
  repository "git@bitbucket.org:kaosf/myapp.git"
end
```

初めてだったのでSSH認証完全に通ってて手動で`git clone`が問題なく出来る状態にしてから試したのですが…

# gitが動かない

何故か止まってしまう

cloneされるディレクトリを見てみると

```sh
ls -l ~
```

```
# (前略)
drwxr-xr-x 3 root root 17 Jul  1 00:00 app
# (後略)
```

所有者がrootに！？

# execute？

```ruby
execute "git clone git@bitbucket.org:kaosf/myapp.git /home/user/myapp"
```

先ほどのgroupinstallみたいに上手くいくか？

と思ったのですがダメでした…

もちろん `-u` オプションの指定を間違えているというようなこともなく…

# gitだけは諦めた

手動でcloneしてその後のライブラリインストールなどの処理は手動でやるように  
結局シェルスクリプト組んだ…

何か知ってる人居たら教えて下さい

# gitが動く例

公開リポジトリからSSH認証とか無しで取ってくるのは普通に出来ます

自分のプロジェクトが依存するOSSのソースコードを取ってきて野良ビルドするときなどは有用なはずです

```ruby
git '/home/user/conoha' do
  repository 'git://github.com/kaosf/conoha.git'
end
```

# itamae-plugin-recipe-rbenvの問題

システムワイドにRubyをrbenvでインストールするのは簡単でした

ユーザローカルにインストールしようとしたときに問題が発生しました

```ruby
include_recipe 'rbenv::user'
```

↑こういう感じに(READMEにある通り)

`default-gems` というファイルの所有者が `root` になっていてエラーを吐きます

正直Bundlerは無いと生きていけないのでdefault-gemsの設定は必須と言えます

# 関連するIssueとPull Request

<ul>
<li>[fix owner for a user by iyuuya · Pull Request #13 · k0kubun/itamae-plugin-recipe-rbenv](https://github.com/k0kubun/itamae-plugin-recipe-rbenv/pull/13)
</li>
<li>[default-gemsファイルの生成に失敗する · Issue #14 · k0kubun/itamae-plugin-recipe-rbenv](https://github.com/k0kubun/itamae-plugin-recipe-rbenv/issues/14)
</li>
</ul>

# forkを使う

本家のgemの方がエラーを吐く状態ですが修正案が一応見つかりました

慌てずGemfileを用いて

```ruby
gem 'itamae'
gem 'itamae-plugin-recipe-rbenv',
  git: 'git://github.com/iyuuya/itamae-plugin-recipe-rbenv',
  branch: 'fix-default-gem'
```

# forkを使う

```sh
bundle install
```

もしくは

```sh
bundle install --path vendor/bundle
```

して

```sh
bundle exec itamae ...
```

として使っていきます

# 私の環境のGemfileの差分

`Gemfile`

```diff
- gem 'itamae-plugin-recipe-rbenv', '0.6.2'
+ gem 'itamae-plugin-recipe-rbenv',
+   git: 'git://github.com/iyuuya/itamae-plugin-recipe-rbenv',
+   branch: 'fix-default-gem'
```

# システムワイドでいいじゃん

しかしこういうしょうもないことをItamaeを使っているせいで考えるのは正直しんどいです

なのでここは割り切って

<ol>
<li>rbenvはもうシステムワイド一択
</li>
<li>Bundlerを必ず使って
</li>
```sh
bundle install --path vendor/bundle`
bundle exec rake
```
</ol>

するようにしましょう

私はそうしました

# Node.js

`node.yml`の例

```yml
nodebrew:
  versions:
    v5.9.0:
      - gulp
  use: v5.9.0
```

さらに詳しくはitamae-plugin-recipe-nodebrewのREADMEを参照

[https://github.com/toihrk/itamae-plugin-recipe-nodebrew#nodejson](https://github.com/toihrk/itamae-plugin-recipe-nodebrew#nodejson)

# YAMLで空の配列

[itamae-plugin-recipe-nodebrew/system.rb at master · toihrk/itamae-plugin-recipe-nodebrew](https://github.com/toihrk/itamae-plugin-recipe-nodebrew/blob/master/lib/itamae/plugin/recipe/nodebrew/system.rb#L20)

ここに

```ruby
(node['nodebrew']['versions'] || {}).each do |node_version, npms|
  node_install node_version do
    profile_path NODEBREW_PROFILE_PATH
  end
  npms.each do |npm|
    # ...
  end
end
```

というコードがあります

# YAMLで空の配列

なので一つもnpmでパッケージをインストールしたくない場合は先程のymlファイルで空の配列を設定する必要があります

もしもnullだとRubyで

```ruby
nil.each do |npm|
end
```

を実行することになってしまいここでプログラムが落ちるためです

# YAMLで空の配列

<ul>
<li>[YAML で、空の配列 - My Octopress Blog](http://chyk.github.io/blog/2013/06/12/yaml-de,-kong-falsepei-lie/)
</li>
<li>[YAMLで空の配列を表現する - Qiita](http://qiita.com/kiaidangoo/items/3b619bca5cf912f6c339)
</li>
</ul>

リンク先にも書いてますが

```yml
versions:
  v5.9.0:
```

だけだと NULL になってしまうのです(これにハマった)

# YAMLで空の配列

```yml
versions:
  v5.9.0: []
```

正解はこう

# templateとremote_file

設定ファイルを用意しておいたり雛形を用意しておいたり出来ます

remote_fileの方は作ってあるファイルをそのまま設置するだけですがtemplateの方はERBになっています

設定ファイル内にユーザ名を埋め込みたいが環境によって変わる…というような場合に

```erb
awesome_configuration:
  host: 192.168.10.1
  port: 12345
  user: <%= node[:server][:user] %>
```

みたいなことが出来ます

レシピから変数を渡して `@var` と参照することも出来ます

# templateのお陰で救われたこと

最初はtemplateを設置してしまうとデフォルトで用意されていた設定をもしかしたら図らずしも上書きによって消滅させてしまうかもしれない…という不安がありました

しかしItamaeは賢くてもしも既にファイルが存在した場合にはそいつとの差分を見せてくれます

```diff
- XXXX=1234
+ XXXX=2345
  YYYY
- YYYY1
- YYYY2
+ ZZZZ1
+ ZZZZ2
```

こんな感じ(実際はマイナスが<span style="color:red">赤</span>，プラスが<span style="color:green">緑</span>になります)

なので妙に赤が多い…と思ったらよく調査してみればいいのです

# templateのお陰で救われたこと

これでもう

```sh
cat /etc/foo.conf | \
  sed 's/^#conf1=default/conf1=myname/' | \
  sed 's/^conf2=enable/conf2=disable/' | \
  > /tmp/foo.conf
\mv /etc/foo.conf /etc/foo.conf.orig
\mv /tmp/foo.conf /etc/foo.conf
```

とかを大量に書かなくてもいい…

# 参考リンク

あとで言うと言ってたやつです

<ul>
<li>[Itamaeチートシート - Qiita](http://qiita.com/fukuiretu/items/170aa956731f2ffb5715)
</li>
<li>[Itamae入門した ~ Vagrantのサーバーにnginx入れるまで - しめ鯖日記](http://llcc.hatenablog.com/entry/2015/07/05/160822)
</li>
<li>[Best Practice · itamae-kitchen/itamae Wiki](https://github.com/itamae-kitchen/itamae/wiki/Best-Practice)
</li>
<li>[Itamaeレシピ集 - Ruby/nginx/MySQL/Redis/etc - 珈琲駆動開発](http://totutotu.hatenablog.com/entry/2015/10/30/Itamae%E3%83%AC%E3%82%B7%E3%83%94%E9%9B%86_-_Ruby/nginx/MySQL/Redis/etc)
</li>
</ul>

# 他に伝えたいこと要約 + 感想まとめ

<ul>
<li>ベストプラクティスはちゃんと見てディレクトリ構成を整えよう</li>
<li>意外と欲しいレシピがありそうで無い</li>
<li>結局普通にコマンドで作業出来ないとダメ(サーバ構築未経験者が最初からitamaeは無理)</li>
<li>OSのことちゃんと分かってないとダメ</li>
<li>シェルスクリプトから逃れられると思うな(結局executeを駆使することが多い)</li>
</ul>

# systemd関連

<ul>
<li>[Systemd入門(4) - serviceタイプUnitの設定ファイル - めもめも](http://enakai00.hatenablog.com/entry/20130917/1379374797)
</li>
<li>[Systemdを使ってさくっと自作コマンドをサービス化してみる - Qiita](http://qiita.com/DQNEO/items/0b5d0bc5d3cf407cb7ff)
</li>
<li>[Itamae で firewalld を操作する itamae-plugin-resource-firewalld - Qiita](http://qiita.com/gongo/items/ae26632416f864a732cf)
</li>
</ul>

自分はこの辺がまだ正直言って自信が無いのでちゃんと `nantoka.service` を作って

```sh
systemctl start nantoka.service
```

を正しく出来るようにしたいです

# 自作conoha gemの話

今回マシンを作ったり壊したりして実験していたのですが

```sh
vagrant up
```

よりもConoHaのVPSを作って壊してする方が速いので自作のConoHa VPS CLI clientを使っていました

そろそろVersion 1.0.0にしたいのですがまだ内部の構造が汚すぎてそんな状態でメジャーバージョン1にしたくないのでまだ0.8.1という状態

# conoha gem使い方簡単紹介

```sh
conoha create centos72 g-1gb # CentOS 7.2 の RAM 1GB のマシン作成
# UUID が表示される

conoha ipaddress $UUID
# IPアドレスが表示される

ssh root@$IPADDRESS

conoha rebuild $UUID ubuntu
# Ubuntu を再インストール

conoha shutdown $UUID
# シャットダウン

conoha delete $UUID
# マシン削除
```

# conoha gem使い方簡単紹介

1台だけ「これ！」と決めて使うのであれば `conoharant` コマンド  
(※言わずもがなvagrantとconohaを合わせた造語)  
があります

```sh
mkdir a; cd a
conoharant init
vi Conoharantfile # 使いたいOSを選んだり設定を弄る(デフォルトはCentOS 7.2のメモリ1GB)

conoharant up
conoharant ssh root
conoharant ssh

conoharant dump
# イメージを保存しておいてマシンを削除する(50GBまではお金がかからない！)
conoharant restore
# 保存しておいたイメージからマシンを復元する
```

# conoha gem宣伝

1時間1.3円で使えるのだからネットさえ確保出来ればかなり快適

※私はGMOの回し者ではありません

# まとめ

<ul>
<li>tokushima.rbやってます他にも色々話したいことあります皆来てね</li>
<li>Itamae便利だけどシェルスクリプトも分からないとやっぱりダメ</li>
<li>conohaってgem作っててこれのお陰で貧弱PCでも開発出来ます良かったらGitHubでStar下さい</li>
</ul>

# おしまい
