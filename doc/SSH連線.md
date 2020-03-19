# SSH連線筆記
## 為什麼ssh比telnet還安全？何謂ssh key？
telnet和ssh是同類型的東西。差別在於，ssh的連線通道是經過加密。ssh使用所謂的public 和 private key的Architecture來做連線通道的加解密。

簡單來說，public 和 private key是2個不同的檔案。如果資訊透過一個key來加密，那麼另外一個key就可以用於解密。而通常public key是給外部系統用，而private key則是自己用，因此才能夠雙方溝通。

為了進一步保護key，設定key的時候通常會在設定一組密碼稱之為passphrase。

通常來說：

public key - 檔名 預設是id_rsa.pub
private key - 檔名 預設是id_rsa

如果上面看不太懂，講白一點就是，ssh key類似一個憑證需要在雙方電腦設定好才能夠溝通。然後passphrase就是密碼。
## 產生SSH key
    
在Terminal輸入輸入指令ssh-keygen -t rsa -C "你的Github信箱"

    ssh-keygen -t rsa -C "your_email@example.com"

再來他會詢問幾次問題：

    Enter file in which to save the key (/Users/[you pc]/.ssh/id_rsa): [Press enter]

直接按enter就好。 預設是/Users/[you pc]/.ssh/id_rsa產生。

    Enter passphrase (empty for no passphrase): [Type a passphrase] Enter same passphrase again: [Type passphrase again]

輸入一個 passphrase，如不想輸入直接按下 Enter 鍵即可。 這樣就完成了。
要設定[連線git參考這篇](https://github.com/WeiBoHaung/Linux-System-core-design/blob/master/doc/%E7%82%BA%E8%87%AA%E5%AD%B8%E7%9A%84git%E7%AD%86%E8%A8%98.md)。


## 如何用config管理多個網站的ssh key和如何不用每一組輸入ssh的Pass Phrase

1. **建立另外一個ssh key**

    在建立Google Cloud Platform用的ssh key之前，先來看一下ssh key的檔名的一些命名規則。

    通常ssh key的檔名前面的部分會是：id_rsa，而在點的後面則會是這個ssh key的用途。因此，假設今天我要建立的ssh key是給Google Cloud Platform用的，我的檔名會變成： id_rsa.gcp。

    有了這個概念之後，可以用下列指令來建立Google Cloud Platform要用的ssh key：
    `ssh-keygen -f id_rsa.gcp -C "weibohuang@github.com"`
    
    將會產生以下的兩個檔案：
        1. id_rsa.gcp
        2. id_rsa.gcp.pub
        
    這個時候在~\.ssh的下面應該就會有兩組key，一組是id_rsa(原本github用的)，一組是id_rsa.gcp(Google Cloud Platform用的)。
    
    下一步就是把產生的id_rsa.gcp.pub的key加入到Google Cloud Platform裏面的允許的ssh key裏面。
    
2. **config檔案的使用**

    當ssh key越來越多，如何控制那裡要用那個key就是config在做的設定，接下來將會介紹如何使用config來方便做ssh連線。
    
     有了兩組key之後，我們會需要有一個東西告訴ssh，當我去github我要用id_rsa，當我去Google Cloud Platform，我要用id_rsa.gcp。而這個檔案，就是所謂的config檔案。
     
     config檔案的每一個hostname其中幾個重要格式如下：
     
        Host alias
        HostName hostname
        IdentityFile ~/.ssh/某一組對應的key
        
    alias ： alias會對到config的hostname來作為實際連線的host。
    hostname : 這個指的是我們連線過去服務的hostname。以我們例子來說，就會有 github.com，或是目標主機位址。
    
    > **請注意config檔案本身的格式**
    > 由於ssh屬於linux類型的工具，因此在檔案格式和line ending上面和windows不同，因此要注意：
    > 
    > 檔案的encoding要用ANSI或者要是utf-8不要有bom
    > 檔案的Line ending要用UNIX格式
    
1.  config檔案的建立
    ![](https://i.imgur.com/OhWb1uR.png)
    可以注意到我的gcp部分是
    ```
    # gcp
    Host  gcp 	 	
    HostName  10.0.0.125	 	
    IdentityFile ~/.ssh/id_rsa.gcp
    ```
    原本我的連線是 ssh weibohuang@10.0.0.125，現在只要輸入ssh weibohuang@gcp，就可以連線到了是不是很方便，不然每一台都要記。

    
## SSH連線出現錯誤 WARNING REMOTE HOST IDENTIFICATION HAS CHANGED

當在使用SSH連線到別台主機時，有時會出現以下錯誤。若是有此錯誤的話。可參考以下解決方式。
錯誤訊息
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:*******************************.
Please contact your system administrator.
Add correct host key in /Users/weibohaung/.ssh/knownhosts to get rid of this message.
Offending ECDSA key in /Users/weibohaung/.ssh/known_hosts:15
ECDSA host key for xxx.xxx.xxx.xxx has changed and you have requested strict checking.
Host key verification failed.
```
1. 把有問題的 xxx.xxx.xxx.xxx KEY刪掉，路徑在警告訊息裡我的/Users/weibohaung/.ssh/knownhosts（每個人不一樣），將檔案打開把錯誤訊息內的xxx.xxx.xxx.xxx的連線刪掉。
2. 將knownhosts其他資料夾備份移到，稍後再回來檢視問題連線，在連線一次會產生新的紀錄黨。
3. 將此有問題的移除，下次登入就可正常
 `ssh-keygen -R 192.168.2.151`