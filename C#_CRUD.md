# 寫C#程式碼

1. Create (新增)
2. Read (讀取/搜尋)
3. Update (修改 - 雖然還沒寫，但原理跟新增一樣是 UPDATE 語法)
4. Delete (刪除)

以下是完成的介面 \
<img width="817" height="477" alt="image" src="https://github.com/user-attachments/assets/a76b1785-36a7-4757-95a1-90f51a66c800" />


請先參考完整程式碼 https://github.com/chocho0130/c-practice/blob/main/C%23_sql_windows.md 
## Create (新增)

在下方 textbox 輸入 id 跟姓名 然後按下新增資料按鈕
``` c#
private void button1_Click(object sender, EventArgs e)
        {
            string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";

            // 使用 INSERT INTO 語法，@ 符號代表參數
            string sql = "INSERT INTO Users (Id, UserName) VALUES (@ID, @Name)";

            // 建立連線物件
            using (SqlConnection conn = new SqlConnection(connString))
            {
                SqlCommand cmd = new SqlCommand(sql, conn);
                // 將 TextBox 的值塞進參數裡
                cmd.Parameters.AddWithValue("@ID", txtId.Text);
                cmd.Parameters.AddWithValue("@Name", txtName.Text);

                try
                {
                    conn.Open();
                    cmd.ExecuteNonQuery(); // 執行「非查詢」指令（新增、刪除、修改用這個）
                    MessageBox.Show("新增成功！");

                    // 成功後可以呼叫讀取按鈕的方法，讓畫面自動更新
                    b1_Click(null, null);
                }
                catch (Exception ex)
                {
                    MessageBox.Show("新增失敗：" + ex.Message);
                }
            }
        }

```
## Read (讀取/搜尋)

- 這邊讀取是寫在按鈕-讀取資料，只要按下按鈕就會將資料庫資料顯示在旁邊的dataGridView
``` C#
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
- 這邊搜尋是寫在按鈕-搜尋姓名，只要在右邊textbox輸入名字然後按下搜尋姓名就可以在dataGridView 上面看到結果
``` C#
 private void button2_Click(object sender, EventArgs e)
        {
            string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";
            // 使用 WHERE 子句與 LIKE 進行模糊查詢
            string sql = "SELECT * FROM Users WHERE UserName LIKE @SearchName";
            using (SqlConnection conn = new SqlConnection(connString))
            {
                // 建立資料配接器 (Adapter)
                SqlDataAdapter adapter = new SqlDataAdapter(sql, conn);
                // % 是 SQL 的萬用字元，代表搜尋包含該文字的結果
                adapter.SelectCommand.Parameters.AddWithValue("@SearchName", "%" + txtName.Text + "%");

                // 建立一個空的資料表 (DataTable) 來接資料
                DataTable dt = new DataTable();
                // 執行並填入資料
                adapter.Fill(dt);
                // 將結果綁定到畫面的表格元件上
                dataGridView1.DataSource = dt;
            }
        }
```
# Update (修改)
因為不希望使用者手動輸入 ID 來修改。
最好的做法是：點一下 DataGridView，資料就自動出現在 TextBox 裡。 

第一階段：點擊表格自動填入 (體驗優化)
輸入以下程式碼：
``` C#
  private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
        {
            // 確保點擊的是有效的資料列 (而非標題列)
            if (e.RowIndex >= 0)
            {
                DataGridViewRow row = dataGridView1.Rows[e.RowIndex];
                // 將選中列的資料填回 TextBox
                txtId.Text = row.Cells["Id"].Value.ToString();
                txtName.Text = row.Cells["UserName"].Value.ToString();

                // 💡 專業提示：修改時 ID 通常不讓使用者改 (ReadOnly)
                txtId.ReadOnly = true;
            }
        }
