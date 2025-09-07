# Fastprefix APIの使い方

この関数は、LuckPerms を利用してプレイヤーの Prefix / Suffix を操作するための API です。
バージョン番号を指定することで処理が分岐し、現在は `v=2` のみサポートしています。

## 関数定義
`function fpapi(v: number, mode: text, p: player, n: text) :: object`

### 引数
- `v` : バージョン番号。現在は `2` のみ対応。
- `mode` : 実行する処理の種類を指定。
- `p` : 操作対象のプレイヤー。
- `n` : 新しい prefix / suffix を指定する場合に使用。不要な場合は空でも可。

## 利用可能なモード
### Prefix 関連
- `prefix_set` : プレイヤーの prefix を新しい値に置き換える。
- `prefix_plus_before` : 現在の prefix の前に文字列を追加する。
- `prefix_plus_after` : 現在の prefix の後に文字列を追加する。
- `prefix_remove` : 現在設定されている prefix を削除する。
- `prefix_get` : 現在の prefix を返します。設定されていない場合はエラーを返す。

### Suffix 関連
- `suffix_set` : プレイヤーの suffix を新しい値に置き換える。
- `suffix_plus_before` : 現在の suffix の前に文字列を追加する。
- `suffix_plus_after` : 現在の suffix の後に文字列を追加する。
- `suffix_remove` : 現在設定されている suffix を削除する。
- `suffix_get` : 現在の suffix を返します。設定されていない場合はエラーを返す。

## 共通
- `remove_all` : プレイヤーの prefix と suffix を両方削除し、変数も空にする。
## 戻り値
処理の結果として以下のいずれかが返されます。

- `success` : 正常に処理が完了した
- `error_missing_argument` : 必須引数 n が指定されていない
- `error_both_same` : 新しい値と現在の値が同じ
- `error_not_found` : prefix / suffix が設定されていない
- `error_not_found_mode` : 存在しない mode を指定した
- `error_not_support_version` : 未対応のバージョン番号を指定した
- `error_not_running_Luckperms` : LuckPerms が導入されていない

## 使用例
### 自分の prefix を変更する
```
command /myprefix <text>:
    trigger:
        set {_r} to fpapi(2, "prefix_set", player, arg-1)
        send "処理結果: %{_r}%"
```

### 管理者が他プレイヤーの suffix を付与
```
command /setsuffix <player> <text>:
    permission: admin.suffix
    trigger:
        set {_r} to fpapi(2, "suffix_set", arg-1, arg-2)
        send "結果: %{_r}%"
```
### プレイヤーの prefix を確認
```
command /checkprefix:
    trigger:
        set {_r} to fpapi(2, "prefix_get", player, "")
        send "あなたの prefix: %{_r}%"
```
### イベント時に全員の prefix と suffix を一括削除
```
command /resetallmeta:
    permission: admin.reset
    trigger:
        loop all players:
            fpapi(2, "remove_all", loop-player, "")
        send "全員の prefix と suffix を削除しました。"
```
### suffix が未設定のプレイヤーを判定
```
command /checkmysuffix:
    trigger:
        set {_r} to fpapi(2, "suffix_get", player, "")
        if {_r} is "error_not_found":
            send "あなたは suffix を持っていません。"
        else:
            send "あなたの suffix: %{_r}%"
```
## 注意事項

この関数は内部的に LuckPerms の `permission set` / `unset` コマンドを実行しています。
データは `{prefix::%uuid%}` および `{suffix::%uuid%}` に保存されます。
prefix / suffix の weight は固定で 1 です。
