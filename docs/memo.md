技術メモです。技術的なことはここへ書くようにします。日誌に散らばっていると再利用できません。

## OBS

- フォントが小さいので大きくして起動します。`QT_SCALE_FACTOR=2 obs`
- Nvidiaのドライバーがどうのと出るので、使わない設定にしました。たぶんGPUを使ってないので重くなりますが。

## Rails
- create_list - factorybot

### ネストしたvalidate
特定の条件だけで発動するvalidation + 条件。`with_options: if`内で`if`を使うと、中のif条件が優先して実行されるため、こう書く必要があります。
```ruby
validates :term_date, date: { after: proc { Time.zone.now } }, if: proc { |p| p.term_date? && p.sellable?  }
```
### `includes`
N+1問題を回避するために用います。

### 何のメソッドがわからないとき
- Emacsだと`robe-doc`を使うと非常に便利です。すでにあるローカルにあるドキュメントを活用しましょう。

## Ruby

### `group_by`
`Enumerable#group_by`
>ブロックを評価した結果をキー、対応する要素の配列を値とするハッシュを返します。
QueryMethodの`where`で取った値をハッシュにして、後で使いまわせます。N+1問題の回避に使えます。QueryMethodぽい名前ですが無関係です。

### `index_by`
viewで何かモデルに関することをループさせないといけないときに役立ちます。モデルを一度にハッシュとして取ることで、パフォーマンスを改善できます。

### インスタンスメソッドを調査する
`String.instance_methods(false).sort`

`false`によってクラスの継承メソッドを表示しないため、クラス単体を調べるのに役立ちます。

### トップレベルで実行できる理由
クラスがなくトップレベルで定義されたメソッドのレシーバーは`Object`クラスです。クラスの中にないトップレベルメソッドでさまざまなことが行えるのは、`Object`のおかげです。`ruby -e 'p Kernel.private_instance_methods.sort'`でチェックできます。
- `puts` がレシーバーなしで呼び出せるのは、`Object`クラスがputsのある`Kernel`クラスをincludeしているからです。
- `.to_d` - BigDecimalに変換する。
- `index` - 配列を検索して添字を返す。

## Capybara
- 属性で探すとき - `find('[data-test="作成"]').click`
- テストがあまりに大きいとき、ファイル記述子の上限でエラーになることがあります。上限を変更します。 https://qiita.com/iorionda/items/4d301e5d5b115a018ba8
## JavaScript

### 配列
- 配列を処理するときはやりたいことに応じて関数を選択します。するとやりたいことが明確になるのでわかりやすくもなります。除去したときは`filter()`、新しい配列を作るときは`map()`という感じです。

### コールバック関数
- コールバック関数: 他の関数に引数として渡す関数のことです。
<a href="https://sbfl.net/blog/2019/02/08/javascript-callback-func/">JavaScriptの「コールバック関数」とは一体なんなのか</a>

### Eslint無視の方法
```json
"rules": {
    "comma-dangle": "error",
    "require-yield": "error",
    "no-unused-vars": "off",
    "no-undef": "off",
},
```
などと書きます。

### クラスを使わずにモジュール分割する
変数内に入れます。
```js
var Msg = (function() {
  function test() {
  }...
}
Msg.test()
```
### 命名の意味
- `_`で始まるのはプライベートメソッドです。言語によっては未使用変数ということもあります。

### NodeList
- NodeList - https://developer.mozilla.org/ja/docs/Web/API/NodeList
- text node: タグに囲まれたもの。`<p>これ</p>。`
- element node: `<p>や、</p>`
- DOMツリー: ブラウザがアクセスしてHTMLを解析すると文書の内容を表すオブジェクトのツリー構造が構築されます。これがDOMツリーです。DOMツリーを形成する1つ1つのオブジェクトがノードです。さらに子のオブジェクトを持っている可能性があります。
https://qiita.com/KDE_SPACE/items/e21bb31dd4d9c162c4a6

## テスト
テスト駆動開発で進めます。
- 最初にバグを再現するコードを書きます。これは失敗する、などと書いておきます。
- バグ修正/リファクタリング、テスト/プロダクトコード修正を混ぜません。わけわからなくなるからです。
- 日本語で検証する内容を書きます。もちろん、意図を明確に伝えられる英語力があれば問題ありませんが。単にAdd, Fixとかレベルの語彙しか使えないので、実用的ではありません。
- テストのネスト、レベル感を合わせます。
- 例の値であることを示します。

## Emacs

### 改善メモ
- undo履歴を保持しないときがあります。3つくらいしか戻れないときがあり、この問題は未着手です。
- `markdown-mode`でHelm-M-xが作動しません。

### key
- `C-x C-;` - 行にコメントが付けられます。
- `<F1> b` - キーバインドの一覧を表示します。
### Mac
- Macでは`query-replace`(`M-%`)が奪われて押せないので、`query-replace-regexp`(`C-M-%`)します。

