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

#### timestamp を持たない更新の stability check

`minimumReleaseAge` は `releaseTimestamp` を持つ更新にしか age を評価できない。timestamp を返さない更新種別は、既定の `minimumReleaseAgeBehaviour: "timestamp-required"` のもとで「age 未達」として扱われ、`renovate/stability-days` が永久に pending になる。`internalChecksFilter: strict` と重なると PR や branch そのものが作られず、`prConcurrentLimit` を設定したリポジトリでは pending の PR が枠を占有して後続の更新まで滞留する。

default プリセットでは 2 つの更新種別を免除している。

| 更新種別 | 設定 | 理由 |
|---|---|---|
| `lockFileMaintenance` | `minimumReleaseAge: null` | lock の再解決はパッケージマネージャが行うため、age ガードは `pnpm-workspace.yaml` の `minimumReleaseAge` 等が resolution 時に担保する |
| `digest` / `pinDigest` | `minimumReleaseAgeBehaviour: "timestamp-optional"` | digest 更新に `releaseTimestamp` が渡らない（[renovatebot/renovate#45236](https://github.com/renovatebot/renovate/issues/45236)）。timestamp が取得できない場合のみ通すため、datasource が timestamp を返すようになれば age ガードは自動的に復活する |

timestamp を持つ通常の依存更新には `minimumReleaseAge: 2 weeks` がそのまま適用される。

### `github-actions`

GitHub Actions の更新をグルーピングし、SHA ピンを有効化する。

```json
"extends": ["github>yshrsmz/renovate-config:github-actions"]
```

### `npm`

npm パッケージの更新を以下のグループに分けて PR を出す。

| グループ | 対象 | `semanticCommitType` |
|---|---|---|
| `npm dependencies` | `dependencies` の minor/patch/pin/digest | `fix` → `fix(deps): ...` |
| `npm devDependencies` | `devDependencies` の minor/patch/pin/digest | `chore` → `chore(deps): ...` |
| `vitejs` | `vite` / `@vitejs/*` (後勝ちで上記から抜き出す) | `chore` → `chore(deps): ...` |

```json
"extends": ["github>yshrsmz/renovate-config:npm"]
```

### `gomod`

Go modules の minor/patch 更新を1つの PR にまとめ、`go mod tidy` を実行する。

```json
"extends": ["github>yshrsmz/renovate-config:gomod"]
```

### `kotlin`

Kotlin / KSP の更新を `Kotlin` グループにまとめ、`semanticCommitType` を `fix` に設定する。Android 以外の Kotlin プロジェクト（Kotlin Multiplatform、Kotlin/JVM 単体など）でも利用可能。

あわせて `java-version` datasource（mise / asdf の JDK 指定）に `minimumReleaseAgeBehaviour: "timestamp-optional"` を適用する。この datasource は `releaseTimestamp` を返さないため、既定の `timestamp-required` では `minimumReleaseAge` を評価できず常に pending 扱いとなり、`internalChecksFilter: strict` と重なると JDK の更新 PR が作られなくなる。

```json
"extends": ["github>yshrsmz/renovate-config:kotlin"]
```

### `android`

Android プロジェクト固有のグルーピングルール。

- `androidx.test` 系をまとめる
- `oss-licenses` 系をまとめ、`semanticCommitType` を `fix` に設定
- `roborazzi` と `ComposablePreviewScanner`（Compose スクリーンショットテスト）をまとめる

```json
"extends": ["github>yshrsmz/renovate-config:android"]
```

`kotlin` プリセットは含まないため、Android プロジェクトでは両方を並べて指定する。

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

### Android / Kotlin プロジェクト

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>yshrsmz/renovate-config",
    "github>yshrsmz/renovate-config:github-actions",
    "github>yshrsmz/renovate-config:kotlin",
    "github>yshrsmz/renovate-config:android"
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