```

第二階段：執行修改按鈕 
拉一個新按鈕，文字改為「修改存檔」，名稱為 btnUpdate
``` C#
private void btnUpdate_Click(object sender, EventArgs e)
        {
            string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";
            // SQL 語法：更新指定的欄位，一定要加 WHERE ID，否則會更新全表的資料！
            string sql = "UPDATE Users SET UserName = @Name WHERE Id = @ID";
            using (SqlConnection conn = new SqlConnection(connString))
            {
                SqlCommand cmd = new SqlCommand(sql, conn);
                cmd.Parameters.AddWithValue("@ID", txtId.Text);
                cmd.Parameters.AddWithValue("@Name", txtName.Text);

                try
                {
                    conn.Open();
                    int result = cmd.ExecuteNonQuery();

                    if (result > 0)
                    {
                        MessageBox.Show("修改成功！");
                        b1_Click(null, null); // 重新讀取畫面
                        txtId.ReadOnly = false;    // 恢復可輸入狀態
                    }
                }
                catch (Exception ex)
                {
                    MessageBox.Show("修改失敗：" + ex.Message);
                }
            }
        }

```
# Delete (刪除) 
這涉及到了「資料安全」與「使用者體驗」。

實作刪除通常有兩種做法：
- 手動輸入 ID 刪除：簡單，但容易打錯。
- 滑鼠點擊表格刪除：直覺，符合現代軟體操作。
這裡挑戰最專業的「點擊表格後刪除選中列」。
> 我發現點擊dataGridView1 卡卡的 
1. 介面準備
在視窗上拉一個按鈕，文字改為「刪除選中資料」，名稱命為 btnDelete。
2. 撰寫程式碼
在按鈕連點兩下，輸入以下邏輯：

``` C#
private void btnDelete_Click(object sender, EventArgs e)
        {
            // 1. 防呆：檢查有沒有選中任何一列
            if (dataGridView1.CurrentRow == null)
            {
                MessageBox.Show("請先點選表格中的一行資料！");
                return;
            }
            // 2. 取得選中那一列的 ID (假設 ID 在第一欄，索引為 0)
            string selectedId = dataGridView1.CurrentRow.Cells[0].Value.ToString();
            // 3. 再次確認 (這是專業軟體必備的警告視窗)
            DialogResult result = MessageBox.Show($"確定要刪除 ID 為 {selectedId} 的資料嗎？", "確認刪除", MessageBoxButtons.YesNo);

            if (result == DialogResult.Yes)
            {
                string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";
                string sql = "DELETE FROM Users WHERE Id = @ID";

                using (SqlConnection conn = new SqlConnection(connString))
                {
                    SqlCommand cmd = new SqlCommand(sql, conn);
                    cmd.Parameters.AddWithValue("@ID", selectedId);

                    try
                    {
                        conn.Open();
                        int rows = cmd.ExecuteNonQuery(); // 會回傳受影響的列數

                        if (rows > 0)
                        {
                            MessageBox.Show("刪除成功！");
                            // 4. 重新整理畫面
                            b1_Click(null, null);
                        }
                    }
                    catch (Exception ex)
                    {
                        MessageBox.Show("刪除失敗：" + ex.Message);
                    }
                }
            }
        }
```


# 完整程式碼 (基本)
Designer.cs
``` C#

namespace try_WindowsFormsApp1
{
    partial class Form1
    {
        /// <summary>
        /// 設計工具所需的變數。
        /// </summary>
        private System.ComponentModel.IContainer components = null;

        /// <summary>
        /// 清除任何使用中的資源。
        /// </summary>
        /// <param name="disposing">如果應該處置受控資源則為 true，否則為 false。</param>
        protected override void Dispose(bool disposing)
        {
            if (disposing && (components != null))
            {
                components.Dispose();
            }
            base.Dispose(disposing);
        }

        #region Windows Form 設計工具產生的程式碼

