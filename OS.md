## 1. 🧩 OS 基本概念
先用AI整理相關知識，列出可能會考得還有重要的

### 📌 定義
作業系統（Operating System, OS）是介於硬體與應用程式之間的管理者，
負責管理系統資源（CPU、記憶體、I/O），並提供程式執行的環境。

👉 核心概念：
- Resource Manager（資源管理）
- Abstraction（抽象化硬體）

---

### ⚙️ 運作流程
（以程式執行為例）

1. 使用者執行程式（User Space）
2. 程式透過 **System Call** 請求 OS 服務
3. CPU 切換到 **Kernel Mode**
4. OS（Kernel）處理請求（如檔案存取、記憶體分配）
5. 完成後回到 **User Mode**
6. 繼續執行程式

👉 補充：
- 硬體事件會透過 **Interrupt** 通知 OS
- 軟體請求則透過 **System Call / Trap**

---

### ⚠️ 問題
（這裡是面試官會挖的點🔥）

- **Context Switch 開銷**
  - 切換 process / thread 需要保存狀態 → 有成本

- **User / Kernel 切換成本**
  - System call 頻繁會影響效能

- **安全性問題**
  - 若沒有 mode 區分，應用程式可能直接操作硬體

---

### 💬 面試 QA

Q: 作業系統是什麼？  
A: OS 是管理硬體資源並提供程式執行環境的系統，核心功能是資源管理與抽象化。

---

Q: Kernel 是什麼？  
A: Kernel 是 OS 的核心部分，負責 CPU、記憶體與裝置管理，並運行在 kernel mode。

---

Q: User mode vs Kernel mode 差別？  
A:  
- User mode：限制權限，不能直接操作硬體  
- Kernel mode：完整權限，可存取所有資源  

👉 目的是提升安全性與穩定性

---

Q: System call 是什麼？  
A: System call 是 user space 程式向 OS 請求服務的介面，例如檔案讀寫。

---

Q: Interrupt vs Trap 差別？  
A:  
- Interrupt：來自硬體（例如鍵盤、I/O）  
- Trap：來自軟體（例如 system call、exception）

---

### 🔥 延伸追問

- 為什麼需要 User / Kernel mode？
- System call 會帶來什麼成本？
- 如果沒有 OS，會發生什麼事？
- 為什麼不能讓程式直接操作硬體？
- Interrupt 發生時 CPU 做了什麼？

👉 進階（加分🔥）
- Interrupt handling 流程
- ISR（Interrupt Service Routine）
- context switch 與 interrupt 的關係

## 2. 🧩 Process & Thread

### 📌 定義

**Process（行程）**  
一個正在執行的程式實例，擁有獨立的記憶體空間與系統資源。

**Thread（執行緒）**  
Process 內的一個執行單位，共享同一個記憶體空間與資源。

👉 核心差異：
- Process：資源分配單位（heavyweight）
- Thread：CPU 排程單位（lightweight）

---

### ⚙️ 運作流程

（以 Process 切換為例）

1. CPU 正在執行 Process A
2. 發生 **Interrupt / System Call / Time Slice 用完**
3. OS 儲存 Process A 狀態（存入 PCB）
4. 選擇下一個 Process（Scheduler）
5. 載入 Process B 狀態
6. CPU 開始執行 Process B

👉 Thread 類似，但切換成本較低（因為共享記憶體）

---

### ⚠️ 問題

（這區超重要🔥 面試官很愛問）

#### 1️⃣ Context Switch Overhead
- Process 切換需要保存/載入大量狀態
- 成本較高

---

#### 2️⃣ Race Condition
- 多個 Thread 同時存取共享資源
- 導致資料錯誤

---

#### 3️⃣ Synchronization 問題
- 需要 mutex / semaphore 控制

---

#### 4️⃣ Deadlock
- 多個 thread/process 互相等待資源

---

#### 5️⃣ Thread 安全性
- shared memory 容易出 bug

---

### 💬 面試 QA

Q: Process 跟 Thread 差在哪？  
A:  
- Process 有獨立記憶體空間，切換成本高  
- Thread 共享記憶體，切換成本低但需處理同步問題  

---

