#Easy #Forentics #picoGym_Exclusive
# Overview
Diskイメージ(.ddファイル)からFlagを掴む
![[Pasted image 20250528090640.png]]

# How to Capture the flag
1. .ddファイルの解析
   展開してもいいが私は`strings`コマンドを利用して中身を見る
2. `strings disko1.dd | grep "picoCTF"`でフラグを掴む
   ```sh
strings disko-1.dd | grep "picoCTF"
# picoCTF{1t5_ju5t_4_5tr1n9_be6031da}
   ```
   Gotcha!
   フラグが来ました