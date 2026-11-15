# Multi-Stage Phishing Attack Chain Investigation Using Splunk

使用 Splunk 進行多階段釣魚攻擊鏈調查

<br/>

---------

<h2>Outline｜調查簡介</h2>

This project demonstrates a structured Security Operations Center (SOC) investigation of a multi-stage phishing attack using the TryHackMe SOC Simulator scenario "Phishing Unfolding", Splunk, an Analyst VM, and the TryDetectThis threat intelligence tool.

本專題透過 TryHackMe SOC Simulator 的「Phishing Unfolding」情境，結合 Splunk、Analyst VM 與 TryDetectThis 威脅情報工具，展示一套針對多階段釣魚攻擊的結構化安全營運中心 (SOC) 調查流程。

The scenario generated 36 alerts, most of which were low-severity process and external email detections caused by routine activity. Initial triage was performed to separate background noise from related suspicious activity, and the alerts linked to the CEO's workstation were grouped into four investigation cases according to attack stage: initial access, execution and discovery, collection, and exfiltration.

本次情境共產生 36 筆警報，其中大多數是由日常活動觸發的低嚴重度程序與外部郵件警報。調查首先區分背景雜訊與彼此關聯的可疑活動，並將與 CEO 工作站相關的警報依攻擊階段整理為四個調查案例：初始存取、執行與偵察、資料收集，以及資料外洩。

The investigation correlated email, Sysmon, and PowerShell logs in Splunk by host, process ID, and timestamp, and supplemented the SIEM evidence with static analysis of the phishing attachment and threat intelligence lookups in the Analyst VM. This made it possible to reconstruct the full attack chain, from a spear-phishing email with a disguised LNK file, through a reverse shell over an ngrok tunnel, host and domain reconnaissance with PowerView, and the collection of files from a financial records share, to the exfiltration of the archived data over DNS.

調查過程在 Splunk 中依主機、程序 ID 與時間戳記關聯電子郵件、Sysmon 與 PowerShell 日誌，並在 Analyst VM 中對釣魚附件進行靜態分析與威脅情報查詢，以補充 SIEM 證據。藉此重建完整的攻擊鏈：從夾帶偽裝 LNK 檔案的魚叉式釣魚郵件開始，經由 ngrok 通道建立反向 Shell，使用 PowerView 進行主機與網域偵察，從財務紀錄共用資料夾收集檔案，最後將壓縮後的資料透過 DNS 外洩。

Beyond identifying malicious activity, the project focuses on evidence-based correlation across multiple data sources, recognising evidence limitations and data quality issues, identifying detection gaps, distinguishing threat intelligence reputation from observed behaviour, and producing SOC case reports that support escalation to incident response.

除了識別惡意活動之外，本專題也著重於跨資料來源的證據關聯、辨識證據限制與資料品質問題、找出偵測缺口、區分威脅情報信譽與實際觀察到的行為，以及撰寫能支援升級至事件應變團隊的 SOC 案例報告。

<br/>

---------

<h2>👥 Employee Asset Inventory｜員工資產清單</h2>


| Name | Role | Email | Logged-in Host |
|------|------|-------|----------------|
| Michael Ascot | CEO <br/>執行長| michael.ascot@tryhatme.com | win-3450 |
| Sophie J | HR <br/>人力資源部| sophie.j@tryhatme.com | win-3461 |
| Michelle Smith | Legal <br/>法務部| michelle.smith@tryhatme.com | win-3459 |
| Roger Fedora | Marketing <br/>行銷部| roger.fedora@tryhatme.com | win-3460 |
| Yani Zubair | IT <br/>資訊部| yani.zubair@tryhatme.com | win-3449 |
| Miguel O'Donnell | Sales <br/>業務部| miguel.odonnell@tryhatme.com | win-3451 |
| Cain Omoore | Sales <br/>業務部| cain.omoore@tryhatme.com | win-3452 |
| Kyra Flores | Sales <br/>業務部| kyra.flores@tryhatme.com | win-3453 |
| Amna Espinoza | Sales <br/>業務部| amna.espinoza@tryhatme.com | win-3454 |
| Ashwin Johnston | Sales <br/>業務部| ashwin.johnston@tryhatme.com | win-3455 |
| Safa Prince | Sales <br/>業務部| safa.prince@tryhatme.com | win-3456 |
| Diego Summers | Sales <br/>業務部| diego.summers@tryhatme.com | win-3457 |
| Armaan Terry | Sales <br/>業務部| armaan.terry@tryhatme.com | win-3458 |

