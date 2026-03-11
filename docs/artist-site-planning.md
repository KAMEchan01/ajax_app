# アーティスト公式サイト設計案（Next.js + Sanity + Vercel）

> 目的：情報整理が明快で、作品（音楽・映像）が主役になる日本人アーティスト向け公式サイトの初期設計。

## 1. トップページのワイヤーフレーム

```text
┌──────────────────────────────────────────────────────────────┐
│ [Logo]                                 [JP/EN] [Menu]       │
├──────────────────────────────────────────────────────────────┤
│ HERO / Latest Release                                        │
│ ┌──────────────────────────────────────────────────────────┐ │
│ │ [Artwork / Key Visual]                                  │ │
│ │ Release: 2026.03.01                                     │ │
│ │ Title: NEW ALBUM "ORBIT"                               │ │
│ │ Short Copy（世界観を一言で）                             │ │
│ │ [Listen] [Watch Video] [Details]                        │ │
│ └──────────────────────────────────────────────────────────┘ │
├──────────────────────────────────────────────────────────────┤
│ NEWS（最新3件）                           [View all]         │
│ ・2026.03.10 ツアー情報公開                                      │
│ ・2026.03.05 MV公開                                             │
│ ・2026.03.01 アルバム配信開始                                    │
├──────────────────────────────────────────────────────────────┤
│ MUSIC PICKS（最新/注目作品）               [All Music]      │
│ [Card] [Card] [Card]                                        │
│ 作品名 / 種別 / リリース日 / 主要リンク                         │
├──────────────────────────────────────────────────────────────┤
│ VIDEO PICKS（最新MV/Live）                 [All Video]      │
│ [Large Thumbnail + Play] [List 2~3]                         │
├──────────────────────────────────────────────────────────────┤
│ ABOUT（短いプロフィール + 写真）             [Read More]    │
├──────────────────────────────────────────────────────────────┤
│ CONTACT CTA                                                  │
│ 「出演依頼・問い合わせはこちら」 [Contact Form]                 │
├──────────────────────────────────────────────────────────────┤
│ FOOTER                                                        │
│ SNS / Copyright / Privacy / Label or Management info         │
└──────────────────────────────────────────────────────────────┘
```

### レイアウト方針
- ファーストビューで「最新作品」を1テーマで強く訴求（CTAは3つまで）。
- 1セクション1目的（Newsはニュースだけ、Musicは作品だけ）で認知負荷を下げる。
- 余白を多めに取り、本文は最大幅 `max-w-screen-xl`、テキストは `max-w-3xl` 前後で可読性確保。
- モバイルでは「縦スクロールで迷わない順番（Latest → News → Music → Video → About → Contact）」を固定。

---

## 2. 全体ナビゲーション設計

## グローバルナビ（常時）
- News
- Music
- Video
- About
- Contact
- （任意）Store / Live

## ヘッダー仕様
- PC：上部固定（透過→スクロールで白背景に変化）
- SP：ハンバーガー + フルスクリーンメニュー
- 右上：言語切替（JP/EN）

## ナビゲーション導線（主要フロー）
1. **新規訪問者**：Top Hero → Latest Details → Music詳細
2. **情報確認ユーザー**：Top → News一覧 → News詳細
3. **視聴目的ユーザー**：Top → Video一覧 → 外部再生（YouTube等）
4. **問い合わせユーザー**：Top/フッター → Contact

## ルーティング案（App Router）
- `/` : トップ
- `/news` / `/news/[slug]`
- `/music` / `/music/[slug]`
- `/video` / `/video/[slug]`
- `/about`
- `/contact`

---

## 3. Music詳細ページの情報設計

## ページ目的
- 作品理解を最短で行い、視聴・購入・再生へ遷移させる。

## 情報ブロック順
1. **作品ヘッダー**
   - ジャケット
   - タイトル / サブタイトル
   - 種別（Single/EP/Album）
   - リリース日
   - 品番（必要時）
2. **CTAエリア（最重要）**
   - Listen（Apple Music / Spotify / YouTube Music）
   - Watch（MVやVisualizer）
   - Buy（CD/Store）
3. **トラックリスト**
   - Track No / 曲名 / Time / Credits（任意）
4. **作品説明**
   - コンセプト文（短）
   - ロングライナー（任意）
5. **クレジット**
   - Lyrics / Music / Arrange / Recording / Mix / Master
6. **関連コンテンツ**
   - 関連MV
   - 関連News
   - 前後作品ナビ（Prev / Next）
7. **SEO補助情報（画面下部or構造化のみ）**
   - canonical / OGP / JSON-LD（MusicAlbum or MusicRecording）

## Sanityスキーマ想定（Music）
- `title`, `slug`, `releaseDate`, `format`, `coverImage`
- `description`, `longDescription`
- `streamingLinks[]`（platform, url）
- `videos[]`（reference）
- `tracks[]`（trackNo, title, duration, credits）
- `credits[]`（role, name）
- `relatedNews[]`（reference）
- `seo`（metaTitle, metaDescription, ogImage）

---

## 4. 必要コンポーネント一覧

## レイアウト系
- `SiteHeader`
- `GlobalNav`
- `MobileMenu`
- `SiteFooter`
- `SectionHeading`
- `Container`

## コンテンツ系
- `HeroLatestRelease`
- `NewsList` / `NewsCard`
- `MusicCardGrid` / `MusicCard`
- `VideoFeatured`
- `AboutSnippet`
- `ContactCTA`

## 詳細ページ系（Music）
- `MusicHero`
- `MusicMeta`
- `StreamingLinks`
- `TrackList`
- `CreditList`
- `RelatedContent`

## 共通UI
- `Button`（primary / ghost）
- `Tag`
- `DateLabel`
- `RichTextRenderer`（Sanity Portable Text）
- `ResponsiveImage`
- `ExternalLinkIcon`

## SEO/計測
- `SeoHead`（App Routerの metadata でラップ）
- `JsonLd`
- `Breadcrumb`

---

## 5. 実装の優先順位

## Phase 1（MVP / 最短公開）
1. プロジェクト基盤作成（Next.js App Router + TS + Tailwind + ESLint）
2. Sanity接続（Dataset・Token・GROQ取得）
3. ルーティング骨格（Top / News / Music / Video / About / Contact）
4. トップページ実装（Hero + 最新News/Music/Video）
5. Music一覧・Music詳細（CTA・Track・Creditsまで）
6. 基本SEO（title/description/OGP/canonical/sitemap/robots）

## Phase 2（品質向上）
1. 画像最適化（next/image、blur placeholder）
2. JSON-LD実装（MusicAlbum / Article / Breadcrumb）
3. パフォーマンス改善（LCP優先、不要JS削減）
4. アクセシビリティ調整（キーボード操作、コントラスト、aria）

## Phase 3（運用拡張）
1. プレビュー機能（Sanity Visual Editing）
2. 多言語化（JP/EN）
3. Contact API連携（reCAPTCHA + rate limit）
4. Vercel Analytics / Search Console連携

## Vercel前提の運用メモ
- `main` pushで本番デプロイ、PRでPreview Deploy。
- Sanity WebhookでISR再生成（`/api/revalidate`）。
- 環境変数はVercel Project Settingsで管理。
