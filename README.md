# gh-codeapps-management-copilot

Power Platform Code Apps 用の Agent・Skill・Prompt と CI 雛形を、GitHub Template Repository として共通配布します。

```text
この Template Repo
  → Use this template → アプリごとの新規 Repo
  → Clone → VS Code → /Quickstart

共通定義の更新
  → 各アプリ Repo で /UpdateCustomizations → 更新 PR → レビュー・マージ
```

**テンプレートからの生成は初期コピーです。元 Repo の更新は既存 Repo に自動反映されません。**

## 管理者によるテンプレート公開

1. 本変更をレビュー・マージし、共通定義 CI の成功を確認します。
2. GitHub の **Settings → General → Template repository** を有効にします。
   この文書やファイルの変更だけでは GitHub 上の設定は変わりません。
3. [管理情報](.github/customizations.json) の `version` を配布版として管理します。
   変更時は版を更新し、その版に対応したリリースタグ（例: `v1.0.0`）を対象コミットへ付けて公開します。
   ファイル内の版番号だけではタグや GitHub Release は作成されません。
4. テンプレートから試験 Repo を作成し、アプリ生成前の共通定義 CI、
   Clone 後のカスタマイズ認識、Quickstart による既存 Repo 再利用を確認します。

テンプレートにはアプリ本体を含めません。Code Apps のアプリ雛形は初期設定時に別途取得します。

## 利用者のクイックスタート

### 1. 新規 Repo を作成して Clone

GitHub の **Use this template → Create a new repository** を使用します。
CLI を使う場合は、テンプレート公開後に次のように実行します。

```powershell
gh repo create YOUR-ORG/my-codeapp `
  --template ketana0224/gh-codeapps-management-copilot `
  --private `
  --clone

Set-Location .\my-codeapp
code .
```

コピー元 Repo 自身ではなく、**作成したアプリ Repo のルート**を VS Code で開きます。
Git、GitHub CLI、Node.js、Copilot を利用できる VS Code と認証を準備してください。
Code Apps CLI 等の必要版・権限は setup 時に確認します。テンプレートには認証情報を含めません。

### 2. VS Code で初期設定

1. 信頼できる Repo であることを確認し、Copilot のカスタマイズ画面で
   `gh-management` Agent と同梱 Skill の認識を確認します。
2. Prompt files に対応する Local セッションで `/Quickstart` を実行します。
3. [Quickstart](.github/prompts/quickstart.prompt.md) が現在の Repo と `origin` を GitHub と照合します。
   **既存 Repo を `reuse` モードで利用し、Repo の再作成や共通定義の再ダウンロードは行いません。**
4. [setup Skill](.github/skills/gh-management-setup/SKILL.md) が既存のローカル設定を再利用し、
   不足項目を確認・保存します。変更と push の承認後、アプリソース・アプリ CI・運用情報を整えます。
5. アプリ CI の成功と GitHub 上への反映を確認します。

Agent・Skill が認識されない場合は Repo ルートを開いているか、利用中の VS Code と
選択した実行環境が該当のカスタマイズ形式に対応するかを確認します。

### 3. ローカル設定と初回公開

設定項目は [設定テンプレート](.github/skills/gh-management-setup/references/.env.example) を参照してください。
実際の設定は対象プロジェクト直下の `.env` に保存し、Git 管理から除外します。
テンプレート生成済み Repo では `GITHUB_REPO_MODE=reuse` です。
認証シークレット・トークン・操作承認は保存しません。

**GitHub Secrets／Variables、Environment と保護、ブランチ保護、端末の認証・CLI はテンプレートでは配布されません。**
GitHub Actions がローカルの `.env` を自動利用する構成でもありません。
Repo ごとに設定・権限・対象環境を確認します。

**初期設定完了は実デプロイ可能という意味ではありません。**
初期デプロイ Workflow は診断して失敗するロック状態です。
人による初回公開、SP への編集権限共有、GitHub Environment の保護、認証検証を終え、
別途承認したロック解除の後に [deploy Skill](.github/skills/gh-management-deploy/SKILL.md) へ引き継ぎます。

## CI の分離

| Workflow | 用途 | テンプレート生成直後 |
|---|---|---|
| [Customizations CI](.github/workflows/customizations-ci.yml) | 共通定義の構成・リンク・管理情報の検証 | 実行可能。アプリや npm 依存関係は不要 |
| [Code App CI の雛形](.github/skills/gh-management-setup/references/workflows/codeapp-ci.yml) | アプリのテスト・ビルド・公開前検証 | references 内のため実行されない |
| [Code App deployment](.github/workflows/codeapp-deploy.yml) | 初回準備前の公開防止 | 手動起動すると理由を表示して失敗する |

Quickstart はアプリ生成後にアプリ用 CI を実運用の `.github\workflows` へ配置します。
雛形にある CI スクリプト等を準備・調整し、存在しないコマンドをそのまま残しません。
**共通定義 CI の成功を、アプリ CI 成功や公開準備完了として扱いません。**

ローカルの共通定義検証:

```powershell
node --test .github\tests\customizations.test.mjs
```

この検証は構成の回帰検出用であり、VS Code の実際の認識や Agent の動作、
GitHub Actions の実行・アプリの公開を証明するものではありません。

## 共通定義の版管理と更新

[管理情報](.github/customizations.json) が共通定義の版・配布元・取得元 SHA・管理対象パスを保持します。

- `version`: 同梱した共通定義の配布版。アプリの版とは別です。
- `upstream`: 共通配布元です。
- `upstreamCommit`: 更新時に取得した完全 SHA です。初期値 `null` は取得元 SHA 未記録を意味します。
  テンプレート生成先の初期コミットは配布元のコミットではないため、代用しません。
  初期配布の由来は管理者が配布元と内容を照合して記録するか、最初の共通更新で確定します。
- `managedPaths`: Agent・Prompt・Skill（付属 references を含む）・共通定義テストです。
  管理対象内でも利用側の変更を無断で上書きしません。

各アプリ Repo で `/UpdateCustomizations` を実行し、公開済みタグまたは完全 SHA を指定します。
[更新 Prompt](.github/prompts/update-customizations.prompt.md) がタグを完全 SHA に固定し、
差分・競合を提示します。承認後に更新・検証・作業ブランチへの push・PR 作成を行い、
マージはレビュー後に実施します。ローカル更新だけの依頼では push しません。
初回等で取得元 SHA が不明な場合は、差分のある既存ファイルをすべて要確認として扱います。

次は一括更新しません。

- 実運用の Workflow: アプリ構成や公開先に依存するため、個別の差分確認・検証・承認が必要です。
  references 内の雛形を更新しても、実運用の Workflow は変更しません。
- アプリソース・運用 README・Repo 固有の Instructions・ローカル設定
- Secrets、Environment、ブランチ保護、認証情報

フォルダ全体の置換・無断削除、自動マージ、共通更新に伴う deploy は行いません。
Repo 数が増えた場合は、同じ版固定・差分確認ルールで更新 PR の作成を自動化できます。

## 同梱カスタマイズ

- [gh-management Agent](.github/agents/gh-management.agent.md): 初期設定と継続更新の窓口
- [Quickstart](.github/prompts/quickstart.prompt.md): Clone 済み Repo の初期設定
- [UpdateCustomizations](.github/prompts/update-customizations.prompt.md): 共通定義の更新 PR
- [setup Skill](.github/skills/gh-management-setup/SKILL.md): 既存 Repo の初期設定／明示依頼時の Repo 新規作成
- [deploy Skill](.github/skills/gh-management-deploy/SKILL.md): 初回準備後のアプリ変更・PR・公開追跡