<br/>

---------
<h2>Investigation Workflow｜調查工作流程</h2>

[Alert Intake & Triage (警報接收與初判)]
* Review the alert queue, alert details, and detection logic<br/>
  檢視警報佇列、警報詳情與偵測邏輯<br/>
* Separate background noise from related suspicious alerts<br/>
  區分背景雜訊與彼此相關的可疑警報<br/>
* Group related alerts into cases by attack stage<br/>
  依攻擊階段將相關警報整理為調查案例<br/>
<br/>
<br/>

[Context Enrichment via SIEM (SIEM 擴展調查)]
* Define the log time range and available data sources<br/>
  確認日誌時間範圍與可用資料來源<br/>
* Correlate email, Sysmon, and PowerShell logs by host, process ID, and timestamp<br/>
  依主機、程序 ID 與時間戳記關聯郵件、Sysmon 與 PowerShell 日誌<br/>
* Trace parent-child process relationships and build the attack timeline<br/>
  追蹤父子程序關係並建立攻擊時間軸<br/>
<br/>
<br/>

[Artifact Analysis & Threat Intelligence (檔案分析與威脅情報)]
* Perform static analysis of the phishing attachment in the Analyst VM<br/>
  在 Analyst VM 中對釣魚附件進行靜態分析<br/>
* Decode and reconstruct exfiltrated data<br/>
  解碼並重建外洩資料<br/>
* Check indicators with TryDetectThis<br/>
  使用 TryDetectThis 查詢入侵指標<br/>
<br/>
<br/>

[Classification, Escalation & Reporting (分類、升級與報告)]
* Classify each case as True Positive or False Positive<br/>
  將各案例分類為真陽性或誤報<br/>
* Determine escalation and remediation requirements<br/>
  判斷升級與補救需求<br/>
* Document evidence, limitations, and indicators in case reports<br/>
  在案例報告中記錄證據、限制與入侵指標<br/>

<br/>

---------

<h2>Tools and Concepts Covered｜涵蓋工具與概念</h2>


| Category (分類) | Tools / Concepts (工具 / 概念) |
| --- | --- |
| SOC Operations <br/>SOC 安全營運 | Alert triage, alert ownership, case investigation <br/>警報分類、警報接手、案例調查 |
| SIEM Investigation <br/>SIEM 調查 | Splunk Enterprise, SPL (stats, table, sort, eval, time-bounded searches) <br/>Splunk Enterprise、SPL (stats、table、sort、eval、限定時間範圍的查詢) |
| Security Telemetry <br/>安全遙測資料 | Email logs, Sysmon, PowerShell Module Logging and Script Block Logging <br/>電子郵件日誌、Sysmon、PowerShell 模組日誌與腳本區塊日誌 |
| Alert Prioritization <br/>警報優先排序 | Severity filtering, rule-name context, noise identification <br/>嚴重度篩選、規則名稱情境、雜訊辨識 |
| Event Correlation <br/>事件關聯 | Host, process ID, and parent-child process correlation, attack timeline reconstruction <br/>主機、程序 ID 與父子程序關聯、攻擊時間軸重建 |
| Endpoint Analysis <br/>端點分析 | Sysmon Event ID 1, 11, 15, process tree analysis <br/>Sysmon 事件 ID 1、11、15、程序樹分析 |
| Static File Analysis <br/>靜態檔案分析 | Get-FileHash, Expand-Archive, Format-Hex, file signature (magic bytes) verification <br/>Get-FileHash、Expand-Archive、Format-Hex、檔案標頭 (magic bytes) 驗證 |
| Data Decoding <br/>資料解碼 | Base64 decoding, ZIP archive reconstruction from DNS query chunks <br/>Base64 解碼、從 DNS 查詢片段重建 ZIP 壓縮檔 |
| Threat Intelligence <br/>威脅情報 | TryDetectThis URL, domain, and file reputation analysis <br/>TryDetectThis URL、網域與檔案信譽分析 |
| Attack Techniques <br/>攻擊手法 | Spear-phishing, LNK double extension, reverse shell (powercat, ngrok), PowerView, SMB share collection, DNS exfiltration <br/>魚叉式釣魚、LNK 雙重副檔名、反向 Shell (powercat、ngrok)、PowerView、SMB 共用資料收集、DNS 資料外洩 |
| Indicators of Compromise <br/>入侵指標 | Domains, URLs, file names, file hashes, command lines, file paths <br/>網域、URL、檔案名稱、檔案雜湊值、命令列、檔案路徑 |
| Threat Framework <br/>威脅框架 | MITRE ATT&CK technique mapping <br/>MITRE ATT&CK 技術對應 |
| Incident Classification <br/>事件分類 | True Positive, False Positive <br/>真陽性、誤報 |
| Incident Escalation <br/>事件升級 | Attack chain correlation, escalation decision, remediation planning <br/>攻擊鏈關聯、升級判斷、補救規劃 |
| Case Reporting <br/>案例報告 | 5Ws, affected entities, attack indicators, evidence limitations, attacker objectives <br/>5W 分析、受影響實體、攻擊指標、證據限制、攻擊者目的 |


