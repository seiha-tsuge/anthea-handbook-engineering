# コンポーネント設計

このドキュメントでは、当プロジェクトにおけるコンポーネントの設計方針について説明します。

## 基本原則

### 単一責任原則

コンポーネントは、できるだけ単一の責任を持つように設計する必要があります。もしコンポーネントが複数の責任を持っている場合、それらを単一責任を持つ小さなコンポーネントに分割してください。単一責任原則に従ったコンポーネントは、再利用しやすく、保守性が高まり、コードが読みやすくなり、テストを行いやすくなります。

単一責任原則のメリットは以下のとおりです。

- 責任が明確になるため、他の箇所で使いやすくなります。
- 単一の責任だけを持つため、他の開発者がコードを理解しやすくなります。
- 単一の責任に集中することで、バグの発見や修正がしやすくなります。
- 振る舞いが明確であるため、正常に動作していることを確認するテストが行いやすくなります。

### Shared Component

コンポーネントを設計する際には、できるだけ再利用性に焦点を当ててください。再利用できるコンポーネントを作ることで、アプリケーション全体の一貫性が高まり、保守性が向上します。また、開発コストや時間を削減でき、より効率的な開発が可能になります。

アプリケーション全体で共有するコンポーネントは、`src/components/shared`ディレクトリに格納してください。また、オブジェクト内で共有するコンポーネントは、`src/objects/object/components/shared`ディレクトリに格納してください。オブジェクト内で共有されるコンポーネントは、そのオブジェクトの範囲内での使用を前提としています。

### Container/Presentational パターン

コンポーネントを、Container/Presentationalパターンを利用して、機能と見た目を分離してください。

コンポーネントは、Container/Presentational パターンを用いて、ロジックと見た目を分離してください。Containerコンポーネントは、ロジックや状態管理を担当し、表示するデータに関心を持ちます。一方、Presentationalコンポーネントは、ユーザーにどのように表示するかに関心を持ちます。

以下の表は、Container/Presentationalパターンの概要を示しています。

| コンポーネントの種類 | View  | Logic | Props | State |
| :------------------- | :---: | :---: | :---: | :---: |
| Container            |   ×   |   ○   |   △   |   ○   |
| Presentational       |   ○   |   ×   |   ○   |   ×   |

具体的な実装例や詳細については、[Container/Presentational Pattern](https://javascriptpatterns.vercel.app/patterns/react-patterns/conpres)を参照してください。

TODO: React Hooksとテストについて追記する

### 分割基準

TODO

## ディレクトリ構成

``` bash
components
├── Component
│   ├── Component.stories.tsx  # StorybookのStory
│   ├── Component.styles.ts    # スタイルの定義
│   ├── Component.test.tsx     # テストコード
│   ├── Component.tsx          # コンポーネントの本体
│   ├── Component.types.ts     # TypeScriptの型定義
│   └── index.ts               # エントリーポイント
```

### 階層構造

Reactでは、コンポーネントの階層構造が非常に重要です。これはデータがpropsを通じて上位コンポーネントから下位コンポーネントへ一方向に流れるためです。この仕組みは`単方向データフロー`と呼ばれています。一般的なユースケースとして、JSONのデータを表示する場面がありますが、適切に設計されていれば、コンポーネントの階層構造とJSONの階層構造が一致するはすです。この一致が生じる理由は、UIとデータモデルが似た階層構造を持つこと多いからです。

ディレクトリの階層構造をコンポーネントの階層構造に沿って整理することで、UIの構造とディレクトリの構造が一致し、開発者が変更を加えるべきファイルを特定しやすくします。また、関連するコンポーネントが近接して配置されることで、開発者はコードを効率的に修正することができます。

以下に、ディレクトリ構造がUIの構造に対応する例を示します。

``` bash
UserProfile
├── ProfileHeader
│   ├── ProfileHeader.tsx # 子Nodeとなるコンポーネント
│   └── index.ts 
├── UserBio
│   ├── UserBio.tsx       # 子Nodeとなるコンポーネント
│   └── index.ts
├── FollowerList
│   ├── FollowerList.tsx  # 子Nodeとなるコンポーネント
│   └── index.ts      
├── UserProfile.tsx       # 親Nodeとなるコンポーネント
├── index.ts    
```

## UI コンポーネントライブラリ

当プロジェクトでは、UIコンポーネントライブラリに[Mantine](https://mantine.dev/)を採用しています。Mantineのコンポーネントを使用する際は、Sharedコンポーネントとしてラップしてください。これを行うことで、Mantineのコンポーネントが利用できる場所は、src/components/sharedディレクトリに限定されます。

以下のコードは、MantineのButtonコンポーネントをSharedコンポーネントとしてラップする例です。

``` tsx filename="src/components/shared/Button.tsx"
import React, { forwardRef } from 'react';

import { Button as MantineButton } from '@mantine/core';
import type { ButtonProps as MantineButtonProps } from '@mantine/core';

const Button = forwardRef<HTMLButtonElement, MantineButtonProps>(({ children, sx, ...props }, ref) => {
  return (
    <MantineButton ref={ref} sx={() => ({ ...sx })} {...props}>
      {children}
    </MantineButton>
  );
});
Button.displayName = 'Button';
```

このラップされたButtonコンポーネントは、プロジェクト内の他の場所でインポートして使用することができます。UIコンポーネントライブラリを将来的に変更する必要がある場合でも、src/components/sharedディレクトリ内のコンポーネントのみを修正することで、影響範囲が最小限に抑えられます。これにより、プロジェクトのメンテナンスが容易になります。

## スタイルソリューション

当プロジェクトでは、スタイルソリューションとして、Mantineが提供する[createStyles](https://mantine.dev/styles/create-styles/)を採用しています。これは、emotionをベースに構築されたcss-in-jsで、MantineのThemeシステムを効率的に利用することができます。これにより、コンポーネント間でのスタイルの一貫性を保つことが容易になります。

具体的な使用例やcreateStylesの機能については、[Mantine のドキュメント](https://mantine.dev/styles/create-styles/)を参照してください。このドキュメントには、基本的な使い方や高度なテクニックが説明されています。

ただし、注意が必要な点があります。Mantineの開発者による[コメント](https://github.com/mantinedev/mantine/issues/2815#issuecomment-1509758301)によれば、Mantineの将来のバージョンである7.0から、ネイティブCSSへの移行が予定されています。そのため、今後のアップデートに伴い、プロジェクトのスタイルソリューションに関して変更が必要になる場合があります。その際は、Mantineのドキュメントやリリースノートをチェックし、適切な対応を行ってください。

## 参考

- [Components And Styling](https://github.com/alan2207/bulletproof-react/blob/master/docs/components-and-styling.md)
- [Thinking in React](https://react.dev/learn/thinking-in-react)
- [Container/Presentational Pattern](https://javascriptpatterns.vercel.app/patterns/react-patterns/conpres)
- [【Atomic Designに懐疑的なあなたへ】改めて考えたい React / Next.js のデザインパターン](https://zenn.dev/mutex_inc/articles/beca85dd7fdcae)
- [React Hooksとカスタムフックが実現する世界 - ロジックの分離と再利用性の向上](https://qiita.com/sonatard/items/617f324228f75b9c802f)
