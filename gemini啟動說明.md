# Gemini 啟動說明

本文檔介紹了啟動 Gemini 應用程式的幾種不同方法。

## 目錄
1. [從命令行直接啟動](#從命令行直接啟動)
2. [使用腳本啟動](#使用腳本啟動)
3. [在開發模式下啟動](#在開發模式下啟動)
4. [作為背景服務啟動](#作為背景服務啟動)

---

### 從命令行直接啟動

這是最基本的啟動方式。打開您的終端機，然後執行以下命令：

```bash
# 替換為實際的啟動命令
gemini --config /path/to/your/config.yaml
```

**優點:**
- 簡單直接
- 適合快速測試和調試

**注意事項:**
- 終端機關閉後，應用程式也會跟著終止。

---

### 使用腳本啟動

您可以建立一個啟動腳本來簡化啟動過程，特別是當啟動命令比較複雜時。

1.  建立一個名為 `start_gemini.sh` 的檔案：

    ```bash
    #!/bin/bash
    
    # 設置環境變數 (如果需要)
    export GEMINI_ENV=production
    
    # 執行啟動命令
    echo "正在啟動 Gemini..."
    gemini --port 8080 --verbose
    echo "Gemini 已停止。"
    ```

2.  給予腳本執行權限：

    ```bash
    chmod +x start_gemini.sh
    ```

3.  執行腳本：

    ```bash
    ./start_gemini.sh
    ```

---

### 在開發模式下啟動

如果您是開發人員，可能需要以開發模式啟動，這通常會啟用熱重載 (hot-reloading) 或更詳細的日誌。

```bash
# 範例：使用 npm/yarn (如果是 Node.js 專案)
npm run dev

# 範例：直接使用參數
gemini --dev --watch
```

請參考專案的 `package.json` 或開發文檔以獲取確切的命令。

---

### 作為背景服務啟動

在生產環境中，您會希望 Gemini 作為一個背景服務持續運行。

#### 方法 1: 使用 `nohup`

```bash
nohup gemini > gemini.log 2>&1 &
```
- `nohup`: 讓程式在您登出後繼續運行。
- `>`: 將標準輸出重定向到 `gemini.log`。
- `2>&1`: 將標準錯誤也重定向到 `gemini.log`。
- `&`: 讓命令在背景執行。

若要停止服務，您需要找到其 PID 並手動 `kill`：
```bash
ps aux | grep gemini
kill <PID>
```

#### 方法 2: 使用 `systemd` (建議)

對於 Linux 系統，`systemd` 是管理服務的標準方式。

1.  建立一個服務設定檔 `/etc/systemd/system/gemini.service`:

    ```ini
    [Unit]
    Description=Gemini Service
    After=network.target
    
    [Service]
    User=your_user          # 替換為您用來運行的使用者
    WorkingDirectory=/path/to/gemini_project # 替換為您的專案路徑
    ExecStart=/usr/local/bin/gemini --config /etc/gemini/config.yaml # 替換為您的啟動命令
    Restart=always
    
    [Install]
    WantedBy=multi-user.target
    ```

2.  重新載入 `systemd` 並啟動服務：

    ```bash
    sudo systemctl daemon-reload
    sudo systemctl start gemini
    ```

3.  檢查服務狀態：

    ```bash
    sudo systemctl status gemini
    ```

4.  讓服務開機自啟：
    ```bash
    sudo systemctl enable gemini
    ```
