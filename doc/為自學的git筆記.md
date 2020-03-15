# 為自己學的git操作筆記

1. 建立Github帳號

    到[github網站](https://github.com/)申請帳號，github是一個git的託管網站，讓開發者可以將自己的專案儲存於網路上．或者你有私人的gitlab也可以使用。
    ![](https://i.imgur.com/NJol00N.png)

3. 建立repository

    ![](https://i.imgur.com/hOau0Aq.png)
    ![](https://i.imgur.com/uNzTDHR.png)
4. 遞交開發端的SSH public key

    因為要讓 Github 知道是我們這台電腦上傳的專案，所以我們要和 Github 之間建立一種權限，這個權限就是使用 ssh 通訊協定時的 public key，我們給 Github 我們本機的 ssh public key，告訴  Github 拿著這個 key 的電腦才是這個帳號的使用者， 如此一來就只能由這台電腦進行管理，才不會造成其他人來我們的帳號亂上傳東西的窘境。

*     首先，點選右上角的
    ![](https://i.imgur.com/YFYhhpo.png)
*     進入帳號設定頁面，在頁面的左下角點選 “SSH keys”到SSH keys新增頁面
    ![](https://i.imgur.com/el8eZaq.png)
    ![](https://i.imgur.com/8dwkplr.png)

    
5. 產生SSH key

    在Terminal輸入輸入指令ssh-keygen -t rsa -C "你的Github信箱"
    
    `ssh-keygen -t rsa -C "your_email@example.com"`
    
    再來他會詢問幾次問題：
    
    `Enter file in which to save the key (/Users/[you pc]/.ssh/id_rsa): [Press enter]`
    
    直接按enter就好。 預設是/Users/[you pc]/.ssh/id_rsa產生。
    
    `Enter passphrase (empty for no passphrase): [Type a passphrase]`
    `Enter same passphrase again: [Type passphrase again]`
    
    輸入一個 passphrase，如不想輸入直接按下 Enter 鍵即可。
    這樣就完成了，接著輸入sudo cat ~/.ssh/id_rsa
    ![](https://i.imgur.com/gJ1ya1f.png)
    將這段複製起來

6. 到Github網站加入你的SSH key

    title輸入一個你可以讓自己知道的名稱（例如：偉博的筆電、台積電公司的電腦
    key直接貼上你的剛剛複製的
    ![](https://i.imgur.com/TtLynJM.png)
    接著案Add key就完成拉！！
7. 測試有沒有綁定

    在Terminal輸入 ssh -T git@github.com
    
    `ssh -T git@github.com`
    
    你會看到這個 warning:
    
    `The authenticity of host 'github.com (207.97.227.239)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no) ?`

    然後輸入"yes"
    你就會看到Hi [你的名字]! You've successfully authenticated, but GitHub does not provide shell access.如下圖：
    ![](https://i.imgur.com/kz1dyDa.png)
    這樣就成功囉!


---

# Clone 其他使用者的專案

clone 是一個較為特殊的稱呼方式，意思是將遠端的 repository 複製一份到本機。在本機的 repository 可以自由修改/實驗，或是針對專案所需要的改善發送 patch 給原 repository，進而貢獻此專案。

如果你有寫入權限的話 (被加入成 “Collaborators”)，就可透過 SSH 通訊協定，以 git 去 “clone” 下來：

`git clone git@github.com:使用者名稱/專案名稱.git`

如果沒有寫入權限 (Collaborators) 的話，因為這個專案是公開的，所以你還是可以用 Git 通訊協定 clone 下來：

`git clone git://github.com/Username/repository.git`

若因遭逢防火牆問題 (如成功大學校園環境)，請改用 HTTPS 通訊協定：

`git clone https://github.com/Username/repository.git`

Pull: 從遠端更新

`git pull`

或

`git pull origin master`

實際作用是先 git fetch 遠端的 branch，然後與本地端的 branch 做 merge，產生一個 merge commit 節點

Push: 將 Commit 送出去

`git push`

或

`git push origin master`

實際的作用是將本地端的 master branch 與遠端的 master branch 作 fast-forward 合併。如果出現 [rejected] 錯誤的話，表示你必須先作 pull。



---

# 或是使用其他帶使用介面的軟體。
    