<br/>



---------


<h2>Materials and Methods｜材料與方法</h2>

[Environment]
* TryHackMe SOC Simulator - Phishing Unfolding (SOC 模擬調查平台)</b>
* Splunk Enterprise (安全事件與日誌調查平台)</b>
* Analyst VM - Windows (SOC 分析師虛擬機)</b>
* TryDetectThis (URL、網域與檔案威脅情報工具)</b>
* Employee and Asset Inventory (員工與資產清單)</b>
* Corporate Domain: tryhatme.com (公司網域)</b>
* Alert Queue and Case Reporting Interface (警報佇列與案例報告介面)</b>

</div>
<br/>

[Available Data Sources]
* Email Logs - 104 events (電子郵件日誌)</b>
* Sysmon Logs - 147 events (Sysmon 端點日誌)</b>
* PowerShell Logs - 62 events (PowerShell 日誌)</b>
* Log Time Range: 2026-11-12 11:05:53 - 11:46:02 UTC (日誌時間範圍)</b>
* No firewall, DNS, or file server logs were available (無防火牆、DNS 或檔案伺服器日誌)</b>

</div>
<br/>

[Investigation Activities]
* Review SOC Documentation, Handover Notes, and Alert Triage Playbook (檢視 SOC 文件、交接紀錄與警報分類手冊)</b>
* Review the Alert Queue and Separate Background Noise from Related Alerts (檢視警報佇列並區分背景雜訊與相關警報)</b>
* Group Related Alerts into Investigation Cases by Attack Stage (依攻擊階段將相關警報整理為調查案例)</b>
* Define the Log Time Range and Available Data Sources in Splunk (在 Splunk 中確認日誌時間範圍與可用資料來源)</b>
* Trace the Phishing Email to Attachment Execution on the Endpoint (追蹤釣魚郵件至端點上的附件執行)</b>
* Reconstruct Parent-Child Process Relationships of the Attacker Sessions (重建攻擊者工作階段的父子程序關係)</b>
* Correlate Sysmon Events with PowerShell Module and Script Block Logs (關聯 Sysmon 事件與 PowerShell 模組及腳本區塊日誌)</b>
* Identify and Correct Missed Events Caused by Inconsistent Field Formats (辨識並修正因欄位格式不一致而遺漏的事件)</b>
* Perform Static Analysis of the Phishing Attachment in the Analyst VM (在 Analyst VM 中對釣魚附件進行靜態分析)</b>
* Decode DNS Query Chunks and Reconstruct the Exfiltrated Archive (解碼 DNS 查詢片段並重建外洩的壓縮檔)</b>
* Validate Indicators with TryDetectThis (使用 TryDetectThis 驗證入侵指標)</b>
* Classify Alerts and Assess Escalation and Remediation Requirements (分類警報並評估升級與補救需求)</b>
* Document Affected Entities, Timeline, Indicators, and Evidence Limitations (記錄受影響實體、時間軸、入侵指標與證據限制)</b>
* Submit Case Reports and Close Investigated Alerts (提交案例報告並關閉已完成調查的警報)</b>

</div>
<br/>

---------

<h2>Initial Triage & Prioritization｜初步警報分類與優先排序</h2>

