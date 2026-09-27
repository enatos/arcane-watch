# arcane-watch

日次と週次の画面は `daily.html` / `weekly.html`。中身は同じ階層の `daily.json` / `weekly.json` を読む。`main` に入ると Pages が配信する。

ページは `status` を見ない。`status` は書く側の印。`ok` / `empty` / `failed`。

## 日次 daily.json

ページが読むキー:

- `period.start` / `period.end` / `period.timezone`。無いときは期間表示が「—」
- `items[]` の `kind` `timing` `announce_type` `timing_at` `headline` `target` `org` `what` `meaning`
- `url` があるときだけリンク。表示文字は `url_label`。無ければ「公式」
- `items` が空のとき `empty_reason`。無ければ「本日の新情報なし」

`kind` は次の5つ。これ以外は色が付かん。

- 能力拡張
- 公式リリース
- 論文
- 資金
- 政策

`generated_at` と `title` は日次ページは読まん。週次と揃えるために置いてよい。

### 0件・空

```json
{
  "status": "empty",
  "empty_reason": "本日の新情報なし",
  "items": []
}
```

`period` は分かるなら入れる。分からんときは `null`。

### 調査失敗

JSONとして届くとき:

```json
{
  "status": "failed",
  "empty_reason": "調査失敗　理由",
  "items": [],
  "period": null,
  "generated_at": null
}
```

`empty_reason` は「調査失敗」で始める。ページはその文字列をそのまま出す。

JSONが壊れている、または取れないときは、ページが「調査失敗」と解析エラーを出す。この形はJSONでは表せん。

## 週次 weekly.json

ページが読むキー:

- `period.start` / `period.end` / `period.timezone`
- `generated_at`（先頭16文字を生成時刻として出す）
- `cross_memo`。`null` なら横断メモ欄は出さん
- `lanes` は3つ。`id` は `harness` / `memory` / `specialized`
- 各レーンの `name` `has_movement` `summary` `this_week` `background` `changed` `views` `eri` `next`

`has_movement` が true なら「動きあり」、false なら「動きなし」。動きなしでも各欄は文を入れる。空文字にせん。

### 0件・空

レーンは3つのまま。全部 `has_movement: false`。各欄は「動きはなし」の文。`cross_memo` は `null`。`status` は `empty`。

### 調査失敗

JSONとして届くとき: 3レーンとも `has_movement: false`。`status` は `failed`。`cross_memo` を「調査失敗　理由」にする。

JSONが壊れている、または取れないときは、日次と同じくページ側の「調査失敗」表示になる。
