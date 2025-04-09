---
title: Bedrock 知識庫聊天機器人end-to-end UI部署教學
date: 2025-03-21 00:00:00 +0800
categories: [aws]
tags: [bedrock,ai,chatbot,kb]     # TAG names should always be lowercase
---

## 背景說明
本篇文章提供 [aws-samples/bedrock-chat](https://github.com/aws-samples/bedrock-chat)的安裝補充說明

### 什麼是bedrock-chat
[Bedrock-chat](https://github.com/aws-samples/bedrock-chat)是aws社群提供的一個開源chatbot的範例應用。它基於bedrock提供的LLM服務基礎上，整合使用者驗證、資安防護、知識庫等功能，讓企業可以快速導入與測試開箱即用的私有知識庫聊天機器人，提升生產力。

### 適用對象
已經擁有aws帳號，想快速導入私有知識庫聊天機器人的IT人員

## 準備項目
- aws account 管理權限
- 確認要部署的區域。例如：ap-northeast-1
- 亦可先參考[Github安裝教學影片](https://www.youtube.com/watch?v=PDTGrHlaLCQ)


## 部署步驟

1. 開啟aws console, 並切換到對應的region。以ap-northeast-1為例：

2. 執行 `CloudShell`
![](/assets/img/BedrockChatBotDeployment/cloudshell-1.png)

3. 貼上並執行以下指令，下載程式碼
```
git clone https://github.com/aws-samples/bedrock-chat.git
cd bedrock-chat
chmod +x bin.sh
```
![](/assets/img/BedrockChatBotDeployment/cloudshell-2.png)

4. 執行以下指令，選擇y開始進行安裝。這步驟大約會花到35分鐘

> 記得更改您的aws區域，參數是bedrockRegion
{: .prompt-info }

```
./bin.sh --cdk-json-override '{
  "context": {
    "selfSignUpEnabled": false,
    "bedrockRegion": "ap-northeast-1"
  }
}'
```

## 設定步驟
1. 啟用對應的大語言模型：
- 搜尋 `Bedrock`
- 在下方Bedrock Configurations 找到 Model access, 選擇 Modify model access
![](/assets/img/BedrockChatBotDeployment/bedrock-model-1.png)
勾選以下模型：
- Titan Text Embeddings V2
- Claude 3.5 Sonnet v2
- Claude 3 Haiku  (3.5 if available)
- Nova Pro
- Nova Lite

> 不同區域可選模型略有不同，使用者可依實際需求進行測試
{: .prompt-info }
- 點擊 Next, 完成後等待五分鐘，確認剛剛選擇的模型都是綠色的Access granted
![](/assets/img/BedrockChatBotDeployment/bedrock-model-1.png)

2.到Cloudformation取得前端網址：
- 搜尋 `CloudFormation`
- 在 Stacks 找到 `BedrockChatStack`的選項
- 點擊進入

![](/assets/img/BedrockChatBotDeployment/Cloudformation-1.png)

- 進入後選擇 `Outputs`，下拉找到 `FrontendURL`
![](/assets/img/BedrockChatBotDeployment/Cloudformation-2.png)

Frontend URL會具有以下格式: https://xxxxxxxxx.cloudfront.net

點入後即可看到登入頁面：
![](/assets/img/BedrockChatBotDeployment/chatbot-login.png)

3.到Cognito新增user
- 搜尋 `Cognito`
- 在`User pools` 選擇 `AuthUserPoolxxxxx-xxxxxxx`
![](/assets/img/BedrockChatBotDeployment/cognito-1.png)
- 在User Management 選擇 Users > Create User
![](/assets/img/BedrockChatBotDeployment/cognito-2.png)
按照步驟完成新增user

4.回到Frontend URL, 應可完成登入

## 使用說明
### 1.最基礎的對談，請先參考 [Github基礎操作](https://github.com/aws-samples/bedrock-claude-chat?tab=readme-ov-file#basic-conversation)

> 接下來，我們可透過創建Bot，完成更多功能
{: .prompt-tip }

### 2.透過Agent進行搜尋後，再回覆內容
- 點擊 Bot Console > + Create New Bot
![](/assets/img/BedrockChatBotDeployment/chatbot-console.png)

- 輸入Name: 網路搜尋bot
- 輸入Instructions(Prompt): 可跳過
- 將Internet Search勾選起來
![](/assets/img/BedrockChatBotDeployment/chatbot-create-1.png)

> 搜尋是透過Duckduckgo或Firecrawl完成的
{: .prompt-info}
- 將Model Activation勾選前一步驟已在Bedrock啟用的模型，避免使用者誤選
![](/assets/img/BedrockChatBotDeployment/chatbot-create-2.png)

- 點選Create完成創建
![](/assets/img/BedrockChatBotDeployment/chatbot-search.png)

- 之後使用時，先選擇創建的bot，然後再進行提問，程式即會先就問題搜尋結果，然後閱讀理解後，再進行回答。

![](/assets/img/BedrockChatBotDeployment/chatbot-search-2.png)

> 問答時，需選擇其中一個剛剛啟用的模型。一般來說，簡易回答可以優先選擇Nova Lite或Haiku，複雜的問題可以選擇Nova Pro或Sonnet。
{: .prompt-tip }


### 3.創建知識庫（Knowledge Base），回答問題前，bot會先參考企業內部文件，取得企業獨有知識，減少幻覺並精確回覆需要的內容

- 點擊 Bot Console > + Create New Bot

- 輸入Name: 知識庫bot
- 輸入Instructions: (Prompt). Insturction結構上可以使用 `Role+Instruction+Example`來建構，例如：

```
你是一個生技產業的專家，請用中文回答問題。
如果問到實驗紀錄，引用的資料應依據時間排序，並說明多筆實驗中是否有關聯、有的話實驗與實驗中的關聯設計為何。
<Good Example>
{輸入希望LLM回覆的範例結果}
</Good Example>
```
{: .nolineno }


- 創建一個KB (Knowledge)

KB目前支援三種方式。
1. Create New Knowledge Base - File 直接透過chatbot網頁上傳檔案。適合快速測試或少量檔案的場景。
2. Create New Knowledge Base - S3 Data Source。如果您檔案會集中上傳到s3，可以將之綁定。
3. Use your existing knowledge base - 選定Bedrock KB ID。如果您有既有的Bedrock KB，可以將之重複使用。

![](/assets/img/BedrockChatBotDeployment/chatbot-kb.png)

> 如果選擇2,3的方式，需確認知識庫有定期進行同步，方可查詢到結果。步驟請參考[bedrock官方文件](https://docs.aws.amazon.com/bedrock/latest/userguide/kb-data-source-sync-ingest.html)
{: .prompt-info }

- 之後使用時，先選擇創建的bot，然後再進行提問，程式即會先就問題於上傳的檔案中搜尋結果，然後閱讀理解後，再進行回答。

> 問答時，需選擇其中一個剛剛啟用的模型。一般來說，簡易回答可以優先選擇Nova Lite或Haiku，複雜的問題可以選擇Nova Pro或Sonnet。
{: .prompt-tip }

## 更新方式
此Github專案截止2025-03-21尚在積極開發中，因此會不定時釋出功能更新與修補。如要更新，目前有兩種方式：

1.透過原有方式更新
- 此方式相對簡單，但是缺點重新部署後，**Frontend Url會變更**。（資料還是會保留）
- 回到`CloudShell`，更新程式碼
```
cd bedrock-claude-chat
git pull
```

- 進行部署
```
./bin.sh --cdk-json-override '{
  "context": {
    "selfSignUpEnabled": false,
    "bedrockRegion": "ap-northeast-1"
  }
}'
```

2.透過cdk更新
> Cloudshell的`$HOME`有1GB大小的限制，因此無法在原目錄進行下方操作。建議使用個人Unix環境，或是另外開啟一台EC2的linux進行。您可使用AWS Free tier挑選適合的t系列型號來進行。
{: .prompt-warning}

- 確保使用的Linux機器
    - 已使用aws configure設定aws身份，並取得對應管理權限
    - 已安裝docker和Node.js

- 更新 cdk.json
```
cd bedrock-claude-chat
cd cdk
sed -i 's/"bedrockRegion"[^,]*,/"bedrockRegion": "ap-northeast-1",/g' cdk.json
sed -i 's/"selfSignUpEnabled"[^,]*,/"selfSignUpEnabled": false,/g' cdk.json
```

- 第一次操作時，須先執行以下指令。未來再次更新時則不需要。
```
npx cdk bootstrap
```

- 執行更新
```
npx cdk deploy --require-approval never --all
```

- [Github參考步驟](https://github.com/aws-samples/bedrock-claude-chat?tab=readme-ov-file#deploy-using-cdk)

## 後續調整方向
- 資料整合進s3建議
- 優化查詢內容
- 是否需要使用公司域名(例如：https://rag.yourdomain.com)
- 確認可訪問的公司白名單ip，限縮訪問範圍
- 強化驗證，透過OIDC串接公司SSO(如Entra ID)或開啟MFA
- 成本優化
- 客製化需求

> 可以聯繫您的aws account team 取得更多協助
{: .prompt-tip }
