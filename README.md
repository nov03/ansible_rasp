# ansible_rasp
# 家で立てたマシンにansibleを流したいから作成。

# initializer.ymlはansibleのベストプラクティスのディレクトリ構成を作ってくれる。
# ansible-playbook -i localhost, -c local initializer.ymlで実行
# 参考：https://qiita.com/n-funaki/items/0156fd038df1787cae77


ansibleを使うまでのコマンド

※redhatリポジトリから落とすansible本体インストール
sudo apt install -y epel-release
sudo apt install -y ansible

※ansibleでssh接続時のパスワード認証に必須パッケージ
sudo apt install sshpass



使い方：
ドライラン実行
ansible-playbook -i inventories/rasp/hosts ./site.yml -C


ディレクトリ構成について
参考：https://qiita.com/makaaso-tech/items/0375081c1600b312e8b0

group_vars/
グループ毎の変数を定義するのに使います、role ではありませ＃ん、role 毎の変数は roles/{role_name}/vars/ を使います。例えばロケーション毎に異なる変数など。ファイル名はインベントリファイルで設定するグループ名です。インベントリファイルにグループ変数を書くことも可能です

host_vars/
ホスト毎の変数を定義するのに使います。group_vars と同じくファイル名はインベントリファイルに設定したホスト名です。インベントリファイルの中でホスト名の右に並べて書くこともできます

sites.yml
ansible-playbook コマンドに渡す大元 (root) の playbook ファイルです

roles/
このディレクトリの下に role (役割) 毎のディレクトリを作成し、それぞれの role にさらに files/, handlers/, tasks/, templates/, vars/, defaults/, meta/ というサブディレクトリを作成します (その role で使うもののみ)

roles/*/files/
当該 role の task で使うファイル関連モジュール (copy) が src として使うファイルの置き場所。任意のファイル名で置きます

roles/*/handlers/
設定変更後にサービスの再起動をさせたりする場合に、notify という定義で処理を呼び出しますが、その呼び出されるハンドラをここで定義します。main.yml というファイルで作成しますが、include という定義で複数ファイルをそこから読み込ませることが可能です

roles/*/tasks/
何かをインストールしたり、ユーザーを作成したりする task 定義のファイルをここに置きます。handlers と同じように main.yml というファイルが起点となります

roles/*/templates/
template モジュールで利用するテンプレートファイルを置きます。このモジュールでは Jinja2 (神社) というテンプレートエンジンが使わ>れていて .j2 という拡張子を使います

roles/*/vars/
role 毎に設定する変数を定義するファイルを置きます。handlers や tasks と同じく main.yml ファイルが起点となります

roles/*/meta/
role の依存設定を書きます。A という role が B という role に依存しているなら roles/A/meta/main.yml に次のように書きます

roles/*/defaults/
当該 role で使う変数の default 値を設定します。他で設定されていなければここで設定した値が使われます。main.yml に書きます。この>変数が一番低い優先度です 
要素で定義した変数は要素の集合で定義した変数より強い
タスク > vars > 処理(Play) > Playbook > インベントリ > デフォルト
変数の詳細な優先度は下記を参照

優先度高
- extra vars
- タスクのvars
- ブロックのvars
- ロールのvars/includeした変数ファイル
- set_facts
- 処理でregisterした変数
- 処理内vars_filesで指定のファイルで定義された変数
- 処理内vars_promptでプロンプトから渡された変数
- 処理内のvarsで定義された変数
- ファクト(setupで拾ってきた値)
- Playbookで定義されたhost_vars
- Playbookで定義されたgroup_vars
- インベントリで定義されたhost_vars
- インベントリで定義されたgroup_vars
- インベントリで定義された変数
- ロールのデフォルト(/roles/*/defaults/main.yml)
優先度低
参考：https://qiita.com/KeijiYONEDA/items/721407cbe418b3d532ed