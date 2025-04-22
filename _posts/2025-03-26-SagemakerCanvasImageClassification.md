---
title: 使用Sagemaker Canvas AutoML 進行影像歸類
date: 2025-03-26 00:00:00 +0800
categories: [aws]
tags: [Sagemaker,Sagemaker Canvas,AutoML,ai,low-code]     # TAG names should always be lowercase
---

## 背景說明
本篇文章提供如何透過Sagemaker Canvas AutoML的功能，在不寫程式碼的情況下，進行高品質的機器學習模型的訓練與部署。

**建議瀏覽器**
- Chrome
- Edge

**Workshop 材料連結**
- Workshop 實驗環境連結: <參與workshop時提供>
- 短網址：<參與workshop時提供>
- access code: <參與workshop時提供>

- 50張測試檔案材料連結: [下載檔案](/assets/file/xray-workshop-small.zip)
- 150張測試檔案材料連結: [下載檔案](/assets/file/xray-workshop-150.zip)
> 測試檔案為以下資料集(約5000張)的子集，為考慮workshop時間而做縮減
Ref:
https://www.kaggle.com/datasets/paultimothymooney/chest-xray-pneumonia
{: .prompt-info }

**實作課程流程**
1. 實驗環境準備
    * 下載資料
2. 模型訓練
    * 上傳資料
3. 模型評估
4. 關閉Canvas


## 1. 實驗環境準備
因為資料大小較大，為避免場地網路速度限制，我們將使用aws骨幹網路，直接在不經過本地電腦的情況下，下載並上傳影像。如果您會後有興趣進一步查看檔案，可點選上方連結，直接下載，解壓縮胸部x光影像後，會看到150張影像檔案。
![image](/assets/img/SagemakerCanvasImageClassification/data.png)

1. 開啟aws console, 並切換到對應的region。

2. 執行 `CloudShell`
![](/assets/img/BedrockChatBotDeployment/cloudshell-1.png)

> AWS CloudShell 是以瀏覽器為基礎的預先驗證Shell，提供使用者快速操作AWS環境用。使用 AWS CloudShell時，您最多可以在使用1GB的持久性儲存體，無需額外費用。持續性儲存空間位於主目錄 ($HOME) 中，而且對您來說是私有的。
{: .prompt-tip }

3. 

> 如果您使用文章開頭提供的檔案，請將以下指令中，所有{}的部分更新;貼上並執行以下指令，執行程式碼
```
curl -o xray-workshop-small.zip https://rlinlen.github.io/assets/file/xray-workshop-small.zip
unzip xray-workshop-small.zip
aws s3 cp --recursive ./xray-workshop-small s3://sagemaker-us-west-2-{account_id}/xray-workshop-small/
```

> 如果您拿到的是短網址的檔案，請將以下指令中，所有{}的部分更新;貼上並執行以下指令，執行程式碼
```
curl -s -D - "{short_url}" | grep -i "target:" | cut -d' ' -f2- | tr -d '\r' | xargs -I {} curl -L -o xray-workshop-150.zip "{}"
unzip xray-workshop-150.zip
aws s3 cp --recursive ./xray-workshop-150 s3://sagemaker-us-west-2-{account_id}/xray-workshop-150/
```
![](/assets/img/SagemakerCanvasImageClassification/cloudshell-1.png)

> 上述指令目的為：下載檔案、解壓、上傳到s3的文件夾中
{: .prompt-info }

## 2. 模型訓練
### 2-1. 進入Sagemaker環境
搜尋Sagemaker AI
![image](/assets/img/SagemakerCanvasShare/access-1.png)

點擊Canvas > Select user profile應已創建SageMakerUser
![image](/assets/img/SagemakerCanvasShare/access-2.png)

>如果沒有該Profile：
>點擊Canvas > Create a SageMaker domain
>![image](/assets/img/SagemakerCanvasShare/access-3.png)
{: .prompt-info }

>選擇 Set up for single user (Quick setup)，創建並等待約五分鐘至完成
>![image](/assets/img/SagemakerCanvasShare/access-4.png)
>點擊選單，回到SageMaker > Canvas > 此時可觀察到已可開啟Canvas > Open Canvas
>![image](/assets/img/SagemakerCanvasShare/access-5.png)


### 2-2 訓練模型
選擇 Datasets > Import Data > Image > "xrayimagesmall"
![image](/assets/img/SagemakerCanvasImageClassification/canvas-1.png)
![image](/assets/img/SagemakerCanvasImageClassification/canvas-2.png)

選擇 Data source: Amazon S3
選擇 sagemaker-[region]-[aws account id] > xray-workshop-150 > train 勾選train資料夾
![image](/assets/img/SagemakerCanvasImageClassification/canvas-3.png)


選擇 My Models > + New Model
![image](/assets/img/SagemakerCanvasImageClassification/canvas-4.png)

輸入 Chestmodel1 > Image analysis > Create
![image](/assets/img/SagemakerCanvasImageClassification/canvas-5.png)

選擇 Dataset "xrayimagesmall" > 探索資料 > Quick build

![image](/assets/img/SagemakerCanvasImageClassification/canvas-6.png)

>Sagemaker會將文件夾名稱當作label
{: .prompt-tip }

>Quick build使用較少的資料，Standard build使用較完整的資料與參數調整。因為時間的關係，我們只選用Quick build。
{: .prompt-tip }


## 3. 模型評估
等待訓練結束後，即可預覽Performance
選用Heatmap可觀察到模型分析時在意的地方
![image](/assets/img/SagemakerCanvasImageClassification/canvas-7.png)

後續可以進行deploy，使用API呼叫模型


>當模型使用約5000張影像進行Standard build訓練後，準確率（accuracy）可達到99.3%
{: .prompt-tip }

## 4. 關閉Canvas

>當實驗結束後，務必點擊Log out退出當前Canvas環境。如果直接關閉視窗，Canvas的費用會持續進行。
{: .prompt-warning }
![image](https://hackmd.io/_uploads/BkP-bAJ2Jl.png)

恭喜您已完成本場次workshop。
