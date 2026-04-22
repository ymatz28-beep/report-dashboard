# report-dashboard-deploy HANDOFF

## [Auto-Kaizen] 2026-04-21
- [WARN] report-dashboard-deploy/HANDOFF.md not updated in 7 days (threshold: 7).



## Last Updated
2026-04-07

## Completed (x-ref wealth-strategy: Public gnav SSoT化 + Intel除去 2026-04-06)
- **Before**: index.htmlのgnavが `class="current"` でハイライト + Intel(private)リンクが公開gnavに残存（`2591880`でprivate URL除去済みだったが、Intel Dashboardリンクが`6dae7df`で再追加されていた）
- **After**: `class="current"` → `aria-current="page"` に変更（アクセシビリティ準拠）。Intelリンク除去でPUBLIC_NAV準拠（Hub / Property / Travel の3項目）。lib/renderer.py `PUBLIC_NAV` 全3項目に `primary: True` 追加（overflow dropdownに落ちていた問題修正）
- **Commits**: 未push（index.html）

## Completed (section_navコンポーネント作成 + 3ページ移行 2026-03-28)
- **Before**: セクションナビが各ページにインラインで個別実装。health_dashboard(63行CSS/JS), stock_report(48行CSS/JS)が重複。Marketページにはセクションナビ自体が存在せず、区分/一棟/戸建てへのジャンプ手段がなかった。スクロール検知はscroll offset手動計算で不安定
- **After**: 再利用可能 `lib/templates/components/section_nav.html` 作成（section_nav_css/section_nav/section_nav_jsの3マクロ）。health_dashboard: 63行→3行。stock_report: 48行→3行（selectattrフィルタで条件分岐）。Market: sticky セクションナビ（区分/一棟/戸建て+件数バッジ）新設。全ページ統一デザイン（pill形状、backdrop blur、IntersectionObserver、モバイル横スクロール）。合計+28行/-120行
- **Commits**: Projects main `825a8df`, property-report `4bf703d`

## Completed ([infra] leader-digest sleep耐性 + コミット清掃 + 全ジョブ検証 2026-03-28)
- **Before**: leader-digestが`StartCalendarInterval`(07:00/12:00/17:30固定)でPCスリープ中にスキップされていた。前セッションの3実装(x_poster, Health Check collector, lessons.md)がコミットされず残存。infra-manifest.yamlのxbookmarks script pathが旧`run_pipeline.sh`のまま
- **After**: leader-digestを`StartInterval 10800s`(3h冪等リトライ)に移行しスリープ耐性獲得。launchd reload完了。全実装5コミット完了。infra-manifest.yamlのxbookmarks pathを`run_pipeline.py`に修正。残り3ジョブ(kaizen-patrol/stock-analyzer/xbookmarks)はStartCalendarInterval維持（macOSがスリープ後catch upするため問題なし）。全launchdジョブ正常実行確認済み(kaizen 11:00, stock 07:00, xbookmarks 14:15)。Market Intel timeout修正効果確認（14:11更新）。Health Checkデータは3/18が最新（health-trackerでの記録待ち）
- **Commits**: Projects main `4744eac`（infra-manifest leader-digest+xbookmarks）他4コミット（Health Check digest, x_poster+quality_gate, lessons.md整理, session sync）

## Completed (Skill Routing追加 2026-03-27)
- **Before**: スキルの自動発火条件がCLAUDE.mdに未記載。ユーザーがスキル名を明示的に指定するか、CC側が暗黙的に判断する必要があった
- **After**: Projects/CLAUDE.mdに「Skill Routing」テーブル（8パターン）を追加。バグ→`systematic-debugging`、実体験→`story-intake`等が文脈から自動発火。`story-intake`のトリガーに「インタビューして」も追加
- **Commits**: なし（CLAUDE.mdローカル編集のみ、commit未実行）

## Completed (ナビSSoT強制 2026-03-24)
- **Before**: 9箇所にナビのコピペが散在。renderer.py変更しても他ファイルは古いまま残る状態
- **After**: `renderer.py`に`get_nav_html(scope, current_page, absolute)`追加。全ジェネレータがSSoT関数を呼ぶ方式に統一。leader_digest.py/generate_dashboard.pyもSSoT化。kaizen config false positive修正(33→31件, error 0)
- **Commits**: report-dashboard-deploy `f5f8209`, kaizen-agent `b21032d`

