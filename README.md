# renovate-config

yshrsmz 個人プロジェクト向けの Renovate shareable config presets を管理する。

## 使い方

各リポジトリの `renovate.json` で `extends` に指定して利用する。

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>yshrsmz/renovate-config"]
}
```

名前付きプリセットを追加で指定する場合:

```json
{
  "extends": [
    "github>yshrsmz/renovate-config",
    "github>yshrsmz/renovate-config:github-actions",
    "github>yshrsmz/renovate-config:npm"
  ]
}
```

## プリセット一覧

### `default` (デフォルトプリセット)

`github>yshrsmz/renovate-config` で参照される標準プリセット。

| 設定 | 値 |
|---|---|
| extends | `config:best-practices`, `github>yshrsmz/renovate-config:vulnerability-alerts`, `:maintainLockFilesWeekly` |
| timezone | `Asia/Tokyo` |
| schedule | 毎週月曜 9:00〜12:00 |
| semanticCommits | enabled |
| dependencyDashboard | true |
| labels | `dependencies` |
| minimumReleaseAge | 2 weeks |
| prCreation | not-pending |
| internalChecksFilter | strict |
| prHourlyLimit | 5 |

> **Note:** 各設定値の SoT は `default.json` です。README の値と差異がある場合は `default.json` を優先してください。

### `github-actions`

GitHub Actions の更新をグルーピングし、SHA ピンを有効化する。

```json
"extends": ["github>yshrsmz/renovate-config:github-actions"]
```

### `npm`

npm の minor/patch 更新を1つの PR にまとめる。

```json
"extends": ["github>yshrsmz/renovate-config:npm"]
```

### `gomod`

Go modules の minor/patch 更新を1つの PR にまとめ、`go mod tidy` を実行する。

```json
"extends": ["github>yshrsmz/renovate-config:gomod"]
```

### `dockerfile`

Dockerfile / docker-compose の更新をグルーピングし、SHA ピンを有効化する。

```json
"extends": ["github>yshrsmz/renovate-config:dockerfile"]
```

### `tooling`

開発ツール (aqua, mise) の管理ルール。`aquaproj/aqua-renovate-config` を継承し、aqua-registry の更新頻度を隔週に抑制する。

```json
"extends": ["github>yshrsmz/renovate-config:tooling"]
```

### `vulnerability-alerts`

脆弱性アラートをスケジュールや安定期間を無視して即時対応する。

```json
"extends": ["github>yshrsmz/renovate-config:vulnerability-alerts"]
```

## 構成例

### シンプルなリポジトリ

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>yshrsmz/renovate-config"]
}
```

### npm + GitHub Actions を使うリポジトリ

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>yshrsmz/renovate-config",
    "github>yshrsmz/renovate-config:github-actions",
    "github>yshrsmz/renovate-config:npm"
  ]
}
```

### リポジトリ固有の設定をオーバーライド

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>yshrsmz/renovate-config"],
  "ignorePaths": [".devcontainer/**"],
  "rangeStrategy": "pin"
}
```
