# gh-codeapps-management-copilot

Power Platform Code Apps 用の GitHub Repo・CI・デプロイ Workflow を準備する Copilot カスタマイズです。

## クイックスタート

1. VS Code の Copilot Chat で `/` を入力し、[Quickstart](.github/prompts/quickstart.prompt.md) を選択します。
2. 対象プロジェクトを確認します。[setup スキル](.github/skills/gh-management-setup/SKILL.md) が
   対象直下の `.env` を再利用し、なければ必要情報を質問して作成します。既存の場合も不足・不一致だけを確認して保存します。
3. Repo 作成・リモート書き込みの計画を確認して承認すると、Repo、Code Apps ソース、CI、
   初期ロック済みデプロイ Workflow、運用 README を作成・検証します。

設定項目は [`.env.example`](.github/skills/gh-management-setup/references/.env.example) を参照してください。
Repo 所有者・名前・公開範囲・ブランチ、Power Platform のテナント ID・環境名・環境 ID、
アプリ情報、GitHub Environment 名などを記録します。手動で準備する場合も、このテンプレートを対象直下の `.env` にコピーします。
テンプレート内の選択肢を確認して入力し、空欄は未確認として扱います。新規アプリの ID などは確認後に追記します。

`.env` は Git 管理から除外するローカルの非秘密設定です。認証シークレット・トークン・操作承認は保存しません。
GitHub Actions がこの `.env` を自動利用する構成ではなく、Workflow 用の設定・秘密は別途登録・検証します。

**クイックスタート完了は、実デプロイ可能という意味ではありません。**
初期デプロイ Workflow は診断して失敗するロック状態です。人による初回公開、SP への編集権限共有、
GitHub Environment の保護、認証検証を終え、ロック解除を別途承認してから
[deploy スキル](.github/skills/gh-management-deploy/SKILL.md) に引き継ぎます。
