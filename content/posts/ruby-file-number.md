---
title: "Rubyでファイル名に連番をつける（Windows環境、exe化対応）"
date: 2019-08-23T03:38:05+09:00
draft: false
toc: false
images:
  - https://picsum.photos/1024/768/?random
tags:
  - ruby
---

## 環境

Ruby2.5.0  
Windows10  

## 参考にしたもの

WindowsにおけるRubyの環境構築は下記を参考  
少し試行錯誤した気がします。  
https://qiita.com/jnchito/items/08b5be458134073c60e3  

作成したコードをexe化するのには下記を参考  
http://d.hatena.ne.jp/mirichi/20140323/p1  

PowerShellについては下記を参考  
http://blog.shibata.tech/entry/2015/11/18/185108  

コードの内容については下記を参考  
https://qiita.com/kaway/items/ad84d27522c0ee9e8172  
https://www.gesource.jp/weblog/?p=444  

## 動機

Windows環境で、フォルダ内にある全てのファイルに連番をつける機能がほしい状況になりました。そのため、Windows環境のRubyを使用しています。環境構築などが少し面倒ですが、Rubyでコードを書くのが好きなため、Rubyで記述しました。また、他の人も使用できるようにexe化にも対応しました。  

## 作成したコード

~~~ruby:add_serial_num_v2.rb
require 'fileutils'

print "Folder: "
# フォルダ名を入力 （例）C:\hogehoge
dir_name = File.expand_path(gets.chomp)

# フォルダ内にあるファイルに連番をつける
Dir.glob("#{dir_name}/*").each.with_index(1) do |path_name, index|
  file_name = File.basename(path_name)
  new_name = "#{"%03d" % index}_#{file_name}"
  puts new_name
  FileUtils.mv("#{dir_name}/#{file_name}", "#{dir_name}/#{new_name}")
end

puts "\nFinish!"

~~~

## 実行例

~~~PowerShell:実行例（コマンドプロンプトやPowerShellで実行）
> ruby add_serial_num_v2.rb
Folder: C:\hogehoge
001_bar.docx
002_foo.docx
003_foobar.docx
004_fuga.docx
005_fugafuga.docx
006_hoge.docx
007_hogehoge.docx
008_ふが.docx
009_ほげ.docx
010_ほげほげ.docx

Finish!
~~~

## つまった箇所

下記部分に少しつまりました。

~~~ruby
# フォルダ名を入力 （例）C:\hogehoge
dir_name = File.expand_path(gets.chomp)
~~~

↑を↓のようしてしまうと、windows環境だとパス名取得がうまくできないみたいです。  

~~~ruby
dir_name = gets.chomp
~~~

## exe化

RubyInlineとOcraをgemでインストールします。  
※RubyInline必要だったのかは不明…  

~~~PowerShell:コマンドプロンプトやPowerShellで実行
> gem install RubyInline
> gem install ocra
~~~

↓はadd_serial_num_v2.rbがあるフォルダで実行してください。  

~~~PowerShell:コマンドプロンプトやPowerShellで実行
> ocra add_serial_num_v2.rb
~~~

私の環境では、特にエラーが起こることなく**add_serial_num_v2.exe**が出来上がりました。これで、Rubyの環境を構築していない人にも配布可能となりました。  

## ちなみに…

PowerShellで同じようなことをしようと思ったら、下記の1行ですみます😅  
連番つけたいファイルのあるディレクトリで実行してください。  

~~~PowerShell:PowerShellで実行
> ls | sort Name | % {$i = 1} { $NewName = "{0:000}_{1}" -f $i, $_.Name ; mv $_.Name $NewName; $i++ }
~~~

## まとめ

Windows環境でも、簡単な作業ならRubyで色々できそうです。  
ただし、Windows環境である必要がない場合は、Windows Subsystem for Linuxなどで作業した方が楽だと思います。  
