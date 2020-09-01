---
title: Database ERModel
date: 2019-11-26 23:00:30
tags: 
- Database
- ERModel
categories: Database
---

## Database 實體關聯模型(E-R model)

<!--more-->
***

### 實體 (Entity)

* 將真實世界的人、事、物等實體，以一個方框圖來表示，如果實體必須依附其他實體而存在，則為弱實體，使用雙方v框圖表示。
* 例如選課系統中的學生與課程兩個實體:
	* 學生的緊急聯絡人則為弱實體
	* 因為學生轉學，則該學生的緊急連絡人資料便不需要被紀錄
* 下圖是學生選課系統中的實體：
{% img http://database.klab.tw/teach/img/t4_2_01.png 300 150 vi-vim-cheat-sheet %}


### 屬性 (Attribute)

* 實體擁有的特性稱為屬性(Attribute)，以橢圓形來表示屬性，
* 當屬性具有多重值時，則以雙橢圓形來表示，
* 屬性為主鍵，則屬性名稱底下畫一條底線區別，
* 複合屬性(Composite attribute)則為屬性再分割，
* 衍生屬性(Derived attribute)則以虛橢圓形來表示，
* 例如選課系統中的學生實體擁有學號、姓名、性別、地址、年齡和電話等屬性，可以如下所示：
{% img http://database.klab.tw/teach/img/t4_2_02.png 400 350 vi-vim-cheat-sheet %}

* 課程實體其屬性如下圖所示：
{% img http://database.klab.tw/teach/img/t4_2_03.png 200 150 vi-vim-cheat-sheet %}

* 緊急聯絡人是弱實體，必須依附著學生實體，其主鍵若設定為學生學號，則此處不必繪出，如下圖所示：
{% img http://database.klab.tw/teach/img/t4_2_04.png 150 100 vi-vim-cheat-sheet %}


### 實體關係 (Relationship)
* 說明各實體之間相互關聯(relationship)的限制(constraints)，是指實體與實體之間如果有關聯，則將此關聯以菱形連結兩實體，並寫出對應的基數。
* 例如：每一個學生可以選修許多課程，一門課程可以被許多學生所選；每位學生皆有一位緊急聯絡人，每位聯絡人也只對應到一位學生。
{% img http://database.klab.tw/teach/img/t4_2_05.png 300 200 vi-vim-cheat-sheet %}

* 當實體間的關聯為多對多時，並需將此關聯改為一個實體，如下圖所示：
{% img http://database.klab.tw/teach/img/t4_2_06.png 400 200 vi-vim-cheat-sheet %}


### E-R關聯圖 (E-R Diagram)
* 第4步驟則畫出完整之E-R關聯圖(E-R Diagram)。例如學生選課的ER model如下：
{% img http://database.klab.tw/teach/img/t4_2_07.png 500 450 vi-vim-cheat-sheet %}

### 外來鍵參考圖 (relational model schema)

1. 實體轉成一個資料表
2. 多值屬性轉成一個資料表
3. 複合屬性可以轉成一個資料表，也可以只記錄複合不分拆成不同欄位。
4. 衍生屬性記錄為計算值，並增加適當的欄位
5. 一般屬性轉成資料表的欄位
6. 多對多關聯轉成一個資料表
7. 1對多關聯必須多一個外來鍵置於多的那一個資料表，已便關聯到另一個資料表的主鍵。
8. 1對1關聯時，如果其中一個實體只有單一屬性，則可併入另一個資料表中，否則獨立為一個資料表，則仍須多一個外來鍵置已便關聯到另一個資料表的主鍵。

* 例如學生選課資料表之ER model轉成綱要如下：
{% img http://database.klab.tw/teach/img/t4_2_08.png 500 450 vi-vim-cheat-sheet %}

* 外來鍵參考圖(relational model schema)如下：
{% img http://database.klab.tw/teach/img/t4_2_09.png 500 450 vi-vim-cheat-sheet %}