        /// <summary>
        /// 此為設計工具支援所需的方法 - 請勿使用程式碼編輯器修改
        /// 這個方法的內容。
        /// </summary>
        private void InitializeComponent()
        {
            this.b1 = new System.Windows.Forms.Button();
            this.dataGridView1 = new System.Windows.Forms.DataGridView();
            this.txtId = new System.Windows.Forms.TextBox();
            this.txtName = new System.Windows.Forms.TextBox();
            this.button1 = new System.Windows.Forms.Button();
            this.button2 = new System.Windows.Forms.Button();
            this.btnDelete = new System.Windows.Forms.Button();
            this.button3 = new System.Windows.Forms.Button();
            this.label1 = new System.Windows.Forms.Label();
            this.label2 = new System.Windows.Forms.Label();
            ((System.ComponentModel.ISupportInitialize)(this.dataGridView1)).BeginInit();
            this.SuspendLayout();
            // 
            // b1
            // 
            this.b1.Location = new System.Drawing.Point(219, 220);
            this.b1.Name = "b1";
            this.b1.Size = new System.Drawing.Size(111, 45);
            this.b1.TabIndex = 0;
            this.b1.Text = "讀取資料";
            this.b1.UseVisualStyleBackColor = true;
            this.b1.Click += new System.EventHandler(this.b1_Click);
            // 
            // dataGridView1
            // 
            this.dataGridView1.ColumnHeadersHeightSizeMode = System.Windows.Forms.DataGridViewColumnHeadersHeightSizeMode.AutoSize;
            this.dataGridView1.Location = new System.Drawing.Point(427, 186);
            this.dataGridView1.Name = "dataGridView1";
            this.dataGridView1.RowTemplate.Height = 24;
            this.dataGridView1.Size = new System.Drawing.Size(240, 150);
            this.dataGridView1.TabIndex = 1;
            this.dataGridView1.CellContentClick += new System.Windows.Forms.DataGridViewCellEventHandler(this.dataGridView1_CellContentClick);
            // 
            // txtId
            // 
            this.txtId.Location = new System.Drawing.Point(230, 86);
            this.txtId.Name = "txtId";
            this.txtId.Size = new System.Drawing.Size(100, 22);
            this.txtId.TabIndex = 2;
            // 
            // txtName
            // 
            this.txtName.Location = new System.Drawing.Point(451, 86);
            this.txtName.Name = "txtName";
            this.txtName.Size = new System.Drawing.Size(100, 22);
            this.txtName.TabIndex = 3;
            // 
            // button1
            // 
            this.button1.Location = new System.Drawing.Point(219, 23);
            this.button1.Name = "button1";
            this.button1.Size = new System.Drawing.Size(111, 45);
            this.button1.TabIndex = 4;
            this.button1.Text = "新增資料";
            this.button1.UseVisualStyleBackColor = true;
            this.button1.Click += new System.EventHandler(this.button1_Click);
            // 
            // button2
            // 
            this.button2.Location = new System.Drawing.Point(451, 23);
            this.button2.Name = "button2";
            this.button2.Size = new System.Drawing.Size(111, 45);
            this.button2.TabIndex = 5;
            this.button2.Text = "搜尋姓名";
            this.button2.UseVisualStyleBackColor = true;
            this.button2.Click += new System.EventHandler(this.button2_Click);
            // 
            // btnDelete
            // 
            this.btnDelete.Location = new System.Drawing.Point(650, 23);
            this.btnDelete.Name = "btnDelete";
            this.btnDelete.Size = new System.Drawing.Size(111, 45);
            this.btnDelete.TabIndex = 6;
            this.btnDelete.Text = "刪除選重資料";
            this.btnDelete.UseVisualStyleBackColor = true;
            this.btnDelete.Click += new System.EventHandler(this.btnDelete_Click);
            // 
            // button3
            // 
            this.button3.Location = new System.Drawing.Point(650, 86);
            this.button3.Name = "button3";
            this.button3.Size = new System.Drawing.Size(111, 45);
            this.button3.TabIndex = 7;
            this.button3.Text = "修改存檔";
            this.button3.UseVisualStyleBackColor = true;
            this.button3.Click += new System.EventHandler(this.btnUpdate_Click);
            // 
            // label1
            // 
            this.label1.AutoSize = true;
            this.label1.Font = new System.Drawing.Font("新細明體", 12F);
            this.label1.Location = new System.Drawing.Point(184, 92);
            this.label1.Name = "label1";
            this.label1.Size = new System.Drawing.Size(21, 16);
            this.label1.TabIndex = 8;
            this.label1.Text = "Id";
            this.label1.Click += new System.EventHandler(this.label1_Click);
            // 
            // label2
            // 
            this.label2.AutoSize = true;
            this.label2.Font = new System.Drawing.Font("新細明體", 12F);
            this.label2.Location = new System.Drawing.Point(400, 89);
            this.label2.Name = "label2";
            this.label2.Size = new System.Drawing.Size(45, 16);
            this.label2.TabIndex = 9;
            this.label2.Text = "Name";
            this.label2.UseMnemonic = false;
            // 
            // Form1
            // 
            this.AutoScaleDimensions = new System.Drawing.SizeF(6F, 12F);
            this.AutoScaleMode = System.Windows.Forms.AutoScaleMode.Font;
            this.ClientSize = new System.Drawing.Size(800, 450);
            this.Controls.Add(this.label2);
            this.Controls.Add(this.label1);
            this.Controls.Add(this.button3);
            this.Controls.Add(this.btnDelete);
            this.Controls.Add(this.button2);
            this.Controls.Add(this.button1);
            this.Controls.Add(this.txtName);
            this.Controls.Add(this.txtId);
            this.Controls.Add(this.dataGridView1);
            this.Controls.Add(this.b1);
            this.Name = "Form1";
            this.Text = "Form1";
            this.Load += new System.EventHandler(this.Form1_Load);
            ((System.ComponentModel.ISupportInitialize)(this.dataGridView1)).EndInit();
            this.ResumeLayout(false);
            this.PerformLayout();

        }

