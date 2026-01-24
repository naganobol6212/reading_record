本を開きながらのタイピングが大変だったので、卓上ブックスタンドを購入し本日はじめて使用。
めちゃくちゃ便利すぎて感動しました :sparkles: 
###### ■ ＜＜今日取り組んだこと＞＞
- 『ログの教科書』を読みながら、grep を使ったログ検索の基本を学習
- 学んだ内容を、自分の卒制アプリ「MabaTalk」 の log/development.log で実践
- Rails のログ出力（Rails.logger.info）と grep を組み合わせて、「起きていることをログから読み取る」体験をした

* * *
###### 🔍＜＜grepとは何か＞＞
- 「ログの中から、欲しい行だけを探し出す道具」
- ログは量が多すぎて、そのまま読むのはほぼ無理。
だから 検索前提で使う。
* * *
###### 🛠＜＜grepの基本的な使い方＞＞
✅ 特定の言葉を含む行だけを見る
`grep Error sample.log`
- sample.log の中から Error を含む行だけ を表示する
* * * 
🔗複数条件で絞り込む（AND検索）
`grep Error sample.log | grep Message`

意味は
- まず Error を含む行を出す
- その中から Message も含む行だけにする
👉 だんだん狭くしていく
* * *
🚫 逆に「含まない行」を出す
`grep -v Error sample.log`
- Error を含む行を 消して
- それ以外を表示する
* * * 
🔤 大文字・小文字を気にしない
`grep -i error sample.log | grep -i message`
* * *
###### 🚀＜＜MabaTalkでの実践＞＞
###### ① まず「ログに目印」を入れる
- grepは**ログに書いてある文字しか探せない**
- だから Rails のコードにこれを追加した
`Rails.logger.info "[FlowItems#create] start"`
- 意味は「FlowItemsController の create が呼ばれたよ」
という目印をログに残す。

###### ② grepでその目印を探す
`grep "\[FlowItems#create\]" log/development.log`
🤔 この書き方の意味
- [ ] は特別な文字なので、そのままだと誤解される
- \[ \] は
👉「ただの [ ] として探してね」という指定
- "" は文字をまとめるため
* * * 
###### ③ 実際に出てきたログ
```
[FlowItems#create] start
[FlowItems#create] validation_failed errors=Nameを入力してください
```
ここから分かること👇
- create はちゃんと呼ばれている
- でも name が空で保存に失敗している
- だから画面が戻ってきている
* * *
###### 🎯 失敗したときだけを見たい
`grep "\[FlowItems#create\]" log/development.log | grep "validation_failed"`

- 意味は「FlowItems#create のログの中から」
- 失敗した行だけを抜き出す
👉 「原因調査モード」
* * *
######📍 Rails.logger.info をどこに書くかの違い
```
def create
  Rails.logger.info "[FlowItems#create] start"
  ...
end
```
`Rails.logger.info "[FlowItems#create] start"`を
**createの真下に書くと**
- 「create が呼ばれた瞬間に出る」
- 「ここまでは来てる？」の確認用

**もっと下のコードのところに書くと**
- そこまで処理が進んだときだけ出る
- どこで止まったかが分かる

👉 ログは
**「通過チェックポイント」**として置く
* * *
😲 grep -v "manifest.json" で大量に出た理由
`grep -v "manifest.json" log/development.log`
意味は
- manifest.json を含まない行を 全部出す

Rails のログはほぼそれ以外なので、
👉 大量に表示されて正解
エラーではない。
* * *
###### 🧩＜＜まとめ＞＞
- ログを「なんとなく眺める」から、grepで自分で絞りこむ感覚を少し覚えてきました :muscle: 
- 「AIが出したログ」ではなく、自分で意味を理解して追う意識ができてきたのではないかと思います〜！！