<p align="center">
<b>Initial SOC Alert Queue<br/> (初始 SOC 警報佇列)</b><br/>
<img src="https://i.imgur.com/6LQPemK.jpeg" height="80%" width="80%" alt="Initial SOC Alert Queue 1035-1018"/>
<img src="https://i.imgur.com/noSlYpe.jpeg" height="80%" width="80%" alt="Initial SOC Alert Queue 1017-1000"/>
<br />
* The queue contained 36 alerts generated between 11:07 and 11:45: 23 Suspicious Parent Child Relationship alerts, <br/>9 Suspicious email from external domain alerts, 3 execution alerts, and 1 Suspicious Attachment found in email alert.<br/>警報佇列共有 36 筆警報，產生於 11:07 至 11:45 之間，包括 23 筆 Suspicious Parent Child Relationship、<br/>9 筆 Suspicious email from external domain、3 筆執行類警報，以及 1 筆 Suspicious Attachment found in email<br/>
<br />
* Most alerts were Low severity and came from broad detection rules, so the queue had to be <br/>reviewed for alerts that were related to each other rather than processed one by one.<br/>大多數警報嚴重度為 Low，且來自涵蓋範圍較廣的偵測規則，<br/>因此需要從佇列中找出彼此相關的警報，而非逐一處理<br/>
<br />
* The only High and Medium severity alerts (1022, 1024, and 1025-1034) all originated <br/>from the same host, win-3450, assigned to the CEO Michael Ascot.<br/>佇列中僅有的 High 與 Medium 嚴重度警報 (1022、1024 與 1025-1034) <br/>全部來自同一台主機 win-3450，也就是 CEO Michael Ascot 的工作站<br/>
<br />
<br />
* Severity alone was not sufficient, because the phishing attachment alert (1005) and the PowerShell script alert (1020) were both Low severity. <br/>Their specific rule names, compared with the broad Parent Child rule, justified including them in the initial review.<br/>僅依嚴重度篩選並不足夠，因為釣魚附件警報 (1005) 與 PowerShell 腳本警報 (1020) 都是 Low 嚴重度。<br/>相較於涵蓋範圍較廣的 Parent Child 規則，這兩筆警報的規則名稱更為具體，因此也納入初步檢視<br/>
<br />
<br />
<b>Case A: Alert 1005 - Invoice-Themed Phishing Attachment<br/> (Case A：發票主題釣魚附件)</b><br/>
<img src="https://i.imgur.com/HDds4pS.jpeg" height="80%" width="80%" alt="Alert 1005"/>
<br />
* Alert 1005 showed an inbound email from the external domain hatmakereurope.xyz to the CEO, <br/>using urgency and legal threats to pressure the recipient into opening the attachment ImportantInvoice-Febrary.zip.<br/>Alert 1005 顯示一封來自外部網域 hatmakereurope.xyz、寄給 CEO 的入站郵件，<br/>以急迫性與法律威脅施壓收件者開啟附件 ImportantInvoice-Febrary.zip<br/>
<br />
* This was the only external email in the queue with an attachment, <br/>making it the most likely initial access point for the activity later observed on win-3450.<br/>這是佇列中唯一夾帶附件的外部郵件，<br/>因此最可能是後續 win-3450 上可疑活動的初始入侵點<br/>
<br />
<br />
<b>Case B: Alert 1020 - PowerShell Script in Downloads Folder<br/> (Case B：Downloads 資料夾中的 PowerShell 腳本)</b><br/>
<img src="https://i.imgur.com/5YcjOWS.jpeg" height="80%" width="80%" alt="Alert 1020"/>
<br />
* Alert 1020 showed powershell.exe (PID 9060) creating PowerView.ps1, <br/>a known Active Directory reconnaissance tool, in the CEO's Downloads folder on win-3450.<br/>Alert 1020 顯示 powershell.exe (PID 9060) 在 win-3450 的 CEO Downloads 資料夾中<br/>建立 PowerView.ps1，這是一個已知的 Active Directory 偵察工具<br/>
<br />
* The host and user matched the recipient of the phishing email in Case A, <br/>indicating a possible link between the two alerts.<br/>主機與使用者都與 Case A 釣魚郵件的收件者相符，<br/>顯示兩筆警報之間可能存在關聯<br/>
<br />
<br />
<b>Case C: Alerts 1022, 1023, 1024 - File Share Access and Data Staging<br/> (Case C：檔案共用存取與資料暫存)</b><br/>
<img src="https://i.imgur.com/0xiDZZ3.jpeg" height="80%" width="80%" alt="Alerts 1022, 1023 and 1024"/>
<br />
* Alerts 1022, 1023, and 1024 were grouped because all three processes were started by <br/>the same PowerShell process (PID 3728) on win-3450 within about one minute.<br/>Alert 1022、1023 與 1024 因三個程序都是在約 1 分鐘內由<br/> win-3450 上的同一個 PowerShell 程序 (PID 3728) 啟動而合併處理<br/>
<br />
* The sequence mapped \\FILESRV-01\SSF-FinancialRecords as drive Z:, copied its contents <br/>with Robocopy to a local folder named "exfiltration", and then disconnected the drive. <br/>Although the rule descriptions state that mapping a drive is normally not a concern, <br/>this context indicated possible data collection.<br/>這三筆警報依序將 \\FILESRV-01\SSF-FinancialRecords 掛載為 Z: 磁碟，以 Robocopy 將內容<br/>複製到名為「exfiltration」的本機資料夾，接著卸載磁碟。<br/>雖然規則描述指出掛載磁碟通常不需擔心，但此情境顯示可能存在資料收集行為<br/>
<br />
<br />
<b>Case D: Alerts 1025-1034 - DNS Exfiltration<br/> (Case D：DNS 資料外洩)</b><br/>
<img src="https://i.imgur.com/c5SbxAV.jpeg" height="80%" width="80%" alt="Alerts 1025-1034"/>
<br />
* Alerts 1025 to 1034 were the only High-severity alerts in the queue and were all triggered within the same minute. <br/>Each showed nslookup.exe started by the same PowerShell process (PID 3728) on win-3450.<br/>Alert 1025 至 1034 是佇列中唯一的 High 嚴重度警報，且全部在同一分鐘內觸發。<br/>每一筆都顯示 win-3450 上同一個 PowerShell 程序 (PID 3728) 啟動了 nslookup.exe<br/>
<br />
* Each query used a Base64-like string as a subdomain of haz4rdw4re.io, <br/>a pattern consistent with DNS-based data exfiltration. <br/>Alert 1025 is shown as a representative example.<br/>每次查詢都以類似 Base64 的字串作為 haz4rdw4re.io 的子網域，<br/>符合透過 DNS 外洩資料的特徵。<br/>此處以 Alert 1025 作為代表範例<br/>
<br />
<br />
<b>Noise Example: Alert 1019 - Benign taskhostw Process<br/> (雜訊範例：良性 taskhostw 程序)</b><br/>
<img src="https://i.imgur.com/M0Hb8k2.jpeg" height="80%" width="80%" alt="Alert 1019"/>
<br />
* Alert 1019 showed svchost.exe starting taskhostw.exe KEYROAMING from C:\Windows\system32\ on win-3460, <br/>which is consistent with a routine Windows scheduled task for credential key roaming.<br/>Alert 1019 顯示 win-3460 上的 svchost.exe 從 C:\Windows\system32\ <br/>啟動 taskhostw.exe KEYROAMING，符合 Windows 用於憑證金鑰漫遊的例行排程工作<br/>
<br />
* The common parent-child pair, system working directory, standard command line, <br/>and lack of any link to win-3450 indicated background activity. <br/>Alert 1019 is shown as a representative example of the recurring Low-severity process alerts.<br/>常見的父子程序組合、系統工作目錄、標準命令列，以及與 win-3450 沒有任何關聯，都顯示這是背景活動。<br/>此處以 Alert 1019 作為反覆出現的 Low 嚴重度程序警報代表範例<br/>
<br />
<br />
* All four cases involved the same host and user and followed a logical attack sequence, <br/>so they were investigated as a single potential attack chain in chronological order, starting from Case A.<br/>四個案例都涉及同一台主機與使用者，並呈現合理的攻擊順序，<br/>因此以單一潛在攻擊鏈的方式，從 Case A 開始依時間順序進行調查<br/>
<br />
<br />

