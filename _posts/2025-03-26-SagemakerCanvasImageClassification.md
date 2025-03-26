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

- 測試檔案材料連結: [下載檔案](/assets/file/xray-workshop-150.zip)
> 測試檔案為以下資料集的子集，為考慮workshop時間而做縮減
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
下載並解壓縮胸部x光影像
![image](/assets/img/SagemakerCanvasImageClassification/data.png)


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


### 2-2. 上傳資料
搜尋s3，並選擇Sagemaker創建的資料夾，找到資料夾
```
sagemaker-[region]-[aws account id]
```
![image](/assets/img/SagemakerCanvasImageClassification/upload-1.png)

>請留意選擇的資料夾，如選成其他資料夾需要額外資安設定（CORS）才能運作
{: .prompt-tip }

>S3是Simple Storage Service的縮寫，顧名思義，S3是一套關於資料儲存的雲端服務，類似於地端使用的公槽空間。使用S3的價值在於，它提供我們一套完善的資安、資料可用性及資料共享服務，並原生跟其他AWS服務深度整合。S3容量無上限、單一檔案大小最高5TB。
{: .prompt-info }
>Bucket name需為全球唯一，因此請避免使用相同名稱
{: .prompt-info }

>AWS account ID是每個aws環境的唯一識別碼。
>![image](/assets/img/WorkshopShare/NavBar-3.png)
{: .prompt-info }

點入該Bucket，並點選 Upload > Add Folder
![image](/assets/img/SagemakerCanvasImageClassification/upload-2.png)

選擇剛剛解壓的資料夾：xray-workshop-150 > 選擇Upload
![image](/assets/img/SagemakerCanvasImageClassification/upload-3.png)


### 2-3 訓練模型
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


>當模型使用約5000張影像進行Standard build訓練後，準確率（accuracy）可達到98.4%
{: .prompt-tip }

## 4. 關閉Canvas

>當實驗結束後，務必點擊Log out退出當前Canvas環境。如果直接關閉視窗，Canvas的費用會持續進行。
{: .prompt-warning }
![image](https://hackmd.io/_uploads/BkP-bAJ2Jl.png)





恭喜您已完成本場次workshop。