Q: 為什麼 Thread 比 Process 輕量？  
A:  
因為 thread 共享 process 的記憶體與資源，不需要重新分配與切換整個地址空間。

---

Q: Context Switch 發生什麼事？  
A:  
OS 會保存目前 process 的 CPU 狀態（暫存器、PC 等）到 PCB，  
再載入下一個 process 的狀態並繼續執行。

---

Q: 什麼是 PCB？  
A:  
PCB（Process Control Block）是用來儲存 process 狀態的資料結構，例如：
- program counter
- registers
- memory info
- scheduling info

---

Q: 多 Thread 有什麼問題？  
A:  
主要是 race condition、deadlock、以及 synchronization 的困難。

---

### 🔥 延伸追問

- Thread 是存在 user space 還是 kernel space？
- User-level thread vs Kernel-level thread 差別？
- Thread scheduling 是誰負責？
- 什麼是 critical section？
- 為什麼會發生 race condition？
  
👉 進階（加分🔥）
- Thread model（1:1、N:1、M:N）
- false sharing
- lock-free programming（少數會問）

## 3. 🧩 Scheduling（排程）

### 📌 定義
Scheduling 是作業系統決定 **CPU 要分配給哪個 Process / Thread 執行** 的機制。

👉 目標：
- 提高 CPU 使用率
- 降低等待時間（Waiting Time）
- 提升回應速度（Response Time）
- 確保公平性（Fairness）

---

### ⚙️ 運作流程

1. 多個 Process 進入 **Ready Queue**
2. Scheduler 從 Queue 中選擇一個 Process
3. Dispatch（分配 CPU）
4. Process 執行
5. 若發生以下情況：
   - Time slice 用完
   - I/O request
   - Interrupt
6. 進行 Context Switch，切換到下一個 Process

---

### ⚠️ 問題

#### 1️⃣ Starvation（飢餓）
某些 Process 長期拿不到 CPU

---

#### 2️⃣ Context Switch Overhead
頻繁切換會降低效能

---

#### 3️⃣ 不公平（Fairness）
某些演算法可能偏向特定 Process

---

#### 4️⃣ 無法同時最佳化所有指標
👉 throughput / response time / fairness 互相 trade-off

---

### 💬 面試 QA

Q: 常見 Scheduling 演算法有哪些？  
A:  
- FCFS（First Come First Serve）  
- SJF（Shortest Job First）  
- Round Robin  
- Priority Scheduling  

---

Q: Preemptive vs Non-preemptive 差別？  
A:  
- Preemptive：OS 可強制中斷 Process（如 RR）  
- Non-preemptive：Process 自己結束才釋放 CPU（如 FCFS）

---

Q: Round Robin 為什麼公平？  
A:  
因為每個 Process 都會分配到固定時間片（time slice），輪流執行。

---

Q: SJF 的優點與缺點？  
A:  
- 優點：平均 waiting time 最低  
- 缺點：需要預測執行時間，且可能造成 starvation  

---

Q: 什麼是 Starvation？  
A:  
某些低優先權 Process 長時間無法取得 CPU

---

Q: 如何解決 Starvation？  
A:  
使用 **Aging（老化）**，逐漸提高等待時間長的 Process 優先權

---

### 🔥 延伸追問

- Time slice 太大或太小會怎樣？
- Round Robin 的效能取決於什麼？
- Priority Scheduling 會有什麼問題？
- Scheduler 跟 Dispatcher 差別？
- Real-time system 用哪種 scheduling？

👉 進階（加分🔥）
- Multi-level queue
- Multi-level feedback queue（MLFQ）
- Linux CFS（Completely Fair Scheduler）

---

### 📊 常見演算法整理（面試速背）

| 演算法 | 特點 | 缺點 |
|--------|------|------|
| FCFS | 簡單 | convoy effect |
| SJF | 最佳平均等待時間 | 需預測時間、可能 starvation |
| RR | 公平 | context switch 多 |
| Priority | 彈性高 | starvation |  

## 4. 🧩 Synchronization（同步）

### 📌 定義
Synchronization 是用來控制多個 Process / Thread **安全地存取共享資源**的機制，
避免同時存取造成資料錯誤。

