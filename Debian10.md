# command_alias

## All User

```
~# vi /etc/profile.d/command_alias.sh
# ファイル新規作成
# 追加したいエイリアスを追記
alias ll='ls $LS_OPTIONS -l'
alias l='ls $LS_OPTIONS -lA'
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
# 再読み込み
~# source /etc/profile.d/command_alias.sh
```

# サービスの管理
```
# 現在起動しているサービスの一覧を表示 (--all を付けると全サービスがリストされる)
~# systemctl -t service
# サービスの起動設定の一覧を表示
~# systemctl list-unit-files -t service
```

```
~# systemctl stop apparmor
~# systemctl disable apparmor
```

# UFW
```
ファイアウールが起動しているかどうかの確認 ufw status
ファイアウォールを起動（有効化）させる ufw enable
ファイアウォールを停止（無効化）させる ufw disable
登録されているルールの確認 ufw status
通信を許可するルールの登録 ufw allow
ufw allow ポート番号/プロトコル（tpc|udp）
通信を遮断するルールの登録 ufw deny
登録されているルールの削除 ufw delete
```

# システム最新化
```
# リスト最新化
~# apt update
# システム最新化
~# apt -y upgrade
```

#Vim の設定
```
~# apt -y install vim
	Vim の設定です
全ユーザー共通で適用する場合は [/etc/vim/vimrc] に記述
~# vi ~/.vimrc
" vim の独自拡張機能を使う(viとの互換性無し)
set nocompatible
" 文字コードを指定する
set encoding=utf-8
" ファイルエンコードを指定する
set fileencodings=utf-8,iso-2022-jp,sjis,euc-jp
" 自動認識させる改行コードを指定する
set fileformats=unix,dos
" バックアップを取得する
" 逆は [ set nobackup ]
set backup
" バックアップを取得するディレクトリを指定する
set backupdir=~/backup
" 検索履歴を残す世代数
set history=50
" 検索時に大文字小文字を区別しない
set ignorecase
" 検索語に大文字を混ぜると検索時に大文字を区別する
set smartcase
" 検索語にマッチした単語をハイライトする
" 逆は [ set nohlsearch ]
set hlsearch
" インクリメンタルサーチを使用する(検索語の入力最中から随時マッチする文字列の検索を開始)
" 逆は [ set noincsearch ]
set incsearch
" 行番号を表示する
" 逆は [ set nonumber ]
set number
" 改行 ( $ ) やタブ ( ^I ) を可視化する
set list
" 括弧入力時に対応する括弧を強調する
set showmatch
" ファイルの末尾に改行を入れない
set binary noeol
" 自動インデントを有効にする
" 逆は [ noautoindent ]
set autoindent
" 構文ごとに色分け表示する
" 逆は [ syntax off ]
syntax on
" [ syntax on ] の場合のコメント文の色を変更する
highlight Comment ctermfg=LightCyan
" ウィンドウ幅で行を折り返す
" 逆は [ set nowrap ]
set wrap

```
