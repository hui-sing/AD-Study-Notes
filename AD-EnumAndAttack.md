
## 外部偵查 external reconnaissance

以下是外部偵查應該尋找幾個關鍵項目:

| **項目**             | **描述**               |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **IP 空間**           | 目標的有效 ASN，使用於組織公共基礎設施、雲端存在、託管服務提供商的網段、DNS 記錄等。   |
| **域名資訊**         | 基於 IP 數據、DNS 以及站點註冊訊息。誰管理該域名？是否有與目標相關的子域名？是否有公開訪問的域名服務（如郵件伺服器、DNS、網站、VPN 門戶等）？能否確定有何防禦措施？（如 SIEM、AV、IPS/IDS 等）|
| **架構格式**         | 我們能否發現組織的郵件帳戶、AD 用戶名，甚至密碼策略？有無任何信息能幫助我們構建有效的用戶名列表，用來測試外部服務的密碼噴射、憑證填充、暴力破解等。   |
| **資料披露**         | 查找**公開訪問**的文件（如 .pdf、.ppt、.docx、.xlsx 等），其中包含有助於了解目標的任何訊息。例如，任何公開的文件中包含內部網站列表、用戶元數據、共享信息或環境中的其他關鍵軟硬件（如推送到公開 GitHub 倉庫的憑證、PDF 元數據中的內部 AD 用戶名格式）。|
| **資料洩露**         | 任何公開釋放的用戶名、密碼或其他關鍵訊息，這些訊息可以幫助攻擊者獲得立足點。          |


可以從以下這些工具去找他對應的資源：