        #endregion

        private System.Windows.Forms.Button b1;
        private System.Windows.Forms.DataGridView dataGridView1;
        private System.Windows.Forms.TextBox txtName;
        private System.Windows.Forms.Button button1;
        private System.Windows.Forms.Button button2;
        private System.Windows.Forms.TextBox txtId;
        private System.Windows.Forms.Button btnDelete;
        private System.Windows.Forms.Button button3;
        private System.Windows.Forms.Label label1;
        private System.Windows.Forms.Label label2;
    }
}

```

```C#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Data.SqlClient; // 💡 記得手動加上這一行引用

namespace try_WindowsFormsApp1
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {

        }

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

        private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
        {
            // 確保點擊的是有效的資料列 (而非標題列)
            if (e.RowIndex >= 0)
            {
                DataGridViewRow row = dataGridView1.Rows[e.RowIndex];
                // 將選中列的資料填回 TextBox
                txtId.Text = row.Cells["Id"].Value.ToString();
                txtName.Text = row.Cells["UserName"].Value.ToString();

                // 💡 專業提示：修改時 ID 通常不讓使用者改 (ReadOnly)
                txtId.ReadOnly = true;
            }
        }

        private void button1_Click(object sender, EventArgs e)
        {
            string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";

            // 使用 INSERT INTO 語法，@ 符號代表參數
            string sql = "INSERT INTO Users (Id, UserName) VALUES (@ID, @Name)";

            // 建立連線物件
            using (SqlConnection conn = new SqlConnection(connString))
            {
                SqlCommand cmd = new SqlCommand(sql, conn);
                // 將 TextBox 的值塞進參數裡
                cmd.Parameters.AddWithValue("@ID", txtId.Text);
                cmd.Parameters.AddWithValue("@Name", txtName.Text);

                try
                {
                    conn.Open();
                    cmd.ExecuteNonQuery(); // 執行「非查詢」指令（新增、刪除、修改用這個）
                    MessageBox.Show("新增成功！");

                    // 成功後可以呼叫讀取按鈕的方法，讓畫面自動更新
                    b1_Click(null, null);
                }
                catch (Exception ex)
                {
                    MessageBox.Show("新增失敗：" + ex.Message);
                }
            }
        }

        private void button2_Click(object sender, EventArgs e)
        {
            string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";
            // 使用 WHERE 子句與 LIKE 進行模糊查詢
            string sql = "SELECT * FROM Users WHERE UserName LIKE @SearchName";
            using (SqlConnection conn = new SqlConnection(connString))
            {
                // 建立資料配接器 (Adapter)
                SqlDataAdapter adapter = new SqlDataAdapter(sql, conn);
                // % 是 SQL 的萬用字元，代表搜尋包含該文字的結果
                adapter.SelectCommand.Parameters.AddWithValue("@SearchName", "%" + txtName.Text + "%");

                // 建立一個空的資料表 (DataTable) 來接資料
                DataTable dt = new DataTable();
                // 執行並填入資料
                adapter.Fill(dt);
                // 將結果綁定到畫面的表格元件上
                dataGridView1.DataSource = dt;
            }
        }

        private void btnDelete_Click(object sender, EventArgs e)
        {
            // 1. 防呆：檢查有沒有選中任何一列
            if (dataGridView1.CurrentRow == null)
            {
                MessageBox.Show("請先點選表格中的一行資料！");
                return;
            }
            // 2. 取得選中那一列的 ID (假設 ID 在第一欄，索引為 0)
            string selectedId = dataGridView1.CurrentRow.Cells[0].Value.ToString();
            // 3. 再次確認 (這是專業軟體必備的警告視窗)
            DialogResult result = MessageBox.Show($"確定要刪除 ID 為 {selectedId} 的資料嗎？", "確認刪除", MessageBoxButtons.YesNo);

            if (result == DialogResult.Yes)
            {
                string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";
                string sql = "DELETE FROM Users WHERE Id = @ID";

                using (SqlConnection conn = new SqlConnection(connString))
                {
                    SqlCommand cmd = new SqlCommand(sql, conn);
                    cmd.Parameters.AddWithValue("@ID", selectedId);

                    try
                    {
                        conn.Open();
                        int rows = cmd.ExecuteNonQuery(); // 會回傳受影響的列數

                        if (rows > 0)
                        {
                            MessageBox.Show("刪除成功！");
                            // 4. 重新整理畫面
                            b1_Click(null, null);
                        }
                    }
                    catch (Exception ex)
                    {
                        MessageBox.Show("刪除失敗：" + ex.Message);
                    }
                }
            }
        }

        private void btnUpdate_Click(object sender, EventArgs e)
        {
            string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";
            // SQL 語法：更新指定的欄位，一定要加 WHERE ID，否則會更新全表的資料！
            string sql = "UPDATE Users SET UserName = @Name WHERE Id = @ID";
            using (SqlConnection conn = new SqlConnection(connString))
            {
                SqlCommand cmd = new SqlCommand(sql, conn);
                cmd.Parameters.AddWithValue("@ID", txtId.Text);
                cmd.Parameters.AddWithValue("@Name", txtName.Text);

                try
                {
                    conn.Open();
                    int result = cmd.ExecuteNonQuery();

                    if (result > 0)
                    {
                        MessageBox.Show("修改成功！");
                        b1_Click(null, null); // 重新讀取畫面
                        txtId.ReadOnly = false;    // 恢復可輸入狀態
                    }
                }
                catch (Exception ex)
                {
                    MessageBox.Show("修改失敗：" + ex.Message);
                }
            }
        }

        private void label1_Click(object sender, EventArgs e)
        {

        }
    }
}
```

#進階

封裝：把連線字串寫在一個統一的地方，而不是每個按鈕都寫一遍 

這邊只用最簡單的
封裝在類別（Class）裡 (適合初學者)
先建立一個專門管理連線的「工具箱」。
在專案按 右鍵 -> 加入 -> 類別 (Class)。
取名為 DBTool.cs。
寫入以下程式碼：
```C#
部分程式碼 寫在namespace 裡面
namespace try_WindowsFormsApp1
{
    // 把工具類別放在 namespace 裡面，這樣它就屬於這個專案的一部分
    public class DBTool
    {
        private static string connString = @"Server=.\SQLEXPRESS; Database=MyDemoDB; Integrated Security=True";

