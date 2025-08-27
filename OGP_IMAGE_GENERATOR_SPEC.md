# OGP画像ジェネレーター 仕様書

## 1. プロジェクト概要

### 1.1 プロダクト名
OGP Image Generator Pro

### 1.2 目的
ウェブサイトやブログのOGP（Open Graph Protocol）画像を簡単に作成できるWebアプリケーション

### 1.3 ターゲットユーザー
- ウェブサイト運営者
- ブロガー
- マーケター
- デザイナー
- 個人開発者

## 2. 機能要件

### 2.1 コア機能

#### 2.1.1 画像生成機能
- **キャンバスサイズ**: 1200x630px（Facebook/Twitter推奨）
- **リアルタイムプレビュー**: 編集内容を即座に反映
- **高解像度出力**: PNG形式でのダウンロード

#### 2.1.2 背景デザイン
- **グラデーション背景**: 
  - プリセット（10種類以上）
  - カスタムグラデーション（色・角度調整可能）
- **パターン背景**: 
  - ジオメトリックパターン
  - ノイズテクスチャ
  - 波紋エフェクト
- **単色背景**: カラーピッカーで選択
- **画像背景**: アップロード機能

#### 2.1.3 画像配置機能
- **画像アップロード**: ドラッグ&ドロップ対応
- **配置オプション**:
  - 位置調整（X, Y座標）
  - サイズ調整（幅、高さ、アスペクト比保持）
  - 回転
  - 透明度調整
- **マスク機能**:
  - 円形マスク
  - 四角形マスク（角丸調整可能）
  - カスタムシェイプ

#### 2.1.4 テキスト編集
- **テキストレイヤー**: 複数レイヤー対応（最大10個）
- **フォント設定**:
  - Google Fonts連携
  - 日本語フォント対応
  - サイズ（8px-200px）
  - 太さ（100-900）
  - スタイル（通常、斜体）
- **テキストエフェクト**:
  - 色（単色、グラデーション）
  - 影（Shadow、Glow）
  - 縁取り
  - 透明度

#### 2.1.5 装飾要素
- **図形**: 
  - 基本図形（円、四角、三角、線）
  - カスタムSVGアップロード
- **アイコン**: Font Awesome連携
- **エフェクト**:
  - グロー効果
  - ぼかし
  - オーバーレイ

### 2.2 プリセット機能

#### 2.2.1 テンプレート
- **カテゴリ別テンプレート**:
  - ブログ記事用（10種）
  - 製品紹介用（10種）
  - イベント告知用（10種）
  - SNS投稿用（10種）

#### 2.2.2 カスタムテンプレート
- テンプレート保存機能
- テンプレート共有機能（URLシェア）
- お気に入り登録

### 2.3 エクスポート機能

#### 2.3.1 画像形式
- PNG（デフォルト）
- JPEG（品質調整可能）
- WebP

#### 2.3.2 サイズバリエーション
- Facebook用（1200x630px）
- Twitter用（1200x675px）
- LinkedIn用（1200x627px）
- カスタムサイズ

#### 2.3.3 一括エクスポート
- 複数サイズ同時出力
- 命名規則カスタマイズ

## 3. 技術仕様

### 3.1 フロントエンド

#### 3.1.1 コア技術
```javascript
{
  "framework": "React 18+ / Next.js 14+",
  "language": "TypeScript",
  "styling": "Tailwind CSS",
  "canvas": "HTML5 Canvas API / Fabric.js",
  "state": "Zustand / Redux Toolkit"
}
```

#### 3.1.2 主要ライブラリ
- **Canvas操作**: Fabric.js or Konva.js
- **画像処理**: Sharp (サーバーサイド)
- **カラーピッカー**: React Color
- **ドラッグ&ドロップ**: React DnD
- **アイコン**: React Icons / Font Awesome
- **フォント**: Google Fonts API

### 3.2 バックエンド（オプション）

#### 3.2.1 API設計
```typescript
// エンドポイント例
POST /api/templates         // テンプレート保存
GET  /api/templates/:id     // テンプレート取得
POST /api/images/process    // 画像処理
GET  /api/fonts             // フォント一覧取得
```

#### 3.2.2 データベース構造
```sql
-- templates テーブル
CREATE TABLE templates (
  id UUID PRIMARY KEY,
  name VARCHAR(255),
  data JSONB,  -- キャンバスの状態を保存
  thumbnail_url TEXT,
  user_id UUID,
  is_public BOOLEAN,
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);

-- user_projects テーブル  
CREATE TABLE user_projects (
  id UUID PRIMARY KEY,
  user_id UUID,
  project_name VARCHAR(255),
  canvas_data JSONB,
  exported_urls TEXT[],
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);
```

### 3.3 パフォーマンス要件
- **レンダリング速度**: 60fps維持
- **画像生成時間**: 3秒以内
- **最大ファイルサイズ**: アップロード10MB、出力5MB

## 4. UI/UX設計

### 4.1 画面構成