---------

<h2>Case Investigation｜案例調查</h2>

<p align="center">
<b>Case A1-Phishing Email Scope Review<br/> (釣魚郵件影響範圍檢視)</b><br/>
<img src="https://i.imgur.com/izLkU0s.jpeg" height="80%" width="80%" alt="Phishing Email Scope Review"/>
<br />
* Single recipient and no reply, indicating a targeted email to the CEO.<br/>僅有單一收件者且無回覆，顯示為鎖定 CEO 的目標式釣魚郵件<br/>
<br />
<br />
<b>Case A2-Malicious Attachment Opened on Endpoint<br/> (惡意附件於端點上開啟)</b><br/>
<img src="https://i.imgur.com/E5JuMNs.jpeg" height="80%" width="80%" alt="Malicious Attachment Opened on Endpoint"/>
<br />
* Outlook saved the ZIP about 20 minutes after delivery, confirming the CEO opened the attachment.<br/>Outlook 於郵件送達約 20 分鐘後存下 ZIP，確認 CEO 開啟了附件<br/>
* invioce.pdf.lnk was extracted via the Windows ZIP viewer, using a double extension to pose as a PDF.<br/>invioce.pdf.lnk 經由 Windows ZIP 檢視器取出，以雙重副檔名偽裝成 PDF<br/>
<br />
<br />
<b>Case A3-Malicious LNK Execution and Reverse Shell<br/> (惡意捷徑執行與反向 Shell 連線)</b><br/>
<img src="https://i.imgur.com/aE1XZzT.jpeg" height="80%" width="80%" alt="Malicious LNK Execution and Reverse Shell"/>
<br />
* The same Explorer process (PID 3180) launched PowerShell one second after extraction, linking the LNK to execution.<br/>同一個 Explorer 程序 (PID 3180) 在取出檔案 1 秒後啟動 PowerShell，連結 LNK 與執行行為<br/>
* powercat was loaded in memory to open a reverse shell through an ngrok tunnel.<br/>powercat 在記憶體中載入，透過 ngrok 通道建立反向 Shell<br/>
<br />
<br />
<b>Case A4-Reverse Shell Connection Confirmed in PowerShell Logs<br/> (PowerShell 日誌確認反向 Shell 連線成功)</b><br/>
<img src="https://i.imgur.com/19vqyzS.jpeg" height="80%" width="80%" alt="Reverse Shell Connection Confirmed in PowerShell Logs"/>
<br />
* With no network logs available, powercat's own output confirmed the connection succeeded.<br/>在缺少網路日誌的情況下，由 powercat 本身的輸出確認連線成功<br/>
* Identical timestamps mean the displayed order is not the execution order.<br/>時間戳記相同，顯示順序不代表實際執行順序<br/>
<br />
<br />
<b>Case A5-Static Analysis of Malicious LNK Attachment<br/> (惡意捷徑附件之靜態分析)</b><br/>
<img src="https://i.imgur.com/oY9z3dw.jpeg" height="80%" width="80%" alt="Static Analysis of Malicious LNK Attachment"/>
<br />
* The file lacked the LNK signature (4C 00 00 00) and was plain text in this simulation.<br/>檔案缺少 LNK 標頭 (4C 00 00 00)，在此模擬環境中為純文字檔<br/>
* Its content matched the command in Case A3, confirming the attachment as the reverse shell source.<br/>內容與 Case A3 的指令一致，確認附件即為反向 Shell 的來源<br/>
<br />
<br />
<b>Case B1-Host Discovery via Attacker PowerShell Session<br/> (攻擊者 PowerShell 工作階段之主機偵察)</b><br/>
<img src="https://i.imgur.com/MWblfS8.jpeg" height="80%" width="80%" alt="Host Discovery via Attacker PowerShell Session"/>
<br />
* PID 9060 appeared 3 seconds after the reverse shell and ran a hands-on-keyboard discovery sequence before dropping PowerView.<br/>PID 9060 於反向 Shell 建立 3 秒後出現，執行人工偵察指令後放置 PowerView<br/>
* No process creation event exists for PID 9060, so its origin is inferred from timing.<br/>日誌中沒有 PID 9060 的程序建立事件，其來源係依時間關係推論<br/>
<br />
<br />
<b>Case B2-PowerView Loaded for Domain Discovery<br/> (載入 PowerView 進行網域偵察)</b><br/>
<img src="https://i.imgur.com/U4ppyCN.jpeg" height="80%" width="80%" alt="PowerView Loaded for Domain Discovery"/>
<br />
* The ExecutionPolicy Bypass session matches the first appearance of PID 3728 one second later.<br/>繞過執行原則的工作階段，與 1 秒後首次出現的 PID 3728 相符<br/>
* PowerView's LDAP computer enumeration ran about one minute before FILESRV-01 was accessed.<br/>PowerView 的 LDAP 電腦列舉在存取 FILESRV-01 約 1 分鐘前執行<br/>
<br />
<br />
<b>Case C1-Initial File Share Activity Query<br/> (檔案共用活動初步查詢)</b><br/>
<img src="https://i.imgur.com/vmulFEb.jpeg" height="80%" width="80%" alt="Initial File Share Activity Query"/>
<br />
* Robocopy from Alert 1023 was missing, showing the query was incomplete.<br/>Alert 1023 的 Robocopy 未出現，顯示查詢結果不完整<br/>
<br />
<br />
<b>Case C2-Corrected Query for Inconsistent PID Format<br/> (PID 格式不一致之修正查詢)</b><br/>
<img src="https://i.imgur.com/3iUpwbb.jpeg" height="80%" width="80%" alt="Corrected Query for Inconsistent PID Format"/>
<br />
* The parent PID was stored as "3,728" for Robocopy; querying both formats recovered the missing event.<br/>Robocopy 的父程序 PID 被存為「3,728」，同時查詢兩種格式後找回遺漏的事件<br/>
* Map, copy from Z:\, and disconnect completed within 58 seconds.<br/>掛載、從 Z:\ 複製、卸載在 58 秒內完成<br/>
<br />
<br />
<b>Case C3-Staged Sensitive Files and Archive Creation<br/> (暫存之敏感檔案與壓縮檔建立)</b><br/>
<img src="https://i.imgur.com/AXjk06V.jpeg" height="80%" width="80%" alt="Staged Sensitive Files and Archive Creation"/>
<br />
* The staging folder was created 7 seconds before the share was mapped, indicating planned collection.<br/>暫存資料夾比掛載共用資料夾早 7 秒建立，顯示為有計畫的資料收集<br/>
<br />
<br />
<b>Case D1-DNS Exfiltration via nslookup<br/> (透過 nslookup 進行 DNS 資料外洩)</b><br/>
<img src="https://i.imgur.com/txHORSd.jpeg" height="80%" width="80%" alt="DNS Exfiltration via nslookup"/>
<br />
* The "UEsDB" chunk decodes to the ZIP signature "PK\x03\x04", linking the queries to exfilt8me.zip.<br/>「UEsDB」片段解碼為 ZIP 標頭「PK\x03\x04」，將查詢與 exfilt8me.zip 連結<br/>
* Shared timestamps prevented recovering the chunk order from Sysmon alone.<br/>時間戳記相同，僅憑 Sysmon 無法還原片段順序<br/>
<br />
<br />
<b>Case D2-Full Exfiltration Sequence in PowerShell Logs<br/> (PowerShell 日誌中的完整外洩序列)</b><br/>
<img src="https://i.imgur.com/VeUzeG9.jpeg" height="80%" width="80%" alt="Full Exfiltration Sequence in PowerShell Logs"/>
<br />
* 18 lookups were logged, but Sysmon captured only 10, leaving 8 undetected.<br/>共記錄 18 次查詢，但 Sysmon 僅捕捉到 10 次，有 8 次未被偵測<br/>
* Reassembling the first 16 chunks in loop order produced a valid ZIP containing the two files from Case C3.<br/>依迴圈順序重組前 16 個片段，得到內含 Case C3 兩個檔案的有效 ZIP<br/>
<br />
<br />
<b>Case E1-URL and Domain Reputation Check<br/> (URL 與網域信譽查詢)</b><br/>
<img src="https://i.imgur.com/fHe7cos.jpeg" height="80%" width="80%" alt="URL and Domain Reputation Check"/>
<br />
* All indicators were CLEAN despite confirmed malicious behaviour; legitimate services (GitHub, ngrok) were abused.<br/>儘管已確認惡意行為，所有指標仍為 CLEAN；攻擊者濫用了 GitHub、ngrok 等合法服務<br/>
<br />
<br />
<b>Case E2-File Reputation Check of LNK Attachment<br/> (捷徑附件之檔案信譽查詢)</b><br/>
<img src="https://i.imgur.com/8ZVAmgt.jpeg" height="80%" width="80%" alt="File Reputation Check of LNK Attachment"/>
<br />
* Classified as MALICIOUS; the octet-stream file type is consistent with Case A5.<br/>判定為 MALICIOUS，octet-stream 檔案類型與 Case A5 結果一致<br/>
<br />
<br />