        public static SqlConnection GetConnection()
        {
            return new SqlConnection(connString);
        }
    }

    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void b1_Click(object sender, EventArgs e)
        {
            // 使用方法：直接呼叫 DBTool.GetConnection()
            try 
            {
                using (SqlConnection conn = DBTool.GetConnection())
                {
                    string sql = "SELECT * FROM Users";
                    SqlDataAdapter adapter = new SqlDataAdapter(sql, conn);
                    DataTable dt = new DataTable();
                    adapter.Fill(dt);
                    dataGridView1.DataSource = dt;
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("連線失敗：" + ex.Message);
            }
        }
    }
}
```

> 在程式碼中，return new SqlConnection(connString); 裡面的這個 new 關鍵字，代表：
> 向系統申請了一塊新記憶體。
> 產生了一個全新的 SqlConnection 實體。
> 當在按鈕裡寫 using (SqlConnection conn = ...) 時，這個連線會被開啟、使用，然後在 using 結束時自動關閉並釋放

> 為什麼要用 using？
> 確保 SqlConnection 在執行完後會自動釋放，避免佔用資料庫連線數。

> 為什麼不直接組字串 VALUES (" + txtId.Text + ", ...)?
> 標準答案： 這是為了防止 SQL 注入 (SQL Injection)。如果直接組字串，駭客可以在 TextBox 輸入惡意代碼來刪除整個資料庫。使用 Parameters 是最安全的做法。

> ExecuteNonQuery vs Fill ?
> ExecuteNonQuery：用於「動手操作資料」（新增、修改、刪除），回傳受影響的列數。
> Fill：用於「讀取資料」到記憶體中供介面顯示。

# 學習語法 C#
這邊就是簡單的介紹C# 負責打底的

## 語法特性
1. static (靜態)
在寫 public static SqlConnection GetConnection() 時，那個 static 是靈魂。
- 白話解釋：static 代表這東西是「公共的」。
- 不加 static：你需要先 new 出一個物件（買一台筆電），才能使用它的功能（開機）。
- 加上 static：就像街上的公用電話，不需要 new（不需要自己買），直接用 DBTool.GetConnection() 就能呼叫。

> 為什麼 DBTool 要用 static？
> 因為連線資訊是全域共用的，不需要為了拿一個字串就特地 new 一個物件，這樣可以節省記憶體。

2. using (釋放)
寫 using (SqlConnection conn = ...) 的地方。
- 白話解釋：using 就像是「租借合約」。
- 功能：確保括號 { } 結束後，那個物件（連線）會被立刻關掉並銷毀。
> 不用 using 會怎樣？ 
> 連線會一直開著不關，導致連線池 (Connection Pool) 被佔滿。久了之後資料庫會拒絕連線，程式就會崩潰（這就是俗稱的 Memory Leak）。 

3. namespace (命名空間)
我們的程式開頭一定有 namespace try_WindowsFormsApp1。
- 白話解釋：它就像是「資料夾」或「姓氏」。
- 功能：防止命名衝突。如果有兩個人都寫了 Form1，只要他們在不同的 namespace 下，電腦就不會搞混。
- 例子：台北市.中山路 和 台中市.中山路 是不同的地方。

4. try...catch (攔截錯誤)
這是我們寫 ex.Message 的地方。
流程：
- try：試試看執行這段程式碼（可能會失敗）。
- catch：萬一失敗了，別讓程式閃退，把錯誤抓過來處理。
- finally (選用)：不管成功還是失敗，最後一定會執行的地方（通常用來關閉檔案）。

## 基本語法

這跟C ++有些相似，但也有許多地方不同，這邊只是簡單列出一些，詳細還是推薦問AI、上網搜尋或是買書來看
> 可以去熟悉物件導向

1. 📦變數與資料型別 (Data Types)
- int：整數（例：123）。常用於 ID、年齡。
- string：字串（例："小明"）。必須用 雙引號。
- double / decimal：小數（例：3.14）。decimal 常用於金錢，因為最精準。
- bool：布林值。只有 true（真）或 false（假）。 
2. 🛠️方法 (Method) — 程式的動作
結構：存取修飾詞 + 回傳類型 + 名稱 + (參數)
```c#
我們在按鈕寫的 btnLoad_Click 就是一個方法。
- 結構：存取修飾詞 + 回傳類型 + 名稱 + (參數)
public int Add(int a, int b) 
{
    return a + b; // 回傳兩個整數相加的結果
}

