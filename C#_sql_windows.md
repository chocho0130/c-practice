# C# 連結「資料庫」 學習流程


## 🛠️ 第一步：環境搭建（VS 2019 必備）

在開始寫程式前，要先安裝了這三個工具：
SQL Server Express：免費的資料庫引擎（後端的核心）。
SSMS (SQL Server Management Studio)：管理資料庫的圖形介面（就像 SQL 界的檔案總管）。
Visual Studio

> 進階 : 嘗試MAC&跨電腦串SQL


## 📂 第二步：SQL Server 必學清單 (由淺入深)

SQL要會的基本操作:

1. 基礎 CRUD (最基本)
SELECT：撈資料。
INSERT：新增資料。
UPDATE：更新資料。
DELETE：刪除資料。

2. 進階查詢 (面試必考)
JOIN：如何把「員工表」跟「部門表」關聯起來（Inner Join, Left Join）。
WHERE vs HAVING：篩選資料的差別。
GROUP BY / Aggregate：計算總和 (SUM)、平均 (AVG)、計數 (COUNT)。

3. 資料庫設計 (專業觀念)
Primary Key (主鍵)：每一列資料的身份證。
Foreign Key (外鍵)：資料表之間的關聯。
正規化 (Normalization)：如何設計表格才不會讓資料重複雜亂。

## 💻 第三步：WinForms 連結 SQL 的實戰演練

1. 在 WinForms 拉一個 Button 和一個 DataGridView (顯示表格)。
2. 程式碼範例：

``` C#
using System.Data.SqlClient; // 💡 記得手動加上這一行引用
private void b1_Click(object sender, EventArgs e)
        {
            // 1. 連線字串 (這是通往資料庫的地址)
            // 💡 Server 請填你圖片中的 .\SQLEXPRESS; Database 填你建的資料庫名
            string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";
            string sql = "SELECT * FROM Users";
            try
            {
                // 3. 建立連線物件
                using (SqlConnection conn = new SqlConnection(connString))
                {
                    // 4. 建立資料配接器 (Adapter)
                    SqlDataAdapter adapter = new SqlDataAdapter(sql, conn);

                    // 5. 建立一個空的資料表 (DataTable) 來接資料
                    DataTable dt = new DataTable();

                    // 6. 執行並填入資料
                    adapter.Fill(dt);

                    // 7. 將結果綁定到畫面的表格元件上
                    dataGridView1.DataSource = dt;

                    MessageBox.Show("資料讀取成功！");
                }
            }
            catch (Exception ex)
            {
                // 如果出錯了，會跳出訊息告訴你原因
                MessageBox.Show("發生錯誤：" + ex.Message);
            }
        }
```


# C# 連結「資料庫」 安裝流程

## 🛠️ 第一步：成功連入 SQL Server

### 🚀 如何安裝 SQL Server 引擎 (真正的後端)
請跟著以下步驟：
1. 下載 SQL Server 2019 Express
- 前往 微軟官方下載頁面。
- 點擊「下載」並執行安裝程式。
2. 選擇「基本」安裝 (Basic)
- 對於初學者和面試準備來說，「基本」安裝就完全夠用了。
- 它會自動幫你設定好一切，不需要去管複雜的網路設定。
3. 注意安裝完成後的資訊 🚩
- 安裝完後，會出現一個畫面，請截圖或記下這兩個資訊：
- 實例名稱 (Instance Name)：預設通常是 SQLEXPRESS。
- 連線字串 (Connection String)：這以後要貼進 C# 程式碼。

### 🛠️ 安裝完後，如何連線？
安裝好「SQL Server 2019 Express」後，安裝 SSMS ：
1. 下載 SSMS
- 前往 微軟官方下載頁面。
- 點擊「下載」並執行安裝程式。
Win +s 輸入 SSMS 點開藍色軟體
伺服器名稱：輸入 .\SQLEXPRESS (小數點代表本機)。
驗證：選擇 Windows 驗證。
點擊 「連線」。

>「如果我的 SQL Server 裝在雲端，我的 SSMS 還能連嗎？」 \
> 答案：可以。只要你有對方的 IP 地址、帳號 和 密碼。 
> 觀念：SSMS 是一個「用戶端 (Client)」，它可以連線到世界上任何一個有開啟權限的 SQL Server 伺服器。

## 📂 第二步：建立練習用的資料表 (手動操作)
第二步，要建立一個資料庫 (Database) 和一張資料表 (Table)，並填入資料，這樣 C# 才有東西可以抓。
請依照以下順序操作：