👉 核心問題：
- 多個執行單位同時操作同一資源（shared resource）

---

### ⚙️ 運作流程

（以使用 Mutex 為例）

1. Thread A 嘗試取得 lock（mutex lock）
2. 若 lock 可用 → 進入 critical section
3. Thread B 嘗試取得 lock → 被阻塞（blocked）
4. Thread A 執行完 → 釋放 lock
5. Thread B 取得 lock → 進入 critical section

👉 核心概念：
- 一次只允許一個 thread 進入 critical section

---

### ⚠️ 問題

#### 1️⃣ Race Condition（競爭條件）
多個 thread 同時存取共享資源，導致結果不正確

---

#### 2️⃣ Deadlock（死鎖）
多個 thread 互相等待資源，永遠卡住

---

#### 3️⃣ Starvation（飢餓）
某些 thread 長期拿不到資源

---

#### 4️⃣ Priority Inversion（優先權反轉）
低優先權 thread 持有 lock，高優先權 thread 被阻塞

---

#### 5️⃣ Lock Overhead
過多 lock 會影響效能

---

### 💬 面試 QA

Q: 為什麼需要 Synchronization？  
A:  
因為多個 thread 會共享資源，如果沒有同步機制，會產生 race condition，導致資料錯誤。

---

Q: 什麼是 Critical Section？  
A:  
程式中存取共享資源的區域，一次只能讓一個 thread 執行。

---

Q: Mutex vs Semaphore 差別？  
A:  
- Mutex：只能一個 thread 使用（binary lock）  
- Semaphore：可允許多個 thread（計數器控制）

---

Q: Spinlock 是什麼？  
A:  
Thread 不會睡眠，而是不斷檢查 lock（busy waiting），適合短時間等待。

---

Q: Race Condition 怎麼解？  
A:  
使用 synchronization 機制，例如 mutex、semaphore、lock。

---

Q: Deadlock 四大條件？  
A:  
1. Mutual Exclusion  
2. Hold and Wait  
3. No Preemption  
4. Circular Wait  

---

### 🔥 延伸追問

- Mutex 跟 Spinlock 什麼時候用？
- Semaphore 可以當 Mutex 用嗎？
- 如何避免 Deadlock？
- Priority inversion 怎麼解？
- lock 太多會發生什麼事？

👉 進階（加分🔥）
- Producer-Consumer 問題
- Readers-Writers 問題
- Lock-free / wait-free
- Memory barrier（少數高階會問）

## 5. 🧩 Memory Management（記憶體管理）

### 📌 定義
Memory Management 是作業系統負責管理主記憶體（RAM），
確保多個 Process 可以**安全且有效率地使用記憶體**。

👉 核心目標：
- Isolation（隔離不同 process）
- Efficient utilization（提高使用率）
- Abstraction（提供虛擬記憶體）

---

### ⚙️ 運作流程

（以 Virtual Memory + Paging 為例）

1. CPU 產生 **Virtual Address**
2. 經由 **MMU（Memory Management Unit）**
3. 查詢 **Page Table**
4. 將 Virtual Address 轉換為 **Physical Address**
5. 存取 RAM

👉 若找不到（Page Fault）：
6. OS 介入
7. 從 Disk（swap）載入資料到 RAM
8. 更新 Page Table
9. 重新執行指令

---

### ⚠️ 問題

#### 1️⃣ Fragmentation（記憶體碎片）

- Internal Fragmentation（內部碎片）  
  👉 固定大小分頁造成空間浪費  

- External Fragmentation（外部碎片）  
  👉 記憶體不連續，無法分配大區塊  

---

#### 2️⃣ Page Fault Overhead
頻繁 page fault 會嚴重影響效能

---

#### 3️⃣ Thrashing（抖動🔥）
系統大部分時間在換頁（swap），幾乎沒有真正執行程式

---

#### 4️⃣ Page Replacement 問題
需要選擇要替換哪一頁：
- FIFO
- LRU
- Optimal（理論最佳）

---

#### 5️⃣ TLB Miss
TLB 查不到 → 需要查 page table → 變慢

---

### 💬 面試 QA

