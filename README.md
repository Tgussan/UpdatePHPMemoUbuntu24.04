# アップデート前の情報
### wp-core
version: 7.1.4
```
server_architecture: Linux 6.8.0-147-generic x86_64
httpd_software: Apache/2.4.58 (Ubuntu)
php_version: 8.3.6 64bit
```


# アップデート後の情報
### wp-core
version: 7.1.4
```
server_architecture: Linux 6.8.0-147-generic x86_64
httpd_software: Apache/2.4.58 (Ubuntu)
php_version: 8.3.36 64bit
```


# アップデートコマンド

Ubuntu 24.04 LTS（Noble Numbat）環境において、PHP 8.3.6 からセキュリティ修正済みの PHP 8.3.36 へアップデートする手順です。
Ubuntu公式リポジトリではセキュリティパッチがバックポート（パッケージのバージョン番号表記はそのままで修正のみ適用）されるのが一般的ですが、最新のマイナーバージョン（8.3.36）を確実に導入する場合は Ondřej Surý 氏の公式PPA（ppa:ondrej/php）を利用するのが標準的かつ最も安全な手順となります。
1.現在導入されているPHPパッケージのバックアップ・確認:5分。アップデート後の環境差分を防ぐため、現在インストールされているPHP関連モジュール（php8.3-cli, php8.3-fpm, php8.3-mysql など）の一覧を記録します。
`dpkg -l | grep -E "php8.3|php-0" > php_packages_backup.txt`

`php -v`
Verification: カレントディレクトリーに php_packages_backup.txt が生成され、php -v で現在のバージョン（8.3.6）が表示されることを確認します。

2.リポジトリの更新と Ondřej Surý PPA の追加:3分。最新の PHP 8.3.36 パッケージを取得できるよう、PPAリポジトリをシステムに追加してパッケージリストを更新します。
```
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository -y ppa:ondrej/php
sudo apt update
```

Verification: 
sudo apt update の実行結果に [http://ppa.launchpad.net/ondrej/php/ubuntu](http://ppa.launchpad.net/ondrej/php/ubuntu) noble InRelease が含まれ、エラーなく終了することを確認します。

3.PHPパッケージのアップデート:5分。PHP 8.3 関連パッケージを最新版にアップグレードします。
`sudo apt upgrade -y php8.3 php8.3-cli php8.3-fpm php8.3-common`
※ Web サーバー環境（Apache や Nginx + PHP-FPM）に応じて、必要な拡張機能も一括で更新されます。設定ファイルのコンフリクト（php.ini の置換問い合わせ）が表示された場合は、既存の設定を保持する（N またはデフォルト）を選択してください。

Verification: 処理完了後、ターミナルで php -v を実行し、バージョンが PHP 8.3.36（またはそれ以上）になっていることを確認します。4.Webサーバー / PHP-FPM サービスの再起動:2分。変更を反映させるため、Web サーバーおよび PHP-FPM サービスを再起動します。
Apache（mod_php等）を利用している場合:
`sudo systemctl restart apache2`

Verification: sudo systemctl status php8.3-fpm（または apache2）を実行し、ステータスが active (running) になっていることを確認します。
設定ファイルの互換性に関する注意アップデート時に php.ini などの設定ファイルを新しいバージョンで置き換えると、以前設定した memory_limit や upload_max_filesize などのパラメータが初期化される場合があります。
既存の設定ファイルを保持（Keep local version）するか、更新後に設定の再確認を行ってください。