## 前セッション完了
- **[stock] Intelパイプライン改修（2026-03-24）**: intel_report.py + transcript_fetcher.py + video_summarizer.py + daily_synthesizer.py を更新。stock-analyzer main push済み
- **[cross] Cloudflareフルデプロイ（2026-03-24）**: deploy_private.py — stock(4)/wealth(3)/intel/action/health/cisco/newsletter(2)/functions(1)/index.html + gnav injection(4ページ)。QA PASSED。Cloudflare Pages ✅
- **[cisco-os] Leader Digest gnav追加（2026-03-23）**: 標準Type Bハンバーガーメニューを`leader_digest.py`に追加。全privateページと統一ナビ
- **[kaizen] constancy_checks.pyモジュール分割（2026-03-23）**: 1,712行モノリスを6モジュールに分割。17チェック全正常動作確認済み
- **[trip-planner] fukuoka.html新規作成（2026-03-22）**: 福岡ローカルガイドページ。**APIエラーでセッション中断、commit+push未完了**

## In Progress / Next Actions
1. **index.html push** — gnav SSoT化+Intel除去の変更をcommit+push。GitHub Pages反映確認
2. **iuma-private.pages.dev トップページ Bento Grid リデザイン** — x-ref wealth-strategy Next Actions #1。Tier1(Stock/Wealth/Action)=wide、Tier2=1x1、Tier3=compact
3. **iuma-hub.html廃止** → リデザイン後のトップページに置き換え
4. **GHA deploy script conflict-proof化**: daily-patrol.ymlのgh-pagesデプロイを/tmp clone方式に書き換え
5. **[trip-planner] fukuoka.html commit+push+確認**
6. **マネタイズ Phase 1: リーダーシップ知見の棚卸し**
7. **ポートフォリオUXリデザイン Phase 2**: Expert Digestテーマとポートフォリオ銘柄の自動マッピング
8. **Action Tracker GitHub PAT設定**
9. **lib/qa_output.py QA gate拡張**: deploy_private.pyのQA対象追加
10. **残りナビ未SSoT化ファイルのget_nav_html()移行** — wealth/strategy, wealth/index等