Q: 虛擬記憶體是什麼？  
A:  
讓每個 Process 以為自己擁有一個連續且足夠大的記憶體空間，實際上由 OS 負責映射到實體記憶體。

---

Q: Paging 是什麼？  
A:  
將記憶體分成固定大小的 page 與 frame，透過 page table 進行位址轉換。

---

Q: Paging vs Segmentation 差別？  
A:  
- Paging：固定大小，無 external fragmentation  
- Segmentation：依邏輯區塊分割，但可能有 external fragmentation  

---

Q: 為什麼 Paging 還會有 fragmentation？  
A:  
因為最後一個 page 可能沒用滿 → 產生 internal fragmentation

---

Q: TLB 是什麼？  
A:  
TLB（Translation Lookaside Buffer）是快取 page table 的硬體，加速位址轉換。

---

Q: Page Fault 發生時會怎樣？  
A:  
OS 會暫停當前 process，從 disk 載入資料到 RAM，更新 page table，再繼續執行。

---

Q: Thrashing 是什麼？  
A:  
系統不斷進行 page swap，導致效能嚴重下降。

---

### 🔥 延伸追問

- 為什麼需要 Virtual Memory？
- Page size 太大或太小會怎樣？
- TLB miss 成本？
- Page replacement 怎麼選？
- Working set 是什麼？

👉 進階（加分🔥）
- Multi-level page table
- Inverted page table
- Copy-on-write（COW）
- Demand paging

## 6. 🧩 File System（檔案系統）

### 📌 定義
File System 是作業系統用來**管理與儲存資料在儲存裝置（如硬碟）上的機制**，
讓使用者可以透過「檔案」的形式存取資料。

👉 核心概念：
- 將資料抽象成 file
- 提供組織與存取方式（directory）

---

### ⚙️ 運作流程

（以讀取檔案為例）

1. 使用者呼叫 read()（system call）
2. OS 解析檔案路徑（directory traversal）
3. 找到對應的 **inode / metadata**
4. 根據 inode 找到資料所在的 block
5. 從 disk 讀取資料到 buffer（或 cache）
6. 回傳資料給使用者程式

👉 補充：
- 常搭配 **buffer cache / page cache** 提升效能

---

### ⚠️ 問題

#### 1️⃣ Disk I/O 慢
- 存取磁碟速度遠慢於 RAM

---

#### 2️⃣ Fragmentation（外部碎片）
- 檔案分散在不同 block，讀取變慢

---

#### 3️⃣ Consistency（資料一致性）
- 系統 crash 可能導致資料損壞

---

#### 4️⃣ Metadata Overhead
- inode / directory 需要額外空間

---

#### 5️⃣ Concurrency 問題
- 多個 process 同時存取同一檔案

---

### 💬 面試 QA

Q: File system 在做什麼？  
A:  
負責管理儲存裝置上的資料，提供檔案的建立、讀寫、刪除與組織方式。

---

Q: 什麼是 inode？  
A:  
inode 是用來儲存檔案 metadata 的結構，例如：
- 檔案大小
- 權限
- 資料 block 位置  

👉 但不包含檔名（檔名在 directory）

---

Q: File allocation methods 有哪些？  
A:  
- Contiguous（連續配置）  
- Linked（鏈結）  
- Indexed（索引，例如 inode）  

---

Q: 為什麼需要 directory？  
A:  
用來組織與管理檔案，讓使用者能快速查找與分類資料。

---

Q: 為什麼 file system 需要 cache？  
A:  
因為 disk I/O 很慢，透過 cache 可以減少實際磁碟存取次數。

---

### 🔥 延伸追問

- inode 跟 file name 關係？
- hard link vs soft link 差別？
- file delete 發生什麼事？
- journaling file system 是什麼？
- disk scheduling（有些會延伸）

👉 進階（加分🔥）
- ext4 / NTFS 差異（不用太深）
- page cache vs buffer cache
- RAID（有些公司會問）

## 7. 🧩 I/O & Device（輸入輸出與裝置管理）

### 📌 定義
I/O & Device 是作業系統負責管理各種硬體裝置（如鍵盤、磁碟、網卡），
並提供統一介面讓程式能存取這些裝置。