```
void：如果在程式碼看到方法寫 void，代表這個動作不回傳任何東西（做完就結束了）。 

3. 🚦控制流程 (Logic) 
這決定程式要走哪一條路。
- If...Else (判斷)
```c#
if (txtName.Text == "") 
{
    MessageBox.Show("請輸入姓名");
}
else 
{
    // 執行新增
}

```
> 麻煩點在於搭配物件

- Foreach (迴圈)
要處理一整排資料（例如 DataGridView 裡的每一列）時：
```C#
foreach (var row in dataGridView1.Rows) 
{
    // 每一列都拿出來處理一次
}
```
跟C/C++語法有點差異

4. 🏛️類別與物件 (Class & Object)
這是 C# 物件導向 (OOP) 的靈魂。
- 類別 (Class)：藍圖（例如：設計圖：汽車）。
- 物件 (Object)：實體（例如：那台紅色的賓士）。
- new 關鍵字：就是「照著藍圖造出一台車」的動作。 
  - SqlConnection conn = new SqlConnection();
  - 這行就是在說：我要照著連線藍圖，造出一個真正的連線物件。
5. 存取修飾詞 (Access Modifiers)
這決定了誰可以看你的程式碼。
- public：全公開，誰都能用。
- private：私有的，只有在同一個 { } 裡面的人能用（安全第一）。
6. 轉換型態
> string 和 int 的差別在哪裡？如果我把數字 123 寫成 "123" 會怎樣？
> int 是數值型別，可以用來做加減乘除的運算；而 "123" 加上了雙引號就變成了字串型別 string，它被視為文字，無法直接進行數學運算。在 C# 這種強型別語言中，兩者不能混用，必須經過轉型（如 int.Parse()）才能互相轉換。

小範例
```C#
private void b1_Click(object sender, EventArgs e) // 這是「方法」，回傳「void」
{
    string name = txtName.Text; // 這是「字串變數」
    
    if (name == "Admin") // 這是「控制流程」的判斷
    {
        MessageBox.Show("歡迎管理員"); //會談出一個視窗顯示資訊
    }
}
```

## ADO.NET 

ADO.NET 的運作原理可以拆解為兩個核心架構：「連線式」與「離線式」 \
1️⃣ 核心三劍客（連線式元件）
這三個元件在運作時，必須保持資料庫連線開啟。
1. SqlConnection (通道)
- 功能：建立 C# 與 SQL Server 之間的橋樑。
- 原理：它負責處理身分驗證（帳密）與網路協議。
- 面試點：它是最耗資源的物件，所以一定要放在 using 裡確保關閉。
2. SqlCommand (指令箱)
- 功能：裝載 SQL 語句（如 SELECT）並發送。
- 原理：它會把指令傳給 SQL Server 的引擎去執行。
- 面試點：使用 Parameters（參數化查詢）是為了防止 SQL Injection（SQL 注入）。
3. SqlDataReader (單向輸送帶)
- 功能：一筆一筆快速讀取資料。
- 原理：它是唯讀、只能向前的。它不會把資料存下來，而是讀一筆丟一筆。
- 優點：速度極快，省記憶體。 

2️⃣ 緩衝快取（離線式元件)
這就是我們寫程式用來填滿表格的核心，它們可以在斷開連線後繼續使用資料。

4. SqlDataAdapter (轉接器/水管)
- 角色：連線式與離線式之間的「橋樑」。
- 原理：它內部其實包含了 SelectCommand。當你呼叫 .Fill() 時，它會自動：
  - Open 連線。
  - 執行 SQL 指令。
  - 把撈到的資料塞進 DataTable。
  - 自動 Close 連線。
5. DataTable / DataSet (虛擬表格)
- 角色：存在於記憶體中的 Excel 表。
- 原理：一旦 DataAdapter 把資料倒進來，資料就存在電腦記憶體裡了。這時就算把 SQL Server 關掉，你的 DataGridView 依然能看到資料。 

3️⃣ 具體流程圖 (以你的讀取功能為例)
當我們按下按鈕時，這幾秒內發生的事：
1. Request: SqlConnection 撥通電話。
2. Order: SqlCommand 告訴資料庫：「我要 SELECT * FROM Users」。
3. Transport: SqlDataAdapter 拿著桶子（DataTable）去接資料。
4. Fill: 資料填滿 DataTable 後，電話（Connection）掛斷。
5. Display: DataGridView.DataSource = dt（把桶子裡的資料倒給螢幕看）。

## 筆記
Q: SqlCommand 和 SqlDataAdapter 它們之間的關係 (可以看到CURD程式碼裡面我們有的用SqlDataAdapter或是SqlCommand )  
A: 簡單來說：SqlDataAdapter 其實偷偷把 SqlCommand 藏在它肚子裡面了。 \
解釋:  
1. 當我們寫 new SqlDataAdapter(sql, conn) 時，SqlDataAdapter 為了讓我們少寫幾行程式碼，它在後台自動幫我們做了一件事：  
它幫我們 new 了一個 SqlCommand。

它把你的 sql 語句和 conn 連線物件都塞進了這個隱形的 Command 裡。
所以，這兩段程式碼的效果是完全一樣的：
【做法 A：簡寫版（我們現在用的）】
```C#
SqlDataAdapter adapter = new SqlDataAdapter(sql, conn);

