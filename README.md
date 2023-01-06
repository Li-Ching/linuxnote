# linuxnote
12/30/2022

上次的點名作業

把useradd放在if裡面比較好 可讀性會比較好
數值比較如果要用test要用不能用== 要用 -eq 課本p8-7
test n1 -eq n2 or [ n1 -eq n2 ]


下禮拜考一題實作 放在期末考成績 跟新增使用者有關

課本

考試內容
9.1.3不教(sed awk)
9.2.3的read -p不講
9.2.5不講
While略

10.3不教

只會講11.1.4 11.2 11.3

12不講

p9-2,3,4,5
正規表示法
^word 搜尋每行開頭的第一個字 如果有此字串整行都會顯示(有行號的話也會)
Word$ 搜尋每行結尾
. 一定要有一個任意字元 空白字元也算
\ 特殊字 ex.\. \^ \$ \’
* 重複前一個的字符(0~無限多個) ex. ess*  es ess esss …
[list] 只有能顯示[]裡面的其中一個 一定要有一個
[n1-n2] 結果顯示某個範圍的字元 ex.[A-Z]
(不考) [^list] 反向 不能顯示[^]裡面的任何一個
(不考) \{n,m\} 重複這個東西的前一個字符n次或m次

P9-5頁範例 會考
1.	$ grep‘http’/etc/services (| head 顯示前10行就好)
2.	$ grep ‘^http’ /etc/services
3.	$ grep ‘^http[ s]’ /etc/services 
([ s]意思是空格或s其中一個 可讀性差一點)
Or
 $ grep '^http[[:blank:]s]' /etc/services
([:blank:]是空白鍵或是tab鍵)
4.	$ grep '^http[[:blank:]s][[:blank:]]' /etc/services
([:blank:]是一個空白字元 所以外面要再加一個[]才會是選字元)
5.	$ grep '^http.*80' /etc/services
.  任意字元 ; * 重複前一個~無限次
6.	$ grep '\* ' /etc/services (*是特殊字元要加\ 只寫*可以成功但不好)
7.	$ grep '[[:alpha:]]\*' /etc/services ([:alpha:]英文不分大小寫)
8.	$ grep '[0-9][A-Z]' /etc/services
Or
$ grep ‘[[:digit:]][[:upper:]]’/etc/services
(特殊字元會考 p9-4下半部&p9-5上半部)
9.	$ grep ‘^[[:digit:]][[:upper:]]’/etc/services
10.	# find /etc | grep '\.conf$’
11.	# find /etc | grep '\.conf$' | grep [[:upper:][:digit:]]
or
$ find /etc 2>/dev/null | grep '\.conf$' | grep [[:upper:][:digit:]]
(用一般使用者可能會跳錯誤訊息 所以可以先把錯誤訊息丟黑洞)


P9-13 外帶參數
$#  抓程式後面接幾個參數
if [ $# -eq 1 ] then… 判斷是否只接一個參數
$*  ifs (先不用管)
$@ (先不用管)
$1 第一個參數

$? 這個指令有沒有執行成功(系統存結果在這邊)


Ch10是考試重點
P10-2 
UID是0是系統管理員 其他不一定
/etc/passwd /etc/shadow /etc/group(新增使用者會異動的檔案)


P10-6
GROUP=100不需要改(是公開群組的意思)
HOME不想在/home裡面可以改
HOME下面三個想改可以改
SKEL如果使用者家目錄 在剛開始創建時內容要放不同的 可以加在這裡
(/etc/skel新增使用者會參考 此目錄內容的東西將為新增使用者之後的家目錄內容)

P10-7
/etc/default/useradd /etc/login.defs(裡面的值是新增使用者會參考)

PASS_MAX_DAYS 多久要改密碼 設99999相當於永久不用改
PASS_MIN_DAYS 多久不能改密碼 公用或專案就會需要不能改
PASS_MIN_LEN 密碼最短長度 

UMASK也可以改  10.1.4會講


10.1.3大量建帳號
參考上週的練習

10.1.4
P10-10
Umask為拿掉不想給予的預設權限
Root的umask預設為022, 一般使用者的umask預設為002

# umask (查看自己umask數字)

新增一個目錄 會從777減掉umask數字
ex. umask是022,建目錄後預設為755
[root@PCCU1 ~]# umask
0022
[root@PCCU1 ~]# mkdir xxx
[root@PCCU1 ~]# ls -ld xxx
drwxr-xr-x. 2 root root 6 Dec 30 13:58 xxx

新增一個檔案 會從666減掉umask數字
ex. umask是022,建目錄後預設為644
[root@PCCU1 ~]# touch aaa
[root@PCCU1 ~]# ls -ld aaa
-rw-r--r--. 1 root root 0 Dec 30 13:59 aaa

10.1.5
P10-11任務一 (下禮拜考試考類似的)
參考答案在P10-12上半部

P10-12任務二 (下禮拜考試考類似的)
Passwd=$(openssl rand -base64 6) 可以取得一個隨機八位數密碼 要記得存檔案不然會找不到
參考答案在P10-13

cat /dev/null > mailuserpw.txt放在最前面,可以清掉當初此檔案本身的內容,之後新增的使用者資料才不會混在之前的內容後面
chage -d 0 帳號 要記得寫

P10-13任務三
useradd -u 399 -g users user 指定UID及初始群組

P10-14任務四
非/tmp or /dev/shm這種1777使用SBIT的目錄
是2770使用SGID的專案目錄


10.2
Sudo 可以讓有權限的人使用自己的密碼登入root,且可以將使用root時做了什麼事記錄下來
初學常使用visudo

Visudo當中的wheel群組是可以使用sudo功能的群組
/wheel 搜尋到wheel的地方可以看到上面有root ALL=(ALL) ALL
在vi裡面 yyp複製一行root ALL=(ALL) ALL 改成 e902 ALL=(ALL) ALL
(增加e902 成為可以使用自己密碼登入root的人(可以執行sudo的使用者))

grep wheel /etc/group會顯示wheel這個群組裡的使用者 是可以使用自己密碼登入root的人(可以執行sudo的使用者) 但很危險因為權限很大(?)

習慣使用nano的人執行nano /etc/sudoers也可以改 但改錯不會有提示 visudo會有提示

Ch13
P13-12例題
systemctl status httpd 看有沒有網際網路連線(檢查目前狀態)
systemctl start httpd馬上啟動
systemctl enable httpd 下次開機自動啟動
systemctl stop httpd 關閉

httpd  /var/www/html

在/var/www/html/index.html改成自己要的東西
Ex. echo 1_A9210256 > /var/www/html/index.html
在瀏覽器打http://10.0.2.44(從ifconfig 找自己的ip)可以看到1_A9210256

systemctl start firewalld 開啟防火牆
systemctl stop firewalld 關閉防火牆
(提到還沒講)