#### 4.1.1 メインエディター
```
┌─────────────────────────────────────┐
│  ヘッダー（ロゴ、保存、エクスポート） │
├────────┬────────────────────┬────────┤
│        │                    │        │
│ ツール │    キャンバス      │プロパティ│
│ パネル │   (1200x630)       │ パネル │
│        │                    │        │
├────────┴────────────────────┴────────┤
│     レイヤーパネル / テンプレート     │
└─────────────────────────────────────┘
```

#### 4.1.2 レスポンシブ対応
- デスクトップ: フル機能
- タブレット: 簡易エディター
- モバイル: プレビューのみ

### 4.2 操作フロー

1. **テンプレート選択** or **新規作成**
2. **背景設定**
3. **画像追加**（オプション）
4. **テキスト追加・編集**
5. **装飾追加**（オプション）
6. **プレビュー確認**
7. **エクスポート**

## 5. 拡張機能（将来実装）

### 5.1 AI機能
- **自動レイアウト提案**: コンテンツに基づくレイアウト最適化
- **テキスト生成**: キーワードからキャッチコピー生成
- **画像生成**: DALL-E / Stable Diffusion連携

### 5.2 コラボレーション
- **リアルタイム共同編集**
- **コメント機能**
- **バージョン管理**

### 5.3 自動化
- **API提供**: プログラマティックな画像生成
- **Webhook連携**: CMS連携での自動生成
- **バッチ処理**: CSV入力での一括生成

### 5.4 分析機能
- **A/Bテスト**: 複数バージョンの効果測定
- **SNSパフォーマンス追跡**: エンゲージメント分析

## 6. マネタイズモデル

### 6.1 フリーミアムモデル
```yaml
Free:
  - 月10枚まで生成
  - 基本テンプレート
  - 透かし入り

Pro ($9.99/月):
  - 無制限生成
  - プレミアムテンプレート
  - 透かしなし
  - 優先サポート

Team ($29.99/月):
  - Pro機能全て
  - チーム共有
  - ブランドキット
  - API アクセス
```

## 7. 開発ロードマップ

### Phase 1: MVP（2ヶ月）
- [x] 基本的なキャンバス機能
- [x] テキスト追加・編集
- [x] 画像アップロード
- [x] PNG エクスポート

### Phase 2: 機能拡張（2ヶ月）
- [ ] テンプレートシステム
- [ ] 高度なテキストエフェクト
- [ ] レイヤー管理
- [ ] ユーザーアカウント

### Phase 3: プロダクト化（2ヶ月）
- [ ] 決済システム統合
- [ ] API開発
- [ ] パフォーマンス最適化
- [ ] モバイル対応

### Phase 4: グロース（継続）
- [ ] AI機能追加
- [ ] コラボレーション機能
- [ ] 分析ダッシュボード
- [ ] マーケットプレイス

## 8. 技術的な実装例

### 8.1 キャンバス初期化
```typescript
import { fabric } from 'fabric';

class OGPCanvas {
  private canvas: fabric.Canvas;
  
  constructor(canvasId: string) {
    this.canvas = new fabric.Canvas(canvasId, {
      width: 1200,
      height: 630,
      backgroundColor: '#ffffff'
    });
  }
  
  addGradientBackground(colors: string[], angle: number) {
    const gradient = new fabric.Gradient({
      type: 'linear',
      gradientUnits: 'pixels',
      coords: { /* 角度に基づく座標計算 */ },
      colorStops: colors.map((color, i) => ({
        offset: i / (colors.length - 1),
        color
      }))
    });
    
    const rect = new fabric.Rect({
      width: 1200,
      height: 630,
      fill: gradient
    });
    
    this.canvas.add(rect);
  }
  
  exportImage(format: 'png' | 'jpeg' = 'png'): string {
    return this.canvas.toDataURL({
      format,
      quality: 1,
      multiplier: 2 // 高解像度出力
    });
  }
}
```

### 8.2 状態管理
```typescript
// Zustand Store
interface OGPStore {
  canvas: fabric.Canvas | null;
  layers: Layer[];
  activeLayer: string | null;
  template: Template | null;
  
  // Actions
  setCanvas: (canvas: fabric.Canvas) => void;
  addLayer: (layer: Layer) => void;
  updateLayer: (id: string, updates: Partial<Layer>) => void;
  deleteLayer: (id: string) => void;
  loadTemplate: (template: Template) => void;
  exportImage: () => Promise<Blob>;
}
```

## 9. セキュリティ考慮事項

- **画像アップロード**: ファイルタイプ検証、サイズ制限
- **XSS対策**: ユーザー入力のサニタイズ
- **CORS設定**: 画像リソースの適切な設定
- **レート制限**: API使用制限
- **データ暗号化**: ユーザーデータの暗号化保存

## 10. 参考リソース

- [Open Graph Protocol公式](https://ogp.me/)
- [Facebook OGP Debugger](https://developers.facebook.com/tools/debug/)
- [Twitter Card Validator](https://cards-dev.twitter.com/validator)
- [Fabric.js Documentation](http://fabricjs.com/)
- [Canvas API MDN](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API)

---

*作成日: 2025年1月*
*バージョン: 1.0.0*