1. 建立資料庫 (Database)
這是存放資料的「大抽屜」。
- 在左側「物件總管」中，對著 Databases (資料庫) 資料夾按 滑鼠右鍵。
- 選擇 「新資料庫...」 (New Database...)。
- 在彈出的視窗中，「資料庫名稱」輸入：MyDemoDB。
- 點擊 「確定」。
- !此時你會在左邊看到 MyDemoDB 資料夾出現了。

2. 建立資料表 (Table)
這是存放資料的「表格」。
- 展開 MyDemoDB 資料夾。
- 對著裡面的 「資料表」 (Tables) 按 滑鼠右鍵。
- 選擇 「資料表...」 (Table...)。
這時中間會出現設計畫面，請輸入以下兩個欄位：
<img width="931" height="197" alt="image" src="https://github.com/user-attachments/assets/23d2e574-ae6d-43ad-95ad-af69ceee882b" />

- 關鍵操作：在 Id 這一行按 右鍵 -> 「設定主索引鍵 (Set Primary Key)」。
- 按下 Ctrl + S 儲存，並將資料表命名為：Users。

3. 輸入測試資料
我們要手動塞一點資料進去，等一下 C# 抓到才會顯示。
- 在左側重新展開「資料表」資料夾（如果沒看到，按一下上方圓形箭頭重新整理）。
- 在 dbo.Users 上按 滑鼠右鍵。
- 選擇 「編輯前 200 列」 (Edit Top 200 Rows)。
  - 直接在格子裡輸入資料，例如：
  - Id: 1, UserName: 小明
  - Id: 2, UserName: 阿華
- 輸入完直接點下一行，資料就存進去了。

4. 驗證資料（用 SQL 語法）
寫一次：
- 點擊工具列上方的 「新增查詢」 (New Query)。
- 在右邊空白處輸入：
```sql
SELECT * FROM Users
```
- 點擊上方綠色的 「執行」 (Execute) 按鈕。
- 如果下方出現了小明和阿華，代表你的後端資料端完全搞定了！

## 💻 第三步：寫C#程式碼
 WinForms 程式與資料庫正式「通話」。
 開啟 Visual Studio 2019，建立一個 WinForms (Windows Forms App) 專案，然後跟著以下步驟做： \
1️⃣ 設計 UI 介面
1. 在左側工具箱 (Toolbox) 找尋 DataGridView，把它拉到視窗中間（這是用來顯示表格的）。
2.拉一個 Button 到視窗上，將它的文字 (Text) 改成「讀取資料」。
> 可以先切換到視窗然後點畫面左邊的工具列，可以直接拉元件出來 \
2️⃣ 安裝必要套件 (NuGet)
在 VS 2019 中，C# 需要一個「翻譯官」才能跟 SQL Server 溝通：
1. 在右側「方案總管」的專案名稱上按 右鍵 -> 管理 NuGet 套件。
2. 切換到「瀏覽」標籤，搜尋並安裝：Microsoft.Data.SqlClient。
3️⃣ 撰寫 C# 程式碼
在剛才拉的 按鈕 上連點兩下，進入程式碼畫面，將內容替換成以下程式碼：
```C#
using System;
using System.Data;
using System.Windows.Forms;
using System.Data.SqlClient; // 💡 記得手動加上這一行引用

namespace YourProjectName
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            // 1. 連線字串 (這是通往資料庫的地址)
            // 💡 Server 請填你圖片中的 .\SQLEXPRESS; Database 填你建的資料庫名
            string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";

            // 2. 撰寫 SQL 指令
            string sql = "SELECT * FROM Users";

            try
            {
                // 3. 建立連線物件
                using (SqlConnection conn = new SqlConnection(connString))
                {
                    // 4. 建立資料配接器 (Adapter)
                    SqlDataAdapter adapter = new SqlDataAdapter(sql, conn);

                    // 5. 建立一個空的資料表 (DataTable) 來接資料
                    DataTable dt = new DataTable();

                    // 6. 執行並填入資料
                    adapter.Fill(dt);

                    // 7. 將結果綁定到畫面的表格元件上
                    dataGridView1.DataSource = dt;

                    MessageBox.Show("資料讀取成功！");
                }
            }
            catch (Exception ex)
            {
                // 如果出錯了，會跳出訊息告訴你原因
                MessageBox.Show("發生錯誤：" + ex.Message);
            }
        }
    }
}
```
4️⃣ 測試執行
1. 按下鍵盤 F5 執行程式。
2. 點擊按鈕。
3. 成果展現：你應該會看到 DataGridView 裡面出現了你在 SSMS 裡手動輸入的「小明」和「阿華」！