## Key Decisions
- **section_navコンポーネント設計（2026-03-28）**: IntersectionObserver（rootMargin '-20% 0px -70% 0px'）でscroll offset手動計算を置換。3マクロ構成（CSS/HTML/JS）でJinja2 import+1行呼び出し。stock_reportはselectattr('show')でif分岐を宣言的に置換。Marketページは都市切替時にバッジ件数を動的更新
- **StartCalendarInterval維持判断（2026-03-28）**: kaizen-patrol/stock-analyzer/xbookmarksの3ジョブはStartCalendarInterval維持。macOSはスリープ後にcatch upするため問題なし。leader-digestのみStartIntervalに移行（固定3回/日では不足だったことが根本原因、スリープ問題ではなかった）
- **GHA deploy根本原因特定（2026-03-20）**: `git fetch origin gh-pages:gh-pages`がpatrol後のworking tree残骸(data/output)と衝突→merge conflict。3/17以降レポート更新停止。対策: /tmp clone方式への書き換え
- **report-dashboard Intelリンク保持方針（2026-03-20）**: gnav Hub/Intel/Property/Travelの4項目を維持。Intelリンク(`iuma-private.pages.dev/intel/`)はpublic gnavに含める（セキュリティ上問題なし: Cloudflare Accessで保護済み）
- **Leader Digest↔Action Tracker相互リンク（2026-03-20）**: cisco/index.html(Leader Digest) ↔ action/index.html(Action Tracker)双方向リンク。LINE AM nudge両URL配信。Cloudflare Pagesデプロイ済み
- **Leader Digest Ollama完全ローカル化（2026-03-20）**: qwen3:8b使用。外部API(Claude/Anthropic)参照なし確認済み。OneDrive経由でWebex transcript取得→Ollama要約
- **subproject track分離（2026-03-20）**: ルートリポからkaizen-agent/stock-analyzer等をindex除去。.gitignoreに8サブプロジェクト列挙。report-dashboard-deployも対象
- **vtt_cache TTL 7日（2026-03-20）**: 30日超は0件だったため7日でTTL適用。42MB→13MB。キャッシュは自動再生成
- **Credibility Trackerソース名正規化が最優先（2026-03-20）**: 60→20chで的中率の信頼性向上。channels.yaml(18ch定義済み)をSSoTに
- **マネタイズ構想設計（2026-03-18）**: 5ドメイン(健康/投資/AI/不動産/リーダーシップ) × PWA/SaaS。monetization-strategy.md作成。Phase 1=知見棚卸し、Phase 2=エージェントチームでグランドデザイン、Phase 3=MVP構築
- **アクション自律追加ルール（2026-03-18）**: 中長期アクション発生時にaction_items.yamlへ指示なしで自動追加。MEMORY.mdに記録
- **Intel Hub → Expert Digest改名・分離（2026-03-10）**: メルマガAI構造化要約のみに特化。Market Intel（YT+市場指標）とは完全分離。gnavラベル="Expert"。配信: 勝間=毎日、中島=火、大前=金
- **gnav統一順序確定（2026-03-09→03-10更新）**: Private: `Stock → Market Intel → Expert → Wealth → Action → Property → Travel`（全7項目）。Public: `Hub → Property → Travel`（private URL載せない）。`memory/design-system.md` に記録
- **Typography Rule制定（2026-03-04）**: h1最大40px、モバイルh1最大24px、装飾的巨大テキスト禁止。ダッシュボード=「読むもの」
- **Public/Private サイト分離（2026-03-04）**: Public (GitHub Pages) = Property + Travel のみ。Private (Cloudflare Access) = Stock + Wealth + Action
- **共通gnav導入（2026-03-04）**: 全ページで統一ナビゲーション（ハンバーガー対応）、フォントはInter統一
- **Private Hub テンプレート管理（2026-03-04）**: deploy-private.sh 内のヒアドキュメントで生成
- **stock-analyzer実行スクリプト（2026-03-04）**: `run.py`（generate_portfolio.pyではない）
- **リポジトリ移動（2026-03-03）**: `~/Documents/report-dashboard` → `~/Documents/Projects/report-dashboard-deploy`。全プロジェクトを Projects/ 配下に統一
- **gnav OPSEC方針確定（2026-03-05）**: 公開ページのgnavにprivate URL（Stock/Wealth/Action）は一切載せない。Property/Hubのみ。property-report 3都市に適用済み
- **gnav 2層構造確定（2026-03-07）**: site-header(Hub/Property/Travel) = グローバルナビ + .gnav(Hub/大阪/福岡/東京/内覧分析/問い合わせ) = property固有サブナビ。両方必須
- **Mobile Update workflow（2026-03-07）**: GitHub Issues('update'ラベル) → Claude Code自動編集。スマホから更新可能
- **Private repos Mobile Update見送り（2026-03-10）**: stock-analyzer/wealth-strategy/market intelは自動生成ダッシュボードでスマホ手動更新不要
- **Google Drive Data Bridge構築（2026-03-16）**: Drive整理9カテゴリ化、data-bridge/portfolio/でローカル↔クラウド橋渡し
- **3証券体制確立（2026-03-16）**: SBI(CSV) + 楽天(CSV) + MooMoo(YAML手動)
- **deploy_private.py SSoT化（2026-03-20）**: deploy_private.pyが全7パス+functions+hubをカバー。deploy-private.shはfallback。GHA経由Cloudflareデプロイは見送り（output/がgitignore → Cloud生成が複雑、ローカル自動化で十分）
- **constancy deploy marker監視（2026-03-20）**: .deploy_private_YYYY-MM-DDマーカーでデプロイ成否をconstancy_checksが毎晩自動検知。deadline_hour=12
- **infra-manifest.yaml deployments（2026-03-20）**: Public/Private全サイトを宣言的管理するセクション追加
- **Verification Before Done強化（2026-03-16）**: サブリポ個別commit+push義務化 + Before/After/Remaining状況サマリ義務化
- **constancy_checksモジュール分割設計（2026-03-23）**: 1,712行モノリス→facade(202行)+4ドメインモジュール(common/data/structural/infra)+violation_tracker。最大モジュールinfra.py(691行)。violation_trackerは14日放置でWARN→ERROR自動昇格
- **gnav Private nav順序更新（2026-03-23）**: `Stock → Market Intel → Expert → Wealth → Action → Cisco → Self-Insight → Health → Property → Travel`。renderer.py `_private_nav`に"Cisco"追加。Leader Digestにも同一gnavを実装
- **ナビSSoT強制設計（2026-03-24）**: 全ジェネレータが`renderer.py`の`get_nav_html()`を呼ぶ方式に統一。Before: 9箇所にナビのコピペ散在（renderer.py変更しても他は古いまま）。After: PRIVATE_NAV変更→次の生成/デプロイで全自動反映+毎晩QA検知。3層防御: SSoT関数 + デプロイ時inject_gnav()自動注入 + patrol.py gnav_consistency夜間検知
- **Gnav Pattern B + PUBLIC_NAV primary修正（2026-04-06）**: PRIVATE_NAV=primary 5項目+overflow 8項目(⋯ドロップダウン)。PUBLIC_NAV全3項目に`primary: True`追加。public index.htmlは`aria-current="page"`でアクセシビリティ準拠。Intelリンクは公開gnavから完全除去

