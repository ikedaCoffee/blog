---
title: "RailsのシステムテストをCircleCIで自動で実行する"
date: 2019-11-02T15:09:12+09:00
draft: false
toc: false
images:
  - https://picsum.photos/1024/768/?random
tags:
  - Rails
  - Minitest
  - Docker
  - CircleCI
  - WSL
---

## Windows10でRailsのシステムテストの環境構築

最近職場で、社内用Webアプリのテストコードを書いている。Railsで作られていて、テストフレームワークはデフォルトのMinitestを使用している。前の職場ではテストコードを書く文化がなく、テストコードを記述させてもらえてありがたく思っている。

今はシステムテストを書いていて、Seleniumを使った自動UIテストをしている。ブラウザをテストコードで自在に操るのはなかなかに楽しい。

Windows10 Proで開発をしていて、WSL上にRails環境を構築して作業をしていた。特に困ることはなかったのだけれど、システムテストだけはWSL環境が原因で躓いた。

当たり前だけど、システムテストにはブラウザが必要だ。でも、WSL内にはブラウザが入っていない。WSL内にchromeをインストールしようと試みたけど、動作が安定せずに断念した。システムテストの環境構築がWSLだとできない...。さて、どうしたものか。なかなかに悪戦苦闘したので、その解決策を記録しておく。

解決策は2つ記述した。解決策1は、WSL環境のまま、Windowsにインストールされているchromeを使用する方法だ。解決策2はWSLを使用せず、Docker for Windowsで開発環境を構築する方法である。

## Windowsにインストールされているブラウザを使用する

WSLを使用し続けるのであれば、Windowsにインストールされているブラウザを使用するのがよいと思う。自分がやった作業は以下の通り。使用するブラウザはChromeなので、他のブラウザを使用する場合は知らない。けど、まあ、似たような設定でいけると思う。

環境: WSL (Ubuntu 18.04) / Rails: 5.2系 / Ruby: 2.5系

1. Chromeを開いてバージョンを調べる。

   1. 右上の点が3つ縦に並んでいるボタンをクリック。
   2. ヘルプをクリック
   3. Google Chromeについてをクリック
   4. `バージョン: 78.0.3904.87` などと書いてあるので、このバージョン (78) を覚えておく。

2. バージョンにあったChromeDriverをインストールする。

   [こちら](https://chromedriver.chromium.org/downloads)からインストールできる。最初の二桁、つまり`バージョン: 78.0.3904.87` なら、78のもので新しいものを選べばよい。

3. インストールしたchromedriver.exeを適当な場所に置く。

   例えば、`C:\Program Files\chromedriver_win32\chromedriver.exe`

4. `/test/application_system_test_case.rb` でchromedriverの場所を指定する。

   3の場所に置いたなら、↓のように記述すればよい。

   ```ruby
   require 'test_helper'
   # 下記を追加
   Selenium::WebDriver::Chrome::Service.driver_path = '/mnt/c/Program Files/chromedriver_win32/chromedriver.exe'

   class ApplicationSystemTestCase < ActionDispatch::SystemTestCase
     driven_by :selenium, using: :chrome, screen_size: [1400, 1400]
   end
   ```

5. Capybaraのバージョンを3.17以上にする。

   Gemfileを開いて、`gem 'capybara', '~> 2.13'`などと記述されている箇所を`gem 'capybara', '~> 3.17'`に変更する。

   ```ruby
   - gem 'capybara', '~> 2.13'
   + gem 'capybara', '~> 3.17'
   ```

   変更したら、忘れずに`bundle install`をすること。

以上である。これで、システムテストを記述して、`rails test:system`と実行すれば、chromeが立ち上がって、自動でUIテストがはじまる。Seleniumを使ったことがなかった自分のような人にとっては、なかなかに面白いものがみれると思う。

## Docker for Windowsで開発環境を構築する

ローカルの環境だけでテストをするのであれば、Windows環境のブラウザを使ってテストをすれば良い。だが、CircleCIなどで自動でシステムテストを行ってチェックをしたいという場合、どうすればよいのだろうか？Docker使えばいけるよとアドバイスをもらったので、そうすることにした。Dockerのイメージで、ブラウザが入ったものを使用できるとのこと。

1. Docker for Windowsをインストールする

   Docker for Windowsのインストールは[公式サイト(英語)](https://docs.docker.com/docker-for-windows/install/)を参考にすればいけると思う。簡略化すると、以下の通り。

   1. Windows10 Proの設定で、Hiper-Vを有効化する。
   2. [こちら](https://hub.docker.com/?overlay=onboarding)からDocker Desktop for WIndowsをインストール。

   英語だけど、読み進めながら進めるのが無難かと思う。

2. Docker Hubから`CircleCI/ruby`イメージを探し出す

   1. [Docker Hub](https://hub.docker.com/)にアクセスする。
   2. `circleci/ruby`と検索する。もしくは、直接[こちら](https://hub.docker.com/r/circleci/ruby)にアクセスする。
   3. `Tags`を選択
   4. Tagsの検索部分で、`2.5.7-node-browsers`と検索する。`2.5.7`は自分が使用しているRubyのバージョン。`node`はNode .jsを含める場合。Rails使う場合はこれを含めておいた方が無難そう。そして大事なのが`browsers`の部分。これだけでChromeやFirefoxのブラウザ環境が入った環境を構築できる。
   5. 今回、自分の使用するイメージは`circleci/ruby:2.5.7-node-browsers`ということになる。

3. Dockerfileおよびdocker-compose.ymlの作成

   自分の場合、すでに作成済のrailsアプリを動かすために、railsアプリ配下に下記のような構成でDockerfileとdocker-compose.ymlを作成した。どちらもrailsのルートディレクトリ直下に置いた。

   - Dockerfile

   ```dockerfile
   FROM circleci/ruby:2.5.7-node-browsers
   RUN apt-get update -qq && apt-get install -y build-essential libpq-dev
   RUN mkdir /railsアプリ名
   WORKDIR /railsアプリ名
   COPY . /railsアプリ名/
   RUN gem install bundler
   RUN bundle install
   ```

   - Docker-compose.yml

   ```yaml
   version: '3'
   services:
     db:
       image: postgres:12.1
       volumes:
         - postgres_data:/var/lib/postgresql/data
     web:
       build: .
       volumes:
         - .:/railsアプリ名
       ports:
         - "3000:3000"
       depends_on:
         - db
   volumes:
     postgres_data: {}
   ```

4. railsのルートディレクトリ直下で下記を実行

   ```shell
   > docker-compose build
   > docker-compose up -d
   > docker-compose exec app rails db:create db:migrate
   ```

5. 対象のコンテナにアタッチしてシステムテストを試してみる

   `docker ps`コマンドで、起動中のコンテナを確認すると、下記のような状態になっているはず。

   ```shell
   > docker ps
   ... NAMES
   ... railsアプリ名_web_1
   ... railsアプリ名_db_1
   ```

   下記を実行してコンテン内に入る。

   ```shell
   > docker exec -it railsアプリ名_web_1 /bin/sh
   ```

   Railsアプリのルーディレクトリに移動して、システムテストを試す。

   ```shell
   $ rails test:system
   ```

   テストが実行されればOK。
