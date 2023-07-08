# ディレクトリ構成

このドキュメントでは、当プロジェクトにおけるディレクトリ構成について説明します。

## src

`src`ディレクトリには**アプリケーション全体で共有される要素**が格納され、以下のような構造です。例外については、これから詳しく説明します。

``` bash
app
├── src
│   ├── api        # アプリケーション全体で使われるAPI Hooks
│   ├── components # アプリケーション全体で使われるコンポーネント
│   ├── config     # グローバル設定や環境変数などが格納され、アプリケーションで使用される
│   ├── consts     # アプリケーション全体で使われる定数
│   ├── objects    # オブジェクトベースのモジュール
│   ├── hooks      # アプリケーション全体で使われるReact Hooks
│   ├── libraries  # ライブラリの再エクスポート
│   ├── pages      # ルーティング設定
│   ├── providers  # アプリケーションの全プロバイダー
│   ├── stores     # アプリケーション全体で使われる状態管理
│   ├── styles     # アプリケーション全体で使われるスタイル
│   ├── types      # アプリケーション全体で使われる型定義
│   └── utils      # アプリケーション全体で使われるユーティリティ関数
```

### components

``` bash
src
├── components
│   ├── icons   # アプリケーション全体で使われるアイコンコンポーネント
│   ├── layouts # レイアウトコンポーネント
│   ├── pages   # ページのコンポーネント
│   └── shared  # アプリケーション全体で使われるコンポーネント
```

#### components/pages

`src/components/pages`ディレクトリは、アプリケーションの各ページコンポーネントを格納する場所です。`src/pages`ディレクトリも存在しますが、ここではルーティングのみを行い、ページコンポーネントの実体は`src/components/pages`に格納します。

Next.jsでは、`page`ディレクトリに新しいファイルが追加されると、そのファイル名が自動的に利用可能なルートとなります。ただし、場合によっては、ファイル名と目的のコンポーネント名が一致しないことがあります。この問題を解決するために、ページコンポーネントの実体を`src/components/pages`ディレクトリに格納し、ファイル名とページコンポーネント名が一致するように図っています。

また、このプロジェクトでは、今後Next.js 13のApp Routerへの移行をスムーズに行えるようにすることを目指しています。そのため、ページコンポーネントの実体は`src/components/pages`で管理し、一方で`src/pages`ではルーティングのみを担当するように設定しています。

#### components/layouts

`src/components/layouts`ディレクトリには、アプリケーションのさまざまなレイアウトコンポーネントが格納されています。このディレクトリ内には、HeaderやFooterなどの主要なレイアウトコンポーネントが含まれています。

### libraries

`src/libraries`ディレクトリは、アプリケーションで利用するライブラリを格納する場所です。ここにライブラリを置くことで、依存関係を簡単に把握できます。ライブラリを使う際には、直接使う代わりにラッパーを作成し、そのラッパーを通じて利用してください。このようにライブラリをラップすることで、将来ライブラリの変更が必要になった場合でも、影響を最小限に抑えることができます。この方法により、インターフェースを維持しながら内部の実装を変更できるようになります。

### pages

`pages`ディレクトリは、Next.jsの[Routing機能](https://nextjs.org/docs/routing/introduction)を活用するために使用されます。このディレクトリ責務は、各ページへのルーティングを制御することです。

## objects

`objects`ディレクトリは、**アプリケーション全体で共有されない**、特定のオブジェクトに関連するコードを格納する場所です。これらのオブジェクトは、[オブジェクト指向UI設計](https://www.sociomedia.co.jp/10241)において、ユーザーが意識するべき要素として抽出されたものです。

`objects`ディレクトリ内には、それぞれのオブジェクトに対応したディレクトリが作成され、関連するファイルがまとめられています。開発者は`src/objects/awesome-object`ディレクトリにアクセスすることで、該当オブジェクトに関連するコードを確認できます。これにより、改修時に特定のオブジェクトに関わるコードを探す手間が省かれ、開発効率の向上が期待できます。

``` bash
objects
├── object1
│   ├── api        # 特定のオブジェクトで使われるAPI Hooks
│   ├── components # 特定のオブジェクトで使われるコンポーネント
│   ├── consts     # 特定のオブジェクトで使われる定数
│   ├── hooks      # 特定のオブジェクトで使われるReact Hooks
│   ├── model      # オブジェクトのモデル
│   ├── stores     # 特定のオブジェクトで使われる状態管理
│   └── types      # 特定のオブジェクトで使われる型定義
├── object2
│   ├── ...
```

### model

`model`は、オブジェクトモデリングを通じて抽出されたオブジェクトを表現するために使用される型定義です。

model.tsのサンプルコードは以下のようになります。
  
``` ts
export type User = {
  id: string;
  name: string;
  age: string;
};
```

## 参考

- [Screaming Architecture - Evolution of a React folder structure](https://dev.to/profydev/screaming-architecture-evolution-of-a-react-folder-structure-4g25)
- [Bulletproof React / Project Structure](https://github.com/alan2207/bulletproof-react/blob/master/docs/project-structure.md)
- [SPA Componentの推しディレクトリ構成について語る](https://zenn.dev/yoshiko/articles/99f8047555f700)