## ブロッカー / 注意事項
- **GHA gh-pagesデプロイが壊れやすい**: daily-patrol.ymlの現行deploy方式はworking treeとgh-pagesブランチの衝突でmerge conflictを起こす。/tmp clone方式への書き換えが最優先
- 東京property管理費表示率が90%で他都市(100%)より低い
- Google Drive MCP: 壊れたまま。OAuth curl直接APIで迂回中
- `.gitignore`をEditするとフックがサブプロジェクトセクションを削除する場合あり。即座に再追加が必要
- downloads_router.py: bashインラインPythonは日本語文字列が壊れる → 別ファイル化が必須
- エージェントチームレビュー: Refiner未実行。次回Refiner→Tier B/C実装
- Expert Digest 過去アーカイブ一括AI要約が未着手
- iuma-hub.htmlがgit未追跡（PC交換で消失リスク）
- wealth/strategy, wealth/index, root index のnavは`get_nav_html()`未適用（次回生成時にSSoT化すべき）
- property-report/index.html のsite-navにTravelリンクがない（他の子ページにはある）。軽微だがgnav統一方針との不整合

## 環境構築メモ（PC交換用）
- Branch: `gh-pages`
- Location: `/Users/yumatejima/Documents/Projects/report-dashboard-deploy`
- Remote: `https://github.com/ymatz28-beep/report-dashboard.git`
- Public deploy: GitHub Pages
- Private deploy: Cloudflare Pages (`iuma-private.pages.dev`) via wrangler
- Deploy SSoT: `stock-analyzer/scripts/deploy_private.py` (全パスカバー)
- Deploy fallback: `/Users/yumatejima/Documents/Projects/scripts/deploy-private.sh`
- 依存: `wrangler` (npm install -g wrangler)
- Cloudflare: `wrangler login` でOAuth認証