👉 核心概念：
- Device abstraction（裝置抽象化）
- Driver（裝置驅動程式）

---

### ⚙️ 運作流程

（以讀取裝置資料為例）

1. 使用者程式發出 I/O 請求（system call）
2. OS 呼叫對應的 **Device Driver**
3. Driver 與硬體裝置溝通
4. 裝置完成操作後發出 **Interrupt**
5. CPU 進入 ISR（Interrupt Service Routine）
6. OS 處理資料並回傳給程式

---

👉 另一種方式（Polling）：

1. CPU 持續檢查裝置狀態
2. 若完成 → 讀取資料

---

### ⚠️ 問題

#### 1️⃣ Busy Waiting（浪費 CPU）
- Polling 會讓 CPU 一直空轉

---

#### 2️⃣ Interrupt Overhead
- 太多 interrupt 會影響效能

---

#### 3️⃣ Latency（延遲）
- I/O 通常比 CPU 慢很多

---

#### 4️⃣ Synchronization 問題
- 多個 process 同時使用裝置

---

#### 5️⃣ Driver 複雜度
- 不同硬體需要不同 driver

---

### 💬 面試 QA

Q: Polling vs Interrupt 差別？  
A:  
- Polling：CPU 主動一直檢查裝置（浪費 CPU）  
- Interrupt：裝置完成後通知 CPU（較有效率）

---

Q: Interrupt 發生時 CPU 做什麼？  
A:  
1. 保存目前執行狀態（context）  
2. 跳到 ISR（Interrupt Service Routine）  
3. 處理完後恢復原本程式  

---

Q: 為什麼 ISR 要短？  
A:  
因為 ISR 會阻塞其他 interrupt，若執行太久會影響系統即時性與效能。

---

Q: DMA 是什麼？  
A:  
DMA（Direct Memory Access）允許裝置直接與記憶體傳輸資料，
不需要 CPU 逐筆處理。

---

Q: DMA 的優點？  
A:  
- 減少 CPU 負擔  
- 提高資料傳輸效率  

---

Q: 什麼情況用 Polling？  
A:  
- 簡單系統  
- 即時性要求高（不想等 interrupt）

---

### 🔥 延伸追問

- Interrupt vs Exception 差別？
- DMA 跟 CPU 傳輸差在哪？
- ISR 可以做什麼？不能做什麼？
- Interrupt 太多怎麼辦？
- Blocking I/O vs Non-blocking I/O？

👉 進階（加分🔥）
- Interrupt nesting（中斷巢狀）
- Top half / Bottom half（Linux）
- Memory-mapped I/O vs Port-mapped I/O
- Zero-copy（高階會問）

## 8. 🧩 Multiprocessor（SMP / AMP / CPU Hotplug）

### 📌 定義
Multiprocessor 是指系統中有多個 CPU（或多核心），
可同時執行多個 Process / Thread，以提升效能與資源利用率。

---

### ⚙️ 運作流程

（以 SMP 為例）

1. 系統啟動（Boot CPU）
2. 初始化其他 CPU（APs, Application Processors）
3. 所有 CPU 進入 OS scheduler
4. OS 將 Process / Thread 分配到不同 CPU
5. CPU 同時執行任務
6. 透過 synchronization（lock）確保資料一致性

---

👉 補充：
- 需要 **Load Balancing** 平衡各 CPU 工作量
- 需要 **CPU Affinity** 控制 process 綁定 CPU

---

### ⚠️ 問題

#### 1️⃣ Synchronization Complexity
- 多 CPU 同時存取共享資源 → race condition

---

#### 2️⃣ Cache Coherence（快取一致性）
- 不同 CPU cache 可能有不同資料副本

---

#### 3️⃣ Load Imbalance
- 某些 CPU 很忙，某些很閒

---

#### 4️⃣ Contention（資源競爭）
- 多 CPU 搶同一資源（lock、memory）

---

#### 5️⃣ NUMA 問題（進階🔥）
- 記憶體存取有遠近差異

---

### 💬 面試 QA

Q: SMP 是什麼？  
A:  
SMP（Symmetric Multiprocessing）是多個 CPU 共享同一記憶體，  
且地位平等，由同一個 OS 管理。

