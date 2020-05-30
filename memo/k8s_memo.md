## ちょっとしたメモ
* 下記でK8sのリソース名が全部みれるので、忘れたら見てみると良いかも
```
$ kubectl api-resources
```
* あと、各APIバージョンが分からない時は下記
```
$ kubectl api-versions
```
* 各リソースの説明は下記でみれる、manifestを書く際に忘れた場合は見てみると良さげ
```
$ kubectl explain pods
$ kubectl explain pods.spec.containers   
```
* 下記でコマンドが発行された時のEventを追えたりできる（これは結構みてて面白かった）
```
$ kubectl get event -w -o custom-columns=KIND:.involvedObject.kind,NAME:.metadata.name,SOURCE:.source.component,REASON:.reason,MESSAGE:.message
```
→ `--watch(-w)` でリアルタイムに監視可能、 `--output(-o)` で出力形式を指定。
* `--v=8` とかを指定して `get` や `create` などを実行すると、ログを出力しながら過程を見れる
```
$ kubectl get deploy,rs,pod -v=8
```
$ `template`を使って特定のものだけ出力とかもよく使いそう
```
$ kubectl get rs $(kubectl get rs|grep mattermost|awk '{print $1}') -o template --template='{{.spec.selector.matchLabels}}'
map[app:mattermost-preview pod-template-hash:78bc8686ff]

$ kubectl get po $(kubectl get po|grep mattermost|head -n 1|awk '{print $1}') -o template --template='{{.metadata.labels}}'     
map[app:mattermost-preview pod-template-hash:78bc8686ff]
```
* `--cascade=false`を指定すると子のオブジェクトは削除されない
```
$ kubectl delete deployment mattermost-preview --cascade=false -v=8  
```
* `kubectl cp` でコンテナのファイルをローカルにコピーなどができる
* `kubectl run` で簡易的にコンテナを生成可能（調査用とかに良いみたい）
  * クラスタ内で参照できるServiceの動作確認などに利用可能
* `kubectl port-forward` でServiceを介さずにPodのポートにポートフォワードしてアクセスできる（クラスタ内のエンドポイントの動作確認やエラーのPodをServiceから外して調査などに使える）
* `kubectl cluster-info` でクラスタ情報を確認可能
* `configMap`や`secret`の更新だけでは、Deploymentの更新が走らないので、deploymentを更新（Potテンプレートの更新）するなどは必要だよ！
* 下記のようにすることで、一回ぽっきりのPodを立ちあげ終了後に即削除などができちゃうよ
```
$ kubectl run test1 -i --rm --image k8spracticalguide/busybox:1.28 --restart=Never -- ping -c 10.1.0.122
```
* クラスタ外のアプリケーション接続はExternalNameとか使うのは良さげ。そうすると、接続先が環境ごとで変わっても問題ナッシングfor the people
* コンテナを終了させる前に実施したい処理がある場合には `preStop` hookと呼ばれる設定を `spec.containers.lifecycle.preStop` に指定可能（GracePeriodも重要）
* PodDisruptionBudgetを設定しておくと、動作しているPodの数が指定した数以下にならないようにNodeの退去などができます（ノード停止のブロックとなる設定をしないにように注意）
* PodSecurityPolicyで、Podの設定がみたすべきポリシーを定義すると、それを満たさないPodのデプロイを禁止できるんだってさ
* `kubectl top` コマンドで各リソースの利用状況が見れんだってさ