```
【做法 B：完整拆解版】
```C#
SqlCommand cmd = new SqlCommand(sql, conn); // 先建指令
SqlDataAdapter adapter = new SqlDataAdapter(cmd); // 把指令交給外送員

```
2. 為什麼要有這兩種寫法？
既然簡寫版這麼方便，為什麼還要有「做法 B」呢？
當我們需要「細節設定」時用 SqlCommand：
如果需要設定 指令逾時時間 (CommandTimeout)，或者要加上 參數 (Parameters) 來防止 SQL 注入時，你通常會先建立 SqlCommand：
```C#
SqlCommand cmd = new SqlCommand("SELECT * FROM Users WHERE Id = @id", conn);
cmd.Parameters.AddWithValue("@id", 1); // 這種細節設定，DataAdapter 的簡寫版做不到
SqlDataAdapter adapter = new SqlDataAdapter(cmd);
```
3. 三劍客的「巢狀」關係
可以這樣想像：
SqlConnection 是手機。
SqlCommand 是簡訊內容。
SqlDataAdapter 是自動發報機。
自動發報機 (Adapter) 需要有 手機 (Connection) 和 內容 (Command) 才能工作。如果你直接給它文字和手機，它會自己組裝成一條簡訊發出去。

🚩 小結 

- 讀取資料並顯示 (SELECT)：通常用 SqlDataAdapter（因為它內建了 Command 且支援 Fill 到 DataTable）。
- 動手修改資料 (INSERT/UPDATE/DELETE)：通常直接用 SqlCommand（因為不需要透過 Adapter 轉一手，直接 Execute 就好）。