| **資源**                     | **範例**    |
|------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ASN / IP 註冊機構**          | IANA、ARIN（美洲地區搜尋）、RIPE（歐洲地區搜尋）、[BGP Toolkit](https://bgp.he.net/)  |
| **域名註冊機構與 DNS**         | [Domaintools](https://whois.domaintools.com/)、[ViewDNSinfo](https://viewdns.info/)、[ICANN](https://lookup.icann.org/en)、針對目標域名或常見的 DNS 伺服器（如 8.8.8.8）進行手動 DNS 記錄請求 |
| **社交媒體**                   | 搜尋 LinkedIn、Twitter、Facebook、當地主要社交媒體網站、新聞文章以及關於該組織的任何相關資訊 |
| **公開公司網站**               | 通常企業的公開網站會嵌入有關的資訊。新聞文章、嵌入式文檔，以及“關於我們”和“聯絡我們”頁面也是重要的訊息來源    |
| **雲端與開發儲存空間**         | GitHub、[AWS S3 bucket 和 Azure Blob 存儲容器](https://buckets.grayhatwarfare.com/)，[google-hacking-database](https://www.exploit-db.com/google-hacking-database ) |
| **洩露數據來源** | 使用 [HaveIBeenPwned](https://haveibeenpwned.com/) 查找是否有企業電子郵件帳戶出現在公開洩露數據中，使用 [Dehashed](https://www.dehashed.com/) 查找具有明文密碼或哈希的企業電子郵件，然後可離線破解這些密碼並嘗試用它們登錄暴露的入口（如 Citrix、RDS、OWA、0365、VPN、VMware Horizon、自定義應用等） |

許多大公司通常會自行託管其基礎設施，並且由於它們的佔地面積如此之大，因此它們將擁有自己的 ASN。對於較小的組織或新興公司來說通常不會出現這種情況。在您研究時，請記住這一點，因為較小的組織通常會將其網站和其他基礎設施託管在其他人的空間中（例如 Cloudflare、Google Cloud、AWS 或 Azure）。了解該基礎設施所在的位置對於我們的測試極為重要。

- [Trufflehog](https://github.com/trufflesecurity/trufflehog) 這個工具看起來很酷
- [linkedin2username](https://github.com/initstring/linkedin2username) OSINT 用

### 小練習 :

去搜尋 inlanefreight.com 可以看到甚麼資料 

1. [BGP Toolkit](https://bgp.he.net/) 
    - IP : 134.209.24.248
    - name server : ns1.inlanefreight.com , ns2.inlanefreight.com
    - mail : mail1.inlanefreight.com
    - ASN : AS14061
    ![image](https://hackmd.io/_uploads/Bk-KeQYE1g.png)
    ![image](https://hackmd.io/_uploads/S1lSGXFN1e.png)

2. viewDNS
    透過剛才獲得的 IP 搜尋可以發現兩個 domain，雖然他在同一個 server 上，但是不代表他們是同間公司，可能是子公司也可能是託管公司負責的其他公司，所以要驗證他們有沒有關係
    ![image](https://hackmd.io/_uploads/S1mGBXKNJl.png)
    
3. whoisDomainTool
    - 發現另一個 domain 跟我們的目標沒有關係
    ![image](https://hackmd.io/_uploads/rypH8mYV1x.png)
    ![image](https://hackmd.io/_uploads/r1dKLQY4yg.png)


4. nslookup
    查看剛剛 BGP Toolkit 發現的子網域，得到兩個 IP
    - 178.128.39.165
    - 206.189.119.186
    ![image](https://hackmd.io/_uploads/SynjPmtEkx.png)

5. google
    搜尋語法 : `filetype:pdf inurl:inlanefreight.com`，尋找公開的 pdf
    - goal 沒什麼東西
    - 下面的檔案有 flag
    ![image](https://hackmd.io/_uploads/Bksu_XY4ke.png)
    ![image](https://hackmd.io/_uploads/rJD5u7tVJl.png)
    
    搜尋語法 : `intext:"@inlanefreight.com" inurl:inlanefreight.com`，尋找相關的電子郵件
    - 發現聯絡資料，可以釣魚
    ![image](https://hackmd.io/_uploads/SJvWFQKEyx.png)
    ![image](https://hackmd.io/_uploads/HJkvtQKEkg.png)
    

## 前期需要列舉的資訊
| **Data Point**              | **Description**                                                                                   |
|-----------------------------|---------------------------------------------------------------------------------------------------|
| **AD Users**                | 我們嘗試列舉有效的用戶帳戶，以便進行密碼暴力破解（password spraying）攻擊。                     |
| **AD Joined Computers**     | 重要的電腦包括域控制器、檔案伺服器、SQL伺服器、Web伺服器、Exchange郵件伺服器、資料庫伺服器等。   |
| **Key Services**            | Kerberos、NetBIOS、LDAP、DNS等關鍵服務。 |
| **Vulnerable Hosts and Services** | 任何可能成為快速獲得勝利的目標主機或服務（即可以輕易攻破並取得立足點的主機）。                 |

通常情況下我們會連接到沒有 GUI 的主機上，我們可以使用tcpdump、net-creds 和 NetMiner 等來執行與 wireshark 相同的功能，可以用來捕獲主機和網路流量類型

:::info
Example : sudo tcpdump -i ens224 
:::

- fping : fping 是一個向網路主機發送 ICMP 回顯探測的程序，類似於 ping，但在 ping 多個主機時效能要好得多
    - fping 標誌：a 顯示活動的目標、s 在掃描結束時列印統計資料、g 從 CIDR 網路產生目標列表，以及 q 不顯示每個目標的結果。
![image](https://hackmd.io/_uploads/r1MpEPF4kl.png)

- namp
-A：這個選項啟用多項進階掃描功能，包括：操作系統檢測：nmap 會試圖確定目標設備所運行的操作系統。
-iL hosts.txt：這個選項告訴 nmap 從 hosts.txt中讀取目標主機列表，而不是在命令行中直接指定目標。hosts.txt 是一個包含多個 IP 地址或域名的文本文件，每行一個目標。這樣可以批量掃描多個主機。
-oN /home/htb-student/Documents/host-enum：這個選項用來將 nmap 的掃描結果輸出到指定的文件。-oN 表示以普通文本格式輸出結果，並將其寫入 /home/htb-student/Documents/host-enum 路徑的文件中。

:::info
sudo nmap -v -A -iL hosts.txt -oN /home/htb-student/Documents/host-enum
:::

![image](https://hackmd.io/_uploads/BkXIIvKNyx.png)

- [Kerbrute](https://github.com/ropnop/kerbrute/releases/tag/v1.0.3) : 用來內部 AD 使用者名稱列舉
    - Kerbrute 在網域帳戶列舉能更隱密，不像 nmap 可能會被發現。它利用了 Kerberos 預驗證失敗通常不會觸發日誌或警報的事實。
    - 使用上會搭配 [Insidetrust](https://github.com/insidetrust/statistically-likely-usernames) 的 jsmith.txt 或 jsmith2.txt 使用者清單
    - 使用 sample `kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users`
    ![image](https://hackmd.io/_uploads/HyZcx_YVkg.png)


:::spoiler Kerbrute 安裝教學

step 1 : `sudo git clone https://github.com/ropnop/kerbrute.git`
step 2 : `sudo make all` 對多個平台和架構進行編譯
step 3 : `ls dist/` 可以看到已編譯的二進位文件
step 4 : `./kerbrute_linux_amd64` 測試是否成功執行
step 5 : `sudo mv kerbrute_linux_amd64 /usr/local/bin/kerbrute` 讓工具可以在任意位置執行
::: 

## Poisoning 怎麼開始

Link-Local Multicast Name Resolution (LLMNR) & NetBIOS Name Service (NBT-NS) 都是用於網路上設備名稱解析的協定，它們在某些情況下可能會影響到網路的安全性。這兩者在本地網路環境中運行，並且通常會被用於不依賴 DNS（域名系統）的情況下進行名稱解析。如果 LLMNR 失敗，將使用 NBT-NS。

- LLMNR 使用 5355 UDP port
- NBT-NS 使用 137 UDP port

當使用 LLMNR/NBT-NS 進行名稱解析時，網路上的任何主機都可以回覆。這就是我們毒害Responder這些請求的地方

| 特徵                    | **LLMNR**                              | **NBT-NS**                               |
|-------------------------|----------------------------------------|------------------------------------------|
| **用途**                | 名稱解析，主要在無 DNS 的情況下使用   | NetBIOS 名稱解析，用於識別設備      |
| **工作原理**            | 使用 multicast 發送查詢請求           | 使用廣播發送查詢請求              |
| **應用場景**            | Windows 網絡，局域網中無 DNS 時使用   | 舊版 Windows 網絡中仍有可能使用      |
| **安全風險**            | 中間人攻擊、名稱解析攻擊              | 名稱欺騙、信息泄漏                   |

### poisoning 流程

:::success
Sample :

1. 主機嘗試連接到位於 \\print01.inlanefreight.local 的印表機伺服器，但意外地輸入了 \\printer01.inlanefreight.local。
2. DNS 伺服器回應，指出該主機未知。
3. 然後，主機向整個本地網路廣播，詢問是否有人知道 \\printer01.inlanefreight.local 的位置。
4. 攻擊者（我們正在Responder運行）回應主機，指出主機正在尋找 \\printer01.inlanefreight.local。
5. 主機相信此回复，並向攻擊者發送包含用戶名和 NTLMv2 密碼雜湊的身份驗證請求。
6. 如果存在適當的條件，則可以離線破解該雜湊值或將其用於 SMB 中繼攻擊。
:::


- Responder 是一款專門用於毒害 LLMNR、NBT-NS 和 MDNS 的工具，具有許多不同的功能。
- Inveigh 是一個跨平台的 MITM 平台，可用於欺騙和投毒攻擊。( windows )
> Responder 使用 Python 編寫，通常在 Linux 攻擊主機上使用，但也有一個可在 Windows 上執行的 .exe 版本。 Inveigh 是用 C# 和 PowerShell（被視為遺留）寫的。這兩個工具都可以用來攻擊以下協定：
> LLMNR、DNS、MDNS、NBNS、DHCP、ICMP、HTTP、HTTPS、SMB、LDAP、WebDAV、Proxy Auth

#### responder 參數解析：
- -A：進入分析模式，允許我們查看 NBT-NS、BROWSER 和 LLMNR 請求，但不會發送任何假回應。
- -wf：啟動 WPAD 騙局代理伺服器，這會讓攻擊者模擬代理伺服器進行流量攔截。
- -f：用來識別遠端主機的操作系統和版本，這對於進一步的攻擊有幫助。
- -v：增加輸出的詳細程度，有助於在遇到問題時診斷錯誤，但會導致控制台顯示大量額外資料。
- -F 和 -P：這些選項可以強制 NTLM 或 Basic 認證，並強制代理認證，但可能會引起登錄提示，因此應謹慎使用。
- -w：啟用內建的 WPAD 代理伺服器，這對於大型組織尤其有效。當瀏覽器的自動檢測設置啟用時，所有通過 Internet Explorer 發出的 HTTP 請求都會被捕獲。

responder 必須以 sudo 權限或以 root 身分執行該工具，並確保攻擊主機上有必須的連接埠可用

:::info
捕獲 hash，如下圖 :

sudo responder -I ens224
:::
![image](https://hackmd.io/_uploads/B1QchdYEke.png)

通常通過 Responder 獲取到的哈希類型都是 NTLMv2，獲得 hash 之後可以用 hashcat 來識別跟爆破密碼，hashcat 的 NTLMv2 編號是 5600

透過有效的網域憑證，也可以使用 CrackMapExec 或 rpcclient 來列舉密碼，如下圖
![image](https://hackmd.io/_uploads/HJkcHFtE1l.png)

## 找出 users
要爆出 user 有以下方式 :
- enum4linux
:::info
enum4linux -U 172.16.5.5  | grep "user:"
:::
![image](https://hackmd.io/_uploads/B1bmwhySJl.png)
- rpcclient
:::info
rpcclient -U "" -N 172.16.5.5
:::
![image](https://hackmd.io/_uploads/Byj8b6JHJg.png)
- crackmapexec
:::info
crackmapexec smb 172.16.5.5 --users
:::
![image](https://hackmd.io/_uploads/SyKdWp1B1x.png)

有帳號密碼後的列舉
![image](https://hackmd.io/_uploads/rJP4kexrJx.png)

- ldapsearch
ldapsearch 我們將需要指定一個有效的 LDAP 搜尋過濾器。
:::info
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "
:::
![image](https://hackmd.io/_uploads/B16lMpkSJg.png)
- windapsearch.py
:::info
./windapsearch.py --dc-ip 172.16.5.5 -u "" -U
:::
![image](https://hackmd.io/_uploads/Sk1EM61SJg.png)

### Kerbrute
使用 Kerbrute 可以列舉有效的 AD 帳戶並進行密碼噴射。
:::success
這個工具利用了 Kerberos 預身份驗證不同的結果來判斷用戶是否存在，這是一種更快速且更隱蔽的密碼噴灑方法。`這種方法不會產生 Windows 事件 ID 4625：登錄失敗`，但是會產生 事件 ID 4768: A Kerberos authentication ticket (TGT) was requested。該工具向域控制器發送TGT請求，如果 KDC 回應錯誤“PRINCIPAL UNKNOWN”，則該用戶名無效。每當 KDC 要求 Kerberos 預身份驗證時，這表示該用戶名存在，工具將標記為有效。`這種用戶名枚舉方法不會造成登錄失敗，也不會鎖定帳戶。`
:::


使用 jsmith.txt 字典檔來嘗試這種方法，該字典包含48,705個可能的常見用戶名，格式為 flast。GitHub上的[statistically-likely-usernames](https://github.com/insidetrust/statistically-likely-usernames.git) 是進行這種類型攻擊的絕佳資源，該倉庫包含了各種不同的用戶名列表，可以用來通過 Kerbrute 列舉有效的用戶名。

![image](https://hackmd.io/_uploads/rJKY0JxBye.png)


## 找密碼設定策略

以下新的 domain 被創建時預設的密碼策略：

| 策略                            | 預設值 |
|---------------------------------|-------|
| 強制密碼歷史                      | 24 天|
| 最大密碼年齡                      | 42 天|
| 最小密碼年齡                      | 1 天 |
| 最小密碼長度                      | 7|
| 密碼必須符合複雜性要求            | 已啟用 |
| 使用可逆加密存儲密碼              | 已禁用 |
| 帳戶鎖定持續時間                  | 未設置|
| 帳戶鎖定閾值                      | 0  |
| 帳戶鎖定計數器重設時間            | 未設置 |

> 密碼複雜性，這意味著使用者必須選擇包含以下 3/4 的密碼：大寫字母、小寫字母、數字、特殊字元（Password1或Welcome1滿足此處的「複雜性」要求，但仍然是明顯的弱密碼）

- crackmapexec
    :::info
    crackmapexec smb 172.16.5.5 -u avazquez -p Password123 --pass-pol
    :::
    ![image](https://hackmd.io/_uploads/SkVDZllHkl.png)
    :::success
    - 最小密碼長度 : 8
    - 最大密碼長度 : 24
    - 錯誤會被鎖 30 分鐘
    :::

- rpcclient
    :::info
    rpcclient -U "" -N 172.16.5.5
    :::
    ![image](https://hackmd.io/_uploads/HkqNfelrke.png)
    :::success
    密碼最少為8個字元
    :::

- enum4linux
    :::info
    enum4linux -P 172.16.5.5
    :::
    ![image](https://hackmd.io/_uploads/Hyvg7llrke.png)

### Windows

如果組織內有一台可以登入的 windows 主機，可以進去查看它的本地帳戶設置，可以用來猜測整個系統或組織採取一樣的管理策略
:::info
net accounts
:::
![image](https://hackmd.io/_uploads/rJdiBllH1e.png)
:::success
- 密碼永不過期（密碼最長期限設定為無限制）
- 最小密碼長度為 8，因此很可能使用弱密碼
- 鎖定閾值是 5 次錯誤密碼
- 帳戶鎖定 30 分鐘
:::

- PowerView
    :::info
    import-module .\PowerView.ps1
    Get-DomainPolicy
    :::
    ![image](https://hackmd.io/_uploads/B1jDhggB1l.png)



## 密碼噴灑
### Linux
- kerbrute
    :::info
    kerbrute passwordspray -d inlanefreight.local --dc 172.16.5.5 valid_users.txt  Welcome1
    :::
    ![image](https://hackmd.io/_uploads/H1YiYexrJx.png)

- crackmapexec
    :::info
    sudo crackmapexec smb 172.16.5.5 -u valid_users.txt -p Password123 | grep +
    :::
    ![image](https://hackmd.io/_uploads/SJk19egHkx.png)

    如果從本機 SAM 資料庫找到本機管理員帳戶的 NTLM 雜湊，就可以在整個子網路（或多個子網路）上噴射 NT 哈希，以尋找具有相同密碼集的本機管理員帳戶。在下面的範例中，我們嘗試使用從另一台電腦檢索的內建本機管理員帳戶 NT 雜湊對 /23 網路中的所有主機進行驗證。該--local-auth標誌將告訴工具僅在每台電腦上嘗試登入一次

    > SAM 資料庫（Security Accounts Manager 資料庫）包含了本機使用者帳戶的資料，包括用戶名、密碼雜湊以及其他安全性資訊。然而，由於 SAM 資料庫包含敏感資料，它是受到保護的，只有具有管理員權限的使用者或特定系統服務才能訪問它。

    :::info
    sudo crackmapexec smb --local-auth 172.16.5.0/23 -u administrator -H 88ad09182de639ccc6579eb0849751cf | grep +
    :::
    ![image](https://hackmd.io/_uploads/HJ5oixgByx.png)

### Windows
- DomainPasswordSpray.ps1
    如果我們通過網域身份驗證，該工具將自動從 Active Directory 產生使用者列表，查詢網域密碼原則，並在一次鎖定嘗試中排除使用者帳戶
    
    以下範例由於主機已加入網域，因此我們跳過該 -UserList 標誌並讓該工具為我們產生一個清單。我們將提供該 Password 標誌和一個密碼，然後使用該 -OutFile 標誌將輸出寫入檔案以供以後使用   
    :::info
    - Import-Module .\DomainPasswordSpray.ps1
    - Invoke-DomainPasswordSpray -Password Welcome1 -OutFile spray_success -ErrorAction SilentlyContinue
    :::
    ![image](https://hackmd.io/_uploads/r1f3pxxHJx.png)

### detect
- Domain Controller 的安全性日誌中，事件 ID 4625：帳戶在短時間內無法登入的許多執行個體可能表示存在密碼噴射攻擊
- 事件 ID 4771: Kerberos pre-authentication failed，這可能表示 LDAP 密碼噴射嘗試

## 列舉更多內容

有使用者憑證才能比較好的列舉想要的東西

- 值得注意的 ACE


| ACE                 | 解釋   | 
| ------------------- | ------|
| GenericWrite | 允許我們對目標物件的非保護屬性進行寫入操作。 | 
| GenericAll   | 該權限賦予我們對目標物件的完全控制權 | 
| AddSelf      | 可利用這一權限將自己添加到有權限的群組中，從而提升自己的權限 | 
|ForceChangePassword | 讓我們有權在不知道使用者密碼的情況下重置使用者的密碼   | 


### Linux
crackmapexec ( CME )
:::success
crackmapexec smb -h : 重點如下

- -u Username The user whose credentials we will use to authenticate
- -p Password User's password
Target (IP or FQDN) Target host to enumerate (in our case, the Domain Controller)
- --users Specifies to enumerate Domain Users
- --groups Specifies to enumerate domain groups
- --loggedon-users Attempts to enumerate what users are logged on to a target, if any
:::

找使用者 :
note : 過濾掉badPwdCount屬性大於 0 的所有用戶，不要被鎖了。
:::info
sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --users
:::
![image](https://hackmd.io/_uploads/SJen4ZeH1g.png)


找群組 :
:::info
sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --groups
:::
![image](https://hackmd.io/_uploads/B1B2HZxBJx.png)

查看特定主機，看有誰登入
:::info
sudo crackmapexec smb 172.16.5.130 -u forend -p Klmcargo2 --loggedon-users
:::
![image](https://hackmd.io/_uploads/HJ3X8-grkg.png)上面寫 pwn3d 表示這台主機在我這個憑證下是可用的 


**RPCClient** 列舉 user 資訊
![image](https://hackmd.io/_uploads/r1qKwZxH1l.png)

### Windows

使用 ActiveDirectory PowerShell 模組
:::info
- Import-Module ActiveDirectory
- Get-Module
:::
![image](https://hackmd.io/_uploads/SyXgtZlSkl.png)

列舉有關網域的一些基本資訊
:::info
Get-ADDomain
:::
![image](https://hackmd.io/_uploads/SJ1mY-eBkl.png)

列舉使用者資料
:::info
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
:::
![image](https://hackmd.io/_uploads/HyF_qWeHye.png)
-  Get-ADUser 用來檢索、篩選和顯示指定的使用者資訊。
-  -Filter {ServicePrincipalName -ne "$null"} 篩選出擁有 ServicePrincipalName (SPN) 的使用者帳戶
-  -Properties ServicePrincipalName 要顯示 ServicePrincipalName 屬性

列舉群組
:::info
Get-ADGroup -Filter * | select name
:::
![image](https://hackmd.io/_uploads/SyRKiZgSyg.png)

列舉特定群組詳細資訊、人員
:::info
Get-ADGroup -Identity "Backup Operators"
Get-ADGroupMember -Identity "Backup Operators"
:::
![image](https://hackmd.io/_uploads/ryXsiZlS1g.png)
![image](https://hackmd.io/_uploads/SynCiblr1l.png)

