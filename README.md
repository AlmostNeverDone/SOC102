# Multi-Stage Phishing Attack Chain Investigation Using Splunk

使用 Splunk 進行多階段釣魚攻擊鏈調查

<br/>

---------

<h2>Outline｜調查簡介</h2>

This project demonstrates a structured Security Operations Center (SOC) investigation of a multi-stage phishing attack using the TryHackMe SOC Simulator scenario "Phishing Unfolding", Splunk, an Analyst VM, and the TryDetectThis threat intelligence tool.

本專題透過 TryHackMe SOC Simulator 的「Phishing Unfolding」情境，結合 Splunk、Analyst VM 與 TryDetectThis 威脅情報工具，展示一套針對多階段釣魚攻擊的結構化安全營運中心 (SOC) 調查流程。

The scenario generated 36 alerts, most of which were low-severity process and external email detections caused by routine activity. Initial triage was performed to separate background noise from related suspicious activity, and the alerts linked to the CEO's workstation were grouped into four investigation cases according to attack stage: initial access, execution and discovery, collection, and exfiltration.

本次情境共產生 36 筆警報，其中大多數是由日常活動觸發的低嚴重度程序與外部郵件警報。首先透過 Initial Triage 區分背景雜訊與彼此關聯的可疑活動，並將與 CEO 工作站相關的警報依攻擊階段整理為四個調查案例：初始存取、執行與偵察、資料收集，以及資料外洩。

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