---

Q: SMP 的優點？  
A:  
- 提高效能（parallelism）  
- 資源共享  
- 彈性高  

---

Q: SMP 的問題？  
A:  
- synchronization 困難  
- cache coherence 問題  
- contention  

---

Q: AMP 是什麼？  
A:  
AMP（Asymmetric Multiprocessing）是由一個主 CPU 控制其他 CPU，  
不同 CPU 執行不同任務。

---

Q: SMP vs AMP 差別？  
A:  
- SMP：CPU 平等，共享資源  
- AMP：主從架構，任務分工  

---

Q: CPU Hot Plug 是什麼？  
A:  
在系統運行中動態加入或移除 CPU，不需要關機。

---

Q: CPU Hot Plug 會影響什麼？  
A:  
- Scheduler（重新分配工作）  
- Load balancing  
- CPU affinity  
- Interrupt routing  

---

### 🔥 延伸追問

- 為什麼需要 load balancing？
- CPU affinity 有什麼用？
- cache coherence 是什麼？
- SMP 系統如何避免 race condition？
- NUMA 是什麼？

👉 進階（加分🔥）
- MESI protocol（cache coherence）
- false sharing
- per-CPU data（Linux 常見）

## 9. 🧩 Deadlock（死鎖）

### 📌 定義
Deadlock 是指多個 Process / Thread 彼此等待對方釋放資源，
導致所有人都無法繼續執行的狀態。

👉 白話：
大家互相卡住，誰都動不了。

---

### ⚙️ 運作流程

（簡單例子）

1. Process A 持有資源 R1
2. Process B 持有資源 R2
3. A 想要 R2 → 等待
4. B 想要 R1 → 等待
5. 形成循環等待 → Deadlock

---

### ⚠️ 問題

#### 1️⃣ 系統停止進展
- 所有相關 process 卡住

---

#### 2️⃣ 資源浪費
- CPU / memory 無法有效利用

---

#### 3️⃣ 難以偵測
- 系統不一定能立即發現

---

### 💬 面試 QA

Q: Deadlock 是什麼？  
A:  
多個 process 互相等待資源，形成無法繼續執行的狀態。

---

Q: Deadlock 四大條件？（必背🔥）  
A:  
1. Mutual Exclusion（互斥）  
2. Hold and Wait（持有且等待）  
3. No Preemption（不可搶奪）  
4. Circular Wait（循環等待）  

👉 缺一不可

---

Q: 如何避免 Deadlock？  
A:  

👉 1️⃣ Prevention（預防）  
- 破壞四大條件之一  

👉 2️⃣ Avoidance（避免）  
- 使用 **Banker’s Algorithm** 判斷是否安全  

👉 3️⃣ Detection & Recovery（偵測與恢復）  
- 偵測 deadlock → 強制釋放資源或終止 process  

---

Q: Banker’s Algorithm 是什麼？  
A:  
一種避免 deadlock 的方法，透過檢查系統是否處於「安全狀態」來決定是否分配資源。

---

Q: Deadlock vs Starvation 差別？  
A:  
- Deadlock：互相卡住，完全無法進展  
- Starvation：某些 process 長期拿不到資源  

---

### 🔥 延伸追問

- 如何破壞 circular wait？
- Deadlock detection 怎麼做？
- Recovery 有哪些方法？
- Banker’s Algorithm 的缺點？
- Resource allocation graph 是什麼？

## 10. 🧩 IPC（Inter-Process Communication）

### 📌 定義
IPC 是讓不同 Process 之間可以**交換資料與進行溝通**的機制。

👉 為什麼需要？
因為 Process 彼此是獨立的（不同記憶體空間），無法直接存取彼此資料。

---

### ⚙️ 運作流程

（以 Pipe 為例）

1. 建立 pipe（OS 建立一個 buffer）
2. Process A 將資料寫入 pipe
3. OS 將資料存入 buffer
4. Process B 從 pipe 讀取資料
5. 完成資料傳遞

---

👉 不同 IPC 的本質差異：
- 是否共享記憶體
- 是否需要 kernel 介入
- 傳輸效率

---

