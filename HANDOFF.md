# 期待値オークション 引き継ぎメモ

- リポジトリ：asumax1128/evauction（index.html 1枚）。ソースは /home/claude/auction/src（lots.js・app.js・ui.js・base.css・extra.css）を build.sh で結合。
- Firebase：asumath-games、`games/auction/rooms/<4桁>`（meta・players・state・bids・hint・rise）。ルールは database.rules.json（マスター：/home/claude/bluff-online/database.rules.json）。
- 進行はホストの端末が担当（ひとり練習も同じ処理）。state にゲーム全体を保存：gid, mode(sealed/rising), phase(bid/rise/show/final), round, n, seats[[uid,name,team]], cpu（c/b/s/r の並び）, purse[], lots[[spec]], teams, limit, last, hist。
- くじは spec {t, p} だけ保存し mkLot で再構築（ふつう12種・ジャックポット4種・マイナス4種）。抽選はホストだけが draw して last.res に保存。
- 全6ラウンド。紙入札：3・5回目が2択（くじごとに1割が落札）、ほかは2割。6回目は賞金2倍。ジャックポットは7割のゲームで1回、マイナスは1〜2回。
- 紙入札：bids/<gNrM>/<uid> = {b, l, at}。時間切れ・全員提出でホストが締め切り。未提出は見送り。CPUは型（計算派・強気派・慎重派・気まぐれ）で入札。
- せり上げ式：参加受付6秒（rise/<key>/<uid>={in:1}）→ 0.9秒ごとに step 点上昇、降りると {in:1,out:価格}。残りが2割以下で現在値で落札。CPUの上限は state.cmax。
- ヒント：hint/<key>/<uid> = 1（確率まで10点）/2（期待値まで計40点）。
- 結果発表 show：showAt から カウントダウン（2.4秒）→ 落札者（〜4.3秒）→ 抽選アニメ → 結果。ひとり練習はタップで飛ばせる。
- 実況（commentary）・班対抗（メンバー平均点）・プレイスタイル診断（styleOf：見送り王／ギャンブラー／期待値の申し子／堅実な投資家／バランス型＋情報通など）。
- 音は WebAudio で合成（SFX）。ホストが参加しないとプロジェクター用の大きな画面（body.big）。
- テスト：/tmp/claude-0/sim/auc/solo.js（sealed|rising）、online.js（sealed|rising、ホスト＋3人・2チーム）。
