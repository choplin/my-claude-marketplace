---
name: draft-pr
description: Use this skill when the user wants to create a draft PR. Triggers on phrases like "draft PRを作って", "draft PRを作成", "ドラフトPRを開いて", "draft PR作って", or when the user wants to push and open a PR in draft mode.
allowed-tools: Bash(git *), Bash(gh *)
user-invocable: true
---

# Draft PR 作成

現在のブランチを push し、ドラフトモードで PR を作成する。

## 必要情報

1. **現在のブランチ**: main/master 以外であること
2. **リモートの状態**: push が必要かどうか
3. **PR テンプレート**: `.github/pull_request_template.md` の有無

## プロセス

1. **ブランチ確認**
   - 現在のブランチが main/master でないことを確認
   - main/master の場合はエラーとして停止

2. **PR テンプレート確認**
   - `.github/pull_request_template.md` を探す
   - 存在する場合はその内容を読み込む

3. **Push 実行**
   - `git push -u origin <branch>` でリモートに push
   - 既に push 済みの場合はスキップ

4. **Draft PR 作成**
   - `gh pr create --draft` でドラフト PR を作成
   - テンプレートがある場合はその形式に従って body を作成
   - タイトルはコミットメッセージや変更内容から適切に生成

5. **結果報告**
   - 作成した PR の URL を報告

## テンプレート対応

PR テンプレートが存在する場合：
- テンプレートの各セクションを適切に埋める
- 変更内容を分析して Summary/Description セクションを作成
- Test Plan がある場合はテスト方法を記載
