# AR Demo Starter (MindAR + A-Frame)

QRコードからスマホブラウザでAR体験ができる、最小構成のスターターキット。

## 使い方（全体フロー）

```
[マーカー画像を用意] → [.mind ファイル化] → [GitHub Pages 等で公開]
  → [URL を QR コード化] → [スマホでQRを読む] → [AR起動]
```

---

## セットアップ手順

### 1. マーカー画像の準備

- 特徴点の多い画像を1枚用意する（ロゴ、写真、イラスト等）
- 推奨：縦横比1:1〜4:3、解像度512px以上、コントラスト高め
- NG：単色塗りつぶし、繰り返しパターン、QRコードそのもの（認識可だが不安定）

`assets/marker.png` に置く。

### 2. マーカーを `.mind` ファイルにコンパイル

MindAR 公式のオンラインツールで変換：

🔗 https://hiukim.github.io/mind-ar-js-doc/tools/compile

- "Image Target Compiler" を開く
- マーカー画像をアップロード
- "Start" → "Download" で `.mind` を取得
- `assets/targets.mind` として保存

### 3. 3Dモデルの準備

`assets/sample.glb` に GLB 形式の3Dモデルを置く。

無料モデル入手先：
- [Sketchfab](https://sketchfab.com/) — "Downloadable" + glTF/GLB
- [Poly Pizza](https://poly.pizza/) — 商用利用可・軽量モデル多数
- [Khronos Sample Models](https://github.com/KhronosGroup/glTF-Sample-Models)

> 💡 スマホでサクサク動くには **5万ポリゴン以下・テクスチャ2K以下** を目安に。

### 4. ローカルで動作確認

ブラウザのカメラAPIは **HTTPS or localhost** でないと動かない。

```bash
# Python 3
python3 -m http.server 8080

# Node (npx)
npx serve
```

→ `http://localhost:8080` をスマホで…は不可（HTTPSじゃないため）。
ローカル確認は PC のブラウザで疑似的にチェックするか、後述のホスティングを先にやる。

### 5. 公開（GitHub Pages 推奨・無料）

```bash
# GitHub に push 後、リポジトリの Settings → Pages →
# Branch: main / Folder: /AR-demo (or root) を選んで Save
```

数十秒で `https://<username>.github.io/<repo>/AR-demo/` が発行される。

他の無料ホスティング：
- [Vercel](https://vercel.com/) — `vercel` コマンドで即デプロイ
- [Netlify Drop](https://app.netlify.com/drop) — フォルダをドラッグするだけ

### 6. QRコード生成

公開URLをQRコード化：
- [QR Code Generator](https://www.qr-code-generator.com/)
- [QRCode Monkey](https://www.qrcode-monkey.com/)

印刷物・スライド・展示パネルに配置。

### 7. 体験

1. スマホでQRを読む
2. ブラウザが開き、カメラ許可を求められる
3. 許可するとARが起動
4. マーカー画像にカメラを向けると3Dモデルが現れる 🎉

---

## ファイル構成

```
AR-demo/
├─ index.html         ← メインのページ（このまま編集）
├─ README.md          ← この手順書
└─ assets/
   ├─ marker.png      ← (要追加) 認識させる元画像
   ├─ targets.mind    ← (要生成) MindARコンパイラの出力
   └─ sample.glb      ← (要追加) 表示する3Dモデル
```

---

## カスタマイズのヒント

### 3Dモデルの位置・サイズ調整

`index.html` の `<a-gltf-model>` を編集：

```html
<a-gltf-model
  src="#model"
  position="0 0 0"      <!-- X Y Z(マーカーから手前) -->
  rotation="0 0 0"       <!-- X Y Z 回転 -->
  scale="0.5 0.5 0.5">   <!-- 大きさ -->
</a-gltf-model>
```

### 2D画像を表示

```html
<a-image src="./assets/poster.png"
         position="0 0 0.01"
         width="1" height="1.4"></a-image>
```

### 動画を表示

```html
<a-assets>
  <video id="movie" src="./assets/demo.mp4" autoplay loop muted playsinline></video>
</a-assets>
<a-video src="#movie" position="0 0 0.01" width="1.6" height="0.9"></a-video>
```

### 音を鳴らす

```html
<a-sound src="./assets/sfx.mp3" autoplay="true"></a-sound>
```

### タップで反応

```html
<a-gltf-model src="#model"
              class="clickable"
              animation__click="property: scale; from: 0.5 0.5 0.5; to: 0.8 0.8 0.8; startEvents: click; dur: 300">
</a-gltf-model>
<a-camera>
  <a-cursor></a-cursor>
</a-camera>
```

### 複数マーカー切替

`<a-entity mindar-image-target="targetIndex: 0">` を複製して `targetIndex: 1`, `2` …
（MindARコンパイラに複数画像をまとめて投入する）

### マーカーレス（床に置く）にしたい場合

MindAR ではなく **WebXR + Three.js** または **8th Wall** が必要。
MindAR は画像/顔/トラッキング限定。

---

## トラブルシュート

| 症状 | 原因 | 対処 |
|---|---|---|
| カメラが起動しない | HTTPでアクセスしている | HTTPS or localhostにする |
| マーカーを認識しない | 画像の特徴点が少ない | コントラスト強めの写真に変更 |
| 3Dモデルが表示されない | パスが違う / GLBが壊れている | DevToolsで404を確認 |
| iPhoneで真っ黒 | iOS 17未満 / Safari古い | iOS17+のSafariで再試行 |
| マーカーから3Dがズレる | scale/positionが大きすぎ | scaleを 0.1〜0.5 に下げる |

---

## 参考リンク

- MindAR 公式ドキュメント: https://hiukim.github.io/mind-ar-js-doc/
- MindAR サンプル集: https://github.com/hiukim/mind-ar-js/tree/master/examples
- A-Frame ドキュメント: https://aframe.io/docs/
- A-Frame コンポーネント集: https://aframe.io/aframe-registry/

---

## 動作要件

- iOS Safari 13+ / Android Chrome 80+
- HTTPS環境
- カメラ付き端末
