# github-sshの設定方法
From https://gist.github.com/g-empr/fc793caf3a0a18c31d8c708787bdf5f0

ついでに複数のGitHubごとのマルチ設定も行う.
## 専用フォルダと鍵の作成
```shell
cd ~/.ssh
ssh-keygen -t rsa
```
質問はEnter*3.
​
​
## GitHubにkey登録
https://github.com/settings/ssh にアクセス.  
`SSH keys`>`New SSH key`をクリックし、Titleに適当な名称、keyに公開鍵を設定.  
公開鍵は下記のように持ってくる.  
Winの場合
```shell
clip < ~/.ssh/id_rsa.pub
```
Macの場合
```shell
pbcopy < ~/.ssh/id_rsa.pub
```
## 接続確認
接続確認してみる.
```shell
ssh -T git@github.com
Hi hogehoge! You've successfully authenticated, but GitHub does not provide shell access.
```
## うまく接続できないとき
パスワードとか聞かれたらうまくいってないのでまず下記コマンドで確認.
```shell
git config remote.origin.url
```
確認したら以下のように設定.
```shell
git remote set-url origin git@github.com:[ユーザID]/[リポジトリ].git
```
`~/.ssh/config`の一行目に別のhostが指定してある場合は以下のように設定.
```shell
git remote set-url origin [Host名]:[ユーザID]/[リポジトリ].git
```
## 複数のsshを設定する
### 各鍵の作成
```fish
cd ~/.ssh/
ssh-keygen -t rsa -C '[対象メールアドレス]' -f ~/.ssh/id_rsa_[固有名] 
```
### 公開鍵のコピー
```fish
pbcopy < ~/.ssh/id_rsa_[固有名].pub
```
### hostsの設定
下記に移動.  
なければ作成する.
```fish
~/.ssh/config
```
下記内容を記述.
```
# 基本的な書き方
Host [ホスト名]
  HostName [各サービスのドメインまたはIPアドレス]
  User [ユーザ名: GitHubはgit、Bitbucketはアカウントのユーザ名(ユーザ名がhelloの場合hello)になる]
  Port 22
  IdentityFile [秘密鍵の場所]
  IdentitiesOnly yes

# 例 個人GitHubの場合
Host github.com
  HostName github.com
  User git
  Port 22
  IdentityFile ~/.ssh/id_rsa_github
  IdentitiesOnly yes

# 例 会社GitHubの場合
Host github.com.company
  HostName github.com
  User git
  Port 22
  IdentityFile ~/.ssh/id_rsa_github_company
  IdentitiesOnly yes

# 例 個人GitLabの場合
Host gitlab.com
  HostName gitlab.com
  User git
  Port 22
  IdentityFile ~/.ssh/id_rsa_gitlab_private
  IdentitiesOnly yes

# 例 会社GitLabの場合
Host git.company.co.jp
  HostName git.company.co.jp
  User git
  Port 2233
  IdentityFile ~/.ssh/id_rsa_gitlab_company
  IdentitiesOnly yes
```
### ホストに接続確認
```fish
ssh -T [ホスト名]

# 例 個人GitHub
ssh -T github.com
# 例 会社GitHub
ssh -T github.com.company
```
### `clone` などでアクセスするリポジトリの表記
```fish
git clone git@[ホスト名]:[サービスのユーザ名]/hogehoge.git

# 例 個人GitHub
git clone git@github.com:username/hogehoge.git
# 例 会社GitHub
git clone git@github.com.company:username/fugafuga.git
```
