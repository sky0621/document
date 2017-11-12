# Flux

## ReactにFluxが必要な理由

Reactは画面表示を担当するライブラリ。複雑なUI構造をコンポーネント指向で表現するにはとてもよい。

ただ、コンポーネント間のデータ（状態）の流れを制御しようとすると、ルールがないとカオス（コールバック地獄）になりがち。

本格的なアプリをReactのみで作るとシステム構成が複雑になる。

Fluxでは、各機能ごとにアプリを分割し、情報が上から下へと一方通行で各機能に伝わるように流れを定義することで構造を簡潔にする。

### backbone.jsやVue.jsではダメか

backbone.jsやVue.jsは、ある程度大規模なアプリになると向いてないらしいが、どの程度の規模まで耐えうるかは不明。

## Fluxの機能

### 参考図

![flux flow](flux.png "flux flow")

### Action ... 実行委員

Actionからすべての処理がはじまるが、Actionが行うのは何を行うのかを決め、それを行える機能に処理を任せる役目。

（MVCで言うところのController？）

### Dispatcher ... 連絡係

Actionから依頼された仕事を記録係であるStoreに伝達する役目。

（エンタープライズアプリケーションアーキテクチャパターンのフロントコントローラーパターンに登場するRequestDispatcherのイメージ？）

### Store ... 記録係

アプリの各状態（アプリが利用するデータ全般）を記録する役目。

この状態が変化する＝アプリの見た目が変わる（ことが多い）ため、Storeが変化すると画面の再描画を行うよう、Viewが更新される。

（MVVMで言うところのModelかつViewModel？）

Action、Dispatcherは、やることが限られていて、Viewは表示のみということで、Storeにロジックが集中してしまいそうな感。

Storeは、Dispatcherにディスパッチしてもらうために自身をDispatcherに登録しておく（Observerパターン）。

### View ... 表示係

Storeの状態に応じて画面を表示する役目。

Viewに相当するのが、React。

## 決まり事

必ず、下の一方通行の流れにする必要がある。

[Action] -> [Dispatcher] -> [Store] -> [View] -> [Action] ...

例：

数をかぞえるカウンターアプリで言うと、カウントボタンが押された際にActionが実行される。

ActionはDispatcherに選んで処理を任せ、Dispatcherはボタンが押されたということをStoreに伝達する。

Storeは現在のカウントを１つ加算する。Storeの変更はViewに通知され、画面に現在のカウンターの値が表示される。

※画面で何かが起きた時は、必ずActionから物事が始まるようにする。Viewでボタンが押されたからといって、Viewの中でStoreを呼んでカウントを加算するのはNG。

## サンプル実装

![Facebookの提供するサンプル](https://github.com/facebook/flux/tree/master/examples/flux-todomvc)
