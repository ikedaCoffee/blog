# Hugoでブログ作成用

## hugoコマンド

詳しくは[こちら](https://gohugo.io/commands/)を参照。[こっち](https://hugo.nakaken88.com/use/basic-hugo-command/)もよくまとまっている。

- Hugoのバージョン確認

  ```shell
  $ hugo version
  ```
  
- ローカルでサーバ立ち上げる。`localhost:1313`で

  ```shell
  $ hugo server
  # draft: trueのものもみるには-Dフラグをつける
  $ hugo server -D
  ```
  
- ビルド

  ```shell
  $ hugo
  ```

- 新しい記事の投稿。ファイル名がurlになることに注意。

  ```shell
  $ hugo new posts/ファイル名.md
  ```

- contentディレクトリから古い記事の削除

  ```shell
  $ hugo --cleanDestinationDir
  ```
