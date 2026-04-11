# 寫C#程式碼

1. Create (新增)
2. Read (讀取/搜尋)
3. Update (修改 - 雖然還沒寫，但原理跟新增一樣是 UPDATE 語法)
4. Delete (刪除)

以下是完成的介面
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
