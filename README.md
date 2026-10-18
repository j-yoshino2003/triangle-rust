# Triangle-rust

Rust + Vulkan で画面に三角形 1 枚を表示するだけの学習用リポジトリ。

## 目的

Vulkan の初期化から描画までの一巡を、Rust らしい書き方で一度自分の手で通すこと。
三角形が出た時点でこのリポジトリの目的は達成で、それ以上の機能は追わない。

- Vulkan の構造（Instance → Device → Swapchain → RenderPass → Pipeline → CommandBuffer）を把握する
- 所有権・ライフタイム・`Result` によるエラー処理・`Arc` による共有といった Rust の基礎を、実際に動くものの中で覚える
- この先ゲームエンジン / フレームワークを使うときに、下で何が起きているかを想像できる状態にする

ゲーム開発そのものはこのリポジトリでは行わない。

## 技術選択

| 用途 | クレート | 選んだ理由 |
| --- | --- | --- |
| Vulkan | [vulkano](https://github.com/vulkano-rs/vulkano) 0.35 系 | Vulkan の構造をそのまま保ちながら、リソース解放と同期を Rust の型と所有権で扱える。生の Vulkan を直に叩く [ash](https://github.com/ash-rs/ash) と違い `unsafe` が要点だけに限られるため、Vulkan と Rust を同時に学ぶのに向く |
| ウィンドウ / 入力 | [winit](https://github.com/rust-windowing/winit) | Rust のグラフィックス系で事実上の標準。Vulkan のサーフェス作成に必要なウィンドウハンドルを渡せる |
| シェーダ | GLSL + `vulkano-shaders` | `shader!` マクロでビルド時に SPIR-V へコンパイルされるため、`glslc` を手で叩く手順も生成物の管理も要らない |

ash を選ばなかったのは、Vulkan C API を 1:1 で写すことになり、学習の主対象が「Rust」ではなく
「C API の Rust 記法」に寄るため。wgpu を選ばなかったのは、Vulkan が内部実装の一候補に隠れて
しまい「Vulkan を使った実装」にならないため。

## 前提環境

| 必要なもの | 用途 |
| --- | --- |
| Rust ツールチェーン（rustup 経由、stable） | ビルド。Windows では既定ターゲットが `x86_64-pc-windows-msvc` なので MSVC のリンカを使う |
| Visual Studio の C++ ビルドツール + Windows SDK | 上記ターゲットのリンクに必要 |
| [Vulkan SDK](https://vulkan.lunarg.com/)（LunarG） | 検証レイヤ（`VK_LAYER_KHRONOS_validation`）と `glslc`。シェーダのコンパイルは `vulkano-shaders` が行うので、SDK は主に検証レイヤのために入れる |
| Vulkan 対応 GPU とそのドライバ | 実行 |

確認:

```powershell
rustup show
vulkaninfo --summary
```

## 実装の段取り

vulkano で三角形が出るまでに通る手順。1 つずつ順に積む。

1. `Instance` を作る（検証レイヤを有効化し、winit が要求するサーフェス拡張を渡す）
2. ウィンドウを作り、そこから `Surface` を得る
3. 物理デバイスを列挙し、グラフィックスとプレゼントの両方ができるキューファミリを持つものを選ぶ
4. 論理デバイス（`Device`）とキューを作る
5. `Swapchain` と、その各イメージに対する `ImageView` を作る
6. 頂点データ（三角形 3 頂点）をバッファに置く
7. 頂点シェーダ / フラグメントシェーダを `shader!` マクロで取り込む
8. `RenderPass` と `Framebuffer` を作る
9. `GraphicsPipeline` を作る
10. コマンドバッファを記録し、キューへ submit して present する
11. ウィンドウのリサイズでスワップチェーンを作り直す

## ビルドと実行

```powershell
cargo run
```

初回は依存クレートの取得とビルドに時間がかかる。最適化ビルドは `cargo run --release`。

## 状態

環境構築まで完了。`Cargo.toml` / `rustfmt.toml` / `src/main.rs` を置き、依存クレート
（`vulkano` 0.35.2 / `vulkano-shaders` 0.35.0 / `winit` 0.30.13）を入れた状態で
`cargo build` / `cargo run` / `cargo fmt --check` / `cargo clippy` が通ることを確認した。

`src/main.rs` は `Hello, world!` を出すだけで、Vulkan の実装はまだ入っていない。
上の「実装の段取り」の 1 番から順に積んでいくのが次の一歩。

## 参考

| 文書 | URL |
| --- | --- |
| Vulkan Tutorial（C++。Vulkan の手順そのものの定番） | https://vulkan-tutorial.com/ |
| vulkano guide（vulkano での三角形まで） | https://vulkano.rs/ |
| vulkano の triangle example | https://github.com/vulkano-rs/vulkano/tree/master/examples/triangle |
| The Rust Programming Language（日本語版） | https://doc.rust-jp.rs/book-ja/ |

コーディング規約の正本は `.claude/rules/`（日本語訳は `docs/ja-JP/rules/`）。
作業ルールは `AGENTS.md` にある。