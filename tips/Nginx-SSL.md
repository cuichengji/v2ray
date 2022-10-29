# Nginx : インストール
```
Nginx をインストールします。
~# apt -y install nginx

Nginx の基本設定です。
root@www:~# vi /etc/nginx/sites-available/default
46行目：サーバー名変更
server_name www.srv.world;
~# systemctl restart nginx
```

# UFW
```
ufw allow 80
ufw allow 443
```

# SSL証明書を取得する (Let's Encrypt)
```
証明書を取得するためのクライアントツールをインストールします。
root@dlp:~# apt -y install certbot

 [--webroot] 指定で稼働中 Web サーバーの公開ディレクトリ配下を認証用の一時領域に使用
 -w [ドキュメントルート] -d [証明書を取得したいFQDN]
 FQDN (Fully Qualified Domain Name) : ホスト名.ドメイン名を省略なしで表記
 ドキュメントルートはバーチャルホストで複数のホスト定義がある場合、該当するホスト定義のものを指定
 ドキュメントルート指定の動作としては, 指定したドキュメントルート配下に
 [.well-known] ディレクトリが作成され, 認証用のファイルが自動的,一時的に設置されるのみ
 証明書を取得したいFQDNが複数ある場合は、-d [証明書を取得したいFQDN] を複数指定
 例 : srv.world/dlp.srv.world の二つについて取得する場合
 ⇒ -d srv.world -d dlp.srv.world
root@dlp:~# certbot certonly --webroot -w /var/www/html -d srv.world

 初回のみメールアドレスの登録と利用条件への同意が必要
 受信可能なメールアドレスを指定
Enter email address (used for urgent notices and lost key recovery)

root@mail.srv.world 

<  OK  >           <Cancel>

 利用条件に同意する
Please read the Terms of Service at
     https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf.
     You must agree in order to register with the ACME server at       
     https://acme-v01.api.letsencrypt.org/directory                    

<Agree >           <Cancel>

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/srv.world/fullchain.pem. Your cert will
   expire on 2019-10-23. To obtain a new version of the certificate in
   the future, simply run Let's Encrypt again.
 - If you like Let's Encrypt, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le

 [Congratulations] と表示さえれば成功
 メッセージ中に記載の通り [/etc/letsencrypt/live/(FQDN)/] 配下に証明書が取得されている

 cert.pem       ⇒ SSLサーバー証明書(公開鍵含む)
 chain.pem      ⇒ 中間証明書
 fullchain.pem  ⇒ cert.pem と chain.pem が結合されたファイル
 privkey.pem    ⇒ 公開鍵に対する秘密鍵


作業を実施するサーバーで Webサーバーが稼働していない場合でも、ツールの簡易 Webサーバー機能を使用して証明書の取得が可能です。 いずれにしろ、インターネット側から当作業を実施するサーバーの 80 ポート宛てにアクセス可能であることは前提となります。

 [--standalone] 指定で 簡易 Webサーバー機能を使用
 -d [証明書を取得したいFQDN]
 FQDN (Fully Qualified Domain Name) : ホスト名.ドメイン名を省略なしで表記
 証明書を取得したいFQDNが複数ある場合は、-d [証明書を取得したいFQDN] を複数指定
 例 : srv.world/dlp.srv.world について取得する場合 ⇒ -d srv.world -d dlp.srv.world
root@dlp:~# certbot certonly --standalone -d dlp.srv.world

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/dlp.srv.world/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/dlp.srv.world/privkey.pem
   Your cert will expire on 2019-10-23. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le


取得済みの証明書を更新する場合は以下のように実行します。
 有効期限が 30日未満の証明書を全て更新
 有効期限の残り日数に関わらず更新したい場合は [--force-renewal] を合わせて指定
root@dlp:~# certbot renew

cron でスケジュール実行を設定
 テスト：certbot renew --dry-run
 

vi crontab
0 0,12 * * * certbot renew --force-renewal
```
 

# Nginx : SSL/TLS の設定

```
[2]	Nginx の設定です。
root@www:~# vi /etc/nginx/sites-available/default
# 最終行に追記
# 証明書のパスは取得した自身の環境に置き換え
server {
        listen 443 ssl default_server;
        listen [::]:443 ssl default_server;

        ssl_prefer_server_ciphers  on;
        ssl_ciphers  'ECDH !aNULL !eNULL !SSLv2 !SSLv3';
        ssl_certificate  /etc/letsencrypt/live/www.srv.world/fullchain.pem;
        ssl_certificate_key  /etc/letsencrypt/live/www.srv.world/privkey.pem;

        root /var/www/html;
        server_name www.srv.world;
        location / {
                try_files $uri $uri/ =404;
        }
}

root@www:~# systemctl restart nginx



[3]	HTTP リクエストも全て HTTPS へリダイレクトして Always on SSL とする場合はホスト定義にリダイレクトの設定を追記します。
root@www:~# vi /etc/nginx/sites-available/default
# 80 をリスンしている定義内に追記
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        return 301 https://$host$request_uri;

root@www:~# systemctl restart nginx
```