## History
| 日付 | サマリー |
|------|---------|
| 2026-04-06 | Before: index.html gnav class="current"+Intelリンク残存 → After: aria-current="page"+Intel除去+PUBLIC_NAV準拠3項目 |
| 2026-03-28 | [cross] Before: セクションナビ3ページにインライン重複(111行) → After: section_nav.htmlコンポーネント化+3ページ移行(+28/-120行)。825a8df, 4bf703d |
| 2026-03-28 | [infra] Before: leader-digest StartCalendarInterval+未コミット3実装 → After: StartInterval 3h移行+5コミット完了+全ジョブ正常確認(4744eac) |
| 2026-03-27 | [cross] Before: スキル自動発火条件がCLAUDE.md未記載 → After: Skill Routingテーブル8パターン追加+story-intake「インタビューして」トリガー追加 |
| 2026-03-24 | [cross] ナビSSoT強制: renderer.py get_nav_html()追加。leader_digest.py/generate_dashboard.pyをSSoT化(9箇所コピペ→0)。kaizen config false positive修正(33→31, error 0)。root f5f8209, kaizen b21032d |
| 2026-03-24 | [stock] Intelパイプライン改修(intel_report/transcript_fetcher/video_summarizer/daily_synthesizer)。Cloudflareフルデプロイ+QA PASS。lessons.md「テスト→デプロイ→open不可分」追記 |
| 2026-03-23 | [cisco-os/kaizen] Leader Digest gnav追加 + renderer.py Ciscoナビ追加 + Action Tracker ciscoカード拡張(ソースバッジ/digest要約)。Cloudflareフルデプロイ+QA PASS。kaizen dea98d0, root 2a2a73e |
| 2026-03-23 | [kaizen] constancy_checks.py分割(1712→202L facade + 6モジュール1541L) + violation_tracker追加(14日放置で自動エスカレーション)。a34b7bc push済み |
| 2026-03-22 | [trip-planner] fukuoka.html新規作成(温泉/グルメ/エリア/アクセス/Tips)。APIエラー(502/ConnectionRefused)でセッション中断、commit+push未完了 |
| 2026-03-22 | [cross] HANDOFF横断監査: 全25プロジェクトのHANDOFF.md探索・Cowork HANDOFF読み取り。report-dashboard-deploy変更なし |
| 2026-03-21 | [kaizen] Public→Private導線遮断セキュリティ監査: trip-planner(5)/property-report(7)/renderer.py全クリーン。report-dashboard Intelリンクは承認済み例外。2層設計(scope)正常確認 |
| 2026-03-20 | [cross] deploy_private.py SSoT化(全7パス+functions+hub)。constancy deploy marker監視追加+正常/異常テストPASS。infra-manifest.yaml deployments追加。stock-analyzer conflict解消+push |
| 2026-03-20 | property-report gh-pages手動デプロイ復旧(3日間stale)。GHA deploy merge conflict根本原因特定(/tmp clone方式が次課題)。report-dashboard Intelナビデグレ復元。E2E検証HTTP 200 |
| 2026-03-20 | [cisco-os] Leader Digest↔Action Tracker相互リンク実装+Cloudflareデプロイ+18項目QA全パス(CIRCUIT削除/Ollama/OneDrive/plist/launchd等) |
| 2026-03-20 | [cross] #51 constancy警告一掃: git_uncommitted(`9c5a370`subproject track解除)、vtt_cache cleanup(42→13MB)、Credibility再評価(833件/58.4%) |
| 2026-03-18 | [cross] #50 マネタイズ構想設計: 5ドメイン×PWA/SaaS収益化戦略構造化、monetization-strategy.md作成、action_items.yaml monetize-01追加、アクション自律追加ルール策定 |
| 2026-03-17 | [cross] constancy patrol GHA health検証: 全5リポWF単位で死活チェック。report-dashboard pages-build-deployment 🟢正常確認 |
| 2026-03-16 | [cross] kaizen #63: CLAUDE.mdルール強化(Verification Before Done+サブリポcommit+push義務化+Before/After/Remaining)、midday digest追加(3x/day) |
| 2026-03-16 | [stock] #42-#47: Portfolio UXリデザインP1完了、moomoo証券統合(Newsletter+ポートフォリオ)、KI テンプレートリファクタ、投資余力3証券合算修正、Digest色修正+patrol復旧 |
| 2026-03-16 | [cross-project] #48 Google Drive整理(440→9カテゴリ)、data-bridge/portfolio/、Drive Desktop、downloads_router.py(7カテゴリ+launchd WatchPaths)、property .gitignore修正 |
| 2026-03-13 | Public gnav Intel除去 + [cross-project]インフラ構造改革・通知GD(#41): Gmail v2/LINE 6種/daily_digest一本化/notification.md改訂 |
| 2026-03-10 | [cross-project] Mobile Update workflow修正完了。API key設定(3リポ)、Self-hosted Runner撤去、Private repos対応見送り決定 |
| 2026-03-10 | Before: Intel HubがYT要約とメルマガ混在 → After: Expert Digest改名・分離。gnav "Expert"ラベル化 |

