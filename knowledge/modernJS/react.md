# React

## 特徴

### JSX

JavaScriptの中にHTMLタグを記述

### VirtualDOM

DOMの状態をメモリ上に保持し、必要最小限の更新で済ます

### コンポーネント指向

## コンポーネントのライフサイクル

### コンポーネント生成とDOMへのマウント

初回のみ実行

- constructor(props)

- componentWillMount()　・・・コンポーネントがDOMにマウントされる直前

- render()

- componentDidMount()

### コンポーネント更新

- componentWillReceiveProps(nextProps)　・・・コンポーネントのプロパティが変更された時

## コンポーネント3大要素

### 状態(state)

- コンポーネントの状態を表す書き換え可能なデータの集まり(例：リストの選択値、チェックボックスのチェック有無、テキストボックスのテキスト等)

- 状態が変化するとコンポーネントの再描画が行われる

- 外部非公開で値の変更は、this.setState() で行う

### プロパティ(props)

- 外部との窓口となる、要素のプロパティ(＝タグの属性)

- コンポーネント自身はプロパティを変更できず(読取専用)、親コンポーネントから設定される(componentWillReceiveProps(nextProps))

- 初期値の設定(defaultProps)と型の検証(propTypes)を行える

- 通常、プロパティの変更は、状態の変更となり、以下が呼び出される

 <pre>
   shouldComponentUpdate（
 </pre>

### イベント(onXXX)

## ツール

### create-react-app

Reactプロジェクト生成