### ⚠️ 問題

#### 1️⃣ Synchronization 問題
- 多個 process 同時存取資料

---

#### 2️⃣ 資料一致性
- shared memory 需要自己控制

---

#### 3️⃣ Kernel Overhead
- message passing 需要 system call

---

#### 4️⃣ Blocking 問題
- read / write 可能被阻塞

---

#### 5️⃣ 複雜度
- shared memory 難寫但快

---

### 💬 面試 QA

Q: IPC 是什麼？  
A:  
IPC 是讓不同 process 之間能夠交換資料的機制，例如 pipe、shared memory 等。

---

Q: 常見 IPC 有哪些？  
A:  
- Pipe  
- Message Queue  
- Shared Memory  
- Socket  

---

Q: 哪種 IPC 最快？  
A:  
👉 Shared Memory  
因為不需要經過 kernel copy，直接共享記憶體  

👉 但需要自己處理 synchronization

---

Q: Pipe 的特性？  
A:  
- 單向（half-duplex）  
- 通常用於 parent-child process  

---

Q: Message Queue 跟 Pipe 差別？  
A:  
- Message Queue：有結構（message-based）  
- Pipe：資料流（byte stream）  

---

Q: Socket 用在什麼情況？  
A:  
用於不同機器（network）或跨 process 溝通

---

### 🔥 延伸追問

- Shared Memory 要怎麼避免 race condition？
- Blocking vs Non-blocking I/O？
- Pipe 可以雙向嗎？
- IPC 會不會造成 deadlock？
- 為什麼 message passing 比 shared memory 慢？

👉 進階（加分🔥）
- mmap（memory-mapped file）
- Zero-copy
- UNIX domain socket

👉 進階（加分🔥）
- Wait-for graph
- 實務系統通常怎麼處理 deadlock（很多是忽略或 timeout）

## 11. 🧩 System Call（系統呼叫）

### 📌 定義
System Call 是使用者程式（User Space）向作業系統（Kernel）請求服務的介面。

👉 本質：
提供一個「安全的方式」讓程式存取硬體與系統資源

---

### ⚙️ 運作流程

1. User 程式呼叫 system call（如 read(), write()）
2. 透過特殊指令（trap / syscall）
3. CPU 切換到 **Kernel Mode**
4. OS 根據 system call number 找到對應服務
5. Kernel 執行操作（例如存取檔案、記憶體）
6. 將結果回傳
7. 切回 **User Mode**，繼續執行程式

👉 核心切換：
User Mode → Kernel Mode → User Mode

---

### ⚠️ 問題

#### 1️⃣ Mode Switch Overhead
- User ↔ Kernel 切換需要成本

---

#### 2️⃣ Context Switch 成本
- 某些 system call 會導致 process 切換

---

#### 3️⃣ 安全性限制
- 使用者不能直接操作硬體，必須透過 system call

---

#### 4️⃣ 頻繁呼叫影響效能
- I/O 密集程式會大量使用 system call

---

### 💬 面試 QA

Q: System call 是什麼？  
A:  
System call 是 user program 向 OS 請求服務的介面，例如檔案讀寫或記憶體操作。

---

Q: 為什麼需要 system call？  
A:  
因為 user program 不能直接操作硬體，必須透過 OS 才能安全存取資源。

---

Q: System call 發生時 CPU 做了什麼？  
A:  
CPU 會從 user mode 切換到 kernel mode，執行 OS 的程式碼，完成後再切回 user mode。

---

Q: System call 跟 function call 差別？  
A:  
- function call：在 user space 內部呼叫  
- system call：需要切換到 kernel space  

👉 system call 成本較高

---

Q: 常見 system call 類型？  
A:  
- Process control（fork, exec）  
- File management（read, write）  
- Device management  
- Communication（IPC）  

---

### 🔥 延伸追問

- system call 一定會 context switch 嗎？
- trap 跟 interrupt 差別？
- 為什麼 system call 比 function call 慢？
- 如何減少 system call overhead？
- blocking vs non-blocking system call？

👉 進階（加分🔥）
- syscall number / dispatch table
- vDSO（減少 kernel 切換）
- user-level library（glibc）
