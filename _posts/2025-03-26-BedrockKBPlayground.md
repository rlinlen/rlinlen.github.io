---
title: Bedrock知識庫 step-by-step教學
date: 2025-03-26 00:00:00 +0800
categories: [aws]
tags: [bedrock,ai,chatbot,kb]     # TAG names should always be lowercase
---

## 背景說明
本篇文章提供如何在aws console建置一個Bedrock知識庫的步驟教學。
亦可參考[官方文件](https://docs.aws.amazon.com/bedrock/latest/userguide/knowledge-base-build.html)

**建議瀏覽器**
- Chrome
- Edge

**Workshop 材料連結**
- Workshop 實驗環境連結: <參與workshop時提供>
- 短網址：<參與workshop時提供>
- access code: <參與workshop時提供>

- 測試檔案材料連結: [下載檔案](/assets/file/ELN.zip)

**實作課程流程**
1. 實驗環境準備
    * 使用 AWS 練習環境
2. 啟用模型存取權
3. 建立知識庫
4. 測試知識庫

**Workshop 架構圖**
![image](/assets/img/BedrockKBPlayground/architecture.png)

![image](/assets/img/BedrockKBPlayground/architecture-2.png)


## 1. 實驗環境準備
### 1.1 使用 AWS 練習環境

進入課程提供的 AWS Workshop Studio 網址，並點擊 「Email one-time password (OTP)」，以獲得一次性臨時密碼。
![image](/assets/img/WorkshopShare/OTP-1.png)


輸入自己的 Email 以用於接收一次性臨時密碼 ，並點擊 「傳送密碼」。
![image](/assets/img/WorkshopShare/OTP-2.png)


>活動期間使用相同 Email 進行登入將會使用相同的練習環境
{: .prompt-info }

進入自己的 Email 以取得 9 碼 Passcode。
![image](/assets/img/WorkshopShare/OTP-3.png)

將 9 碼 Passcode 填入，並點擊 「登入」。
![image](/assets/img/WorkshopShare/OTP-4.png)

登入並進行環境使用要求條款後，請勾選 「I agree with the Terms and Conditions」，並點擊 「Join event」。
![image](/assets/img/WorkshopShare/OTP-5.png)

點擊 「Open AWS console」，以開啟 AWS Management Console。
![image](/assets/img/WorkshopShare/OTP-6.png)


>練習過程中，若 AWS Management Console 逾時登出，可再次點擊此連結以重新登入並更新憑證
{: .prompt-info }

進入 AWS Management Console 後，即完成登入。
![image](/assets/img/WorkshopShare/OTP-7.png)

## 2. 啟用模型存取權
>確認區域為 United States(Oregon), us-west-2，並確認使用者為 WSParticipantRole/Participant，避免誤用到貴司 AWS Account
{: .prompt-warning }
![image](/assets/img/BedrockShare/NavBar-1.png)


點擊 齒輪圖示  可以更換為您習慣的語言
![image](/assets/img/BedrockShare/NavBar-2.png)


進入 Amazon Bedrock 服務
![image](/assets/img/BedrockShare/ModelAccess-1.png)


進入 模型存取權 (Model access)>修改模型存取權 來啟用欲存取的 Foundation Models (FMs)
![image](/assets/img/BedrockShare/ModelAccess-2.png)

選用以下模型啟用
- Titan Text Embeddings V2
- Claude 3 Haiku
- Claude 3.5 Sonnet v2
- Nova Lite
- Nova Pro


等待一段時間，確認 FMs 的存取狀態為 已授予存取權 (access granted)
![image](/assets/img/BedrockShare/ModelAccess-3.png)


## 3. 建立知識庫
### 3-1. 建立 S3 Bucket 以存放用於 RAG 的資料

進入 Amazon S3 服務 > 點擊 建立儲存貯體 (Create bucket)
![image](/assets/img/BedrockKBPlayground/S3-1.png)


>S3是Simple Storage Service的縮寫，顧名思義，S3是一套關於資料儲存的雲端服務，類似於地端使用的公槽空間。使用S3的價值在於，它提供我們一套完善的資安、資料可用性及資料共享服務，並原生跟其他AWS服務深度整合。S3容量無上限、單一檔案大小最高5TB。
>Bucket name需為全球唯一，因此請避免使用相同名稱
{: .prompt-info }

>AWS account ID是每個aws環境的唯一識別碼。
>![image](/assets/img/WorkshopShare/NavBar-3.png)
{: .prompt-info }


輸入 
==hcls-[Account_ID]== 
作為儲存貯體名稱 (Bucket name)，當中 [Account_ID] 請覆蓋成您所使用的 AWS Account ID。
完成後請點擊 建立儲存貯體 (Create bucket)

將下載的檔案及目錄上傳至建立的 S3 Bucket 中。
(Upload > Add Folder > 選擇下載的ELN資料夾)
![image](/assets/img/BedrockKBPlayground/S3-2.png)

>請注意 Amazon Bedrock Knowledge Bases 尚不支援非英文組成的目錄，請勿使用非英文的目錄。檔名則不在此限 (可使用中文)。
{: .prompt-info }

### 3-2. 建立 Knowledge Bases
進入 Amazon Bedrock 服務，在左方導覽列 (如果沒有出現導覽列，可點擊右上方的 漢堡圖示 展開)，點擊 知識庫 (Knowledge bases) 來快速建立 RAG 應用


在右方主視窗中，點擊 建立知識庫Create > Knowledge Base with vector store，並完成以下步驟 (未提及的參數設定可保留預設)：
![image](/assets/img/BedrockKBPlayground/KB-1.png)


3.a. 提供知識庫詳細資訊 (Provide knowledge base details)
- Knowledge Base name: eln-kb
- Data Source: Amazon S3
![image](/assets/img/BedrockKBPlayground/KB-2.png)

3.b. 設定資料來源 (Configure data source)
- 資料來源名稱: data-source-eln-s3
- S3 URI: 瀏覽 S3 並選取 > s3://hcls-[Account-ID]/ELN/
- Parsing strategy: Amazon Bedrock default parser
- Chunking strategy(分塊策略): No Chunking(未分塊)


>Parsing strategy(分析策略)
>- Amazon Bedrock default parser
>Amazon 預設的解析器，僅會解析純文字，此項不會產生費用。
>- Amazon Bedrock Data Automation as parser - Preview
>Amazon 提供的解析器，可有效處理多模態資料，包括文字和影像，而無需提供任何額外的提示。
>- Foundation models as a parser
使用基礎模型或推論描述檔來處理多模型資料，包括文字和影像。此剖析器可讓您選擇自訂用於資料擷取的提示。
{: .prompt-tip }

>Chunking(分塊策略)
> - 預設分塊
將內容分割成大約 300 個字符的文字區塊。分塊化程序會遵守句子界限，確保在每個分塊內保留完整的句子。
> - 固定大小分塊
可以透過指定每個分塊的字符數量和重疊百分比來設定所需的分塊大小，從而提供靈活性，以符合您的特定需求。
語意分塊
語意分塊是一種自然語言處理技術，可將文字分割為有意義的區塊，以增強理解和資訊擷取。旨在透過專注於語意內容，而非僅語法結構來提高擷取準確性。
> - 階層分塊
階層分塊涉及將資訊組織到子區塊和父區塊的巢狀結構。建立資料來源時，您可以定義父區塊大小、子區塊大小，以及在每個區塊之間重疊的字符數量。若資料源具備檔案目錄，較爲合適此策略。
{: .prompt-tip }


3.c. 選取內嵌項目模型並設定向量存放區 (Select embeddings model and configure vector store)

內嵌項目模型: Titan Text Embeddings v2
向量資料庫: 快速建立新的向量儲存 – 建議值 > Amazon OpenSearch Serverless

3.d. 檢閱並建立 (Review and create)

點擊 建立知識庫 (Create knowledge base)
![image](/assets/img/BedrockKBPlayground/KB-3.png)

點擊 建立知識庫 按鈕後，可拉到畫面上方，會顯示說明 正在準備向量資料庫，需要等待幾分鐘時間 (約為 5 分鐘)



### 3-3. 同步資料 (Embedding)
當環境建置完成，前往 資料來源 (Data source) 選擇 data-source-eln-s3 並點擊 同步 (Sync)
![image](/assets/img/BedrockKBPlayground/KB-4.png)

點擊 同步 (Sync) 按鈕後，可拉到畫面上方，會顯示說明 資料來源已啟動同步，需要等待幾分鐘時間 (約為 5 分鐘)


點選進去 data-source-eln-s3，查看 同步歷史記錄，確認以下資訊
狀態為完成 (Completed)
![image](/assets/img/BedrockKBPlayground/KB-5.png)


## 4. 測試知識庫
資料完成同步後，即可在右方聊天視窗進行知識庫測試，在 選取模型 (Select model) 選擇 Anthropic > Claude 3.5 Sonnet v2：
![image](/assets/img/BedrockKBPlayground/KB-6.png)


輸入問題：
```
LDH info and it's MTT values?
```

> 我們預期，KB可以正確回答LDH，但MTT因為在圖中無法回答。我們可以透過選擇不同的parsing strategy來處理圖片訊息。本場workshop不包含此部分練習。
{: .prompt-info }

> LLM生成資料具有隨機性，因此您的產出內容可能跟示範的不盡相同
{: .prompt-info }

恭喜您已完成本場次workshop。