### Helm
- Helmのサーチ中のものをコピーするときには、`helm-copy-to-buffer`(`C-c C-i`)します。あるいは`helm-kill-seletion-and-quit`(`C-u C-c C-k`)も使えます。

### Projectile
- プロジェクト全体のファイル名検索は`projectile-find-file`(`C-c p f`)が便利です。
- Modelなど、ディレクトリが絞り込めているときは`projectile-rails`で検索が便利です。ただキーバインドがあまりに多すぎるので、使用頻度が低いものは`projectile-find-file`したほうがいいでしょう。
- 内容を検索するときには`helm-git-grep`を使います。
- `projectile-switch-project`(`C-c p p`) - プロジェクトを切り替えます。
- `projectile-ag`(`C-c p s s`) - プロジェクトをag検索します。検索結果をバッファで得たいときに。
- `projectile-kill-buffers`(`C-c p k`) - プロジェクトのバッファをキルします。
- `projectile-multi-occur`(`C-c p o`) - プロジェクトで`occur`します。
- `projectile-find-dir`(`C-c p d`) - プロジェクトのルートディレクトリを開きます。
- `projectile-toggle-between-implementation-and-test`(`C-c p t`) - テストとプロダクトコードを切り替えます。

### robe
- `robe-doc`(`C-c C-d`)でRubyメソッドを調べられます。gemがあるプロジェクトのGemfileで`pry`,`pry-doc`をインストールして実行するとpryが起動して、以後使えるようになります。これは補完の`company.el`と連携させているため、pryを起動しないことにはgemの補完は表示されません。

### dired
- diredで一気に置換したいとき、`wdired-change-to-wdired-mode`で編集モードにして`C-c C-c`で実行します。

### spring rspec
いつのまにかEmacsでspringが動かなくなっていました。コマンドでは`spring rspec`などで動かせるが、Emacsではふつうの`bundle exec ...`になっていました。前は早くできていたはず。ネットの記事を参考にします。

>emacsのrspec-modeで、上記のようにしてspringを使うと、自動ではspringがONにならない。 これはtmpにpidファイルが作られないため。 強制的にspringを使うには、次のように設定する。
```
(require 'rspec-mode)
(defun rspec-spring-p ()
  (and rspec-use-spring-when-possible
       (stringp (executable-find "spring"))))
```
springを自動で検出してくれるらしいのですが(何より今までできてた)。
- `spring status`でspringが動いてるかチェックする。動いてなかったら`spring rspec`する。

## vterm
`vterm-copy-mode`→`C-c C-t`

## MySQL

### Tranaction Errorになったとき
途中でテストの実行を中断したときに起こることがあります。
`mysql -u root -p`
`SHOW ENGINE INNODB STATUS;`してTRANSACTIONSの部分を確認する。
Thread IDを調べて`kill id`

## fish

### 基本設定

```shell
fish_config
```
でブラウザで設定できます。

### パッケージシステム
fishにパッケージシステムomfをインストールします。
```shell
sudo apt install omf
curl -L https://get.oh-my.fish > install
fish install --path=~/.local/share/omf --config=~/.config/omf
```
色合いがEmacsに合っていなくて見づらいのでテーマをインストールします。
```shell
set -g theme_powerline_fonts no
```
再起動すると反映されます。

## 条文リンク
<a href="https://qiita.com/h-naito/items/92be6487900a1398866f">NodeListとは？ - Qiita</a>
なぜだ。aタグをどこで付与してる?
- `renderResume`
- `replaceTextWithAutoLink` で3条文タイプの変換(build_系を呼び出す)を行う。
- `buildGlobalLawArticleForContinuum`, `buildGlobalLawArticleForSingle`
- 複数、単体で正規表現を用いて。`generateRegExpForGlobalLawArticle`ここを変えるとglobalLawにマッチしなくて、全部基本条文になったりする。datatypeをセットする。
- `replaceSingleWithLink` aタグをつける。replace recursiveをラップしてる。
- `replaceRecursive`共通して使われている。ここでようやく値をreplaceする。nodeListでループする。btn_refがあったりするとlawArticle用data属性がつく。

build系は`buildAutoLinkNode`を使ってaリンクを作る。第5引数にmatchしたものが入り、<a>match</a>になる。置換はここでしているとわかるが、その目標はどうやってサーチしてるか。

`replaceTextWithAutoLink`の`law_articles`の変換をオフにすると後半2つはリンクさえなくなることを確認した。

点つなぎがうまくいっていないときの挙動。点つなぎが作動せず、singleのみが働いてglobalLawArticleがつく。後ろの条はマッチしないので基本条文になる。

- 木構造を再帰で処理すると、単に順にたどるだけになるのか。lintとかとは違う。nodeのときはスルーする。
- 引数で分岐する共通関数が複数あるからややこしい。
- 走査は複数回行われる。複数 → 単数 → 基本
