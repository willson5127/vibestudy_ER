# 訂單系統 ER 圖

沿用測試圖的「表名、型別、欄位、鍵、中文說明」格式，展示 8 張資料表。

```mermaid
erDiagram
    CUSTOMER ||..o{ ADDRESS : "擁有"
    CUSTOMER ||..o{ SALES_ORDER : "下單"
    ADDRESS o|..o{ SALES_ORDER : "配送至"
    SALES_ORDER ||--|{ ORDER_ITEM : "包含"
    PRODUCT ||..o{ ORDER_ITEM : "被訂購"
    CATEGORY o|..o{ PRODUCT : "分類"
    SALES_ORDER ||..o{ PAYMENT : "付款紀錄"
    SALES_ORDER ||--o{ SHIPMENT : "出貨紀錄"

    CUSTOMER {
        int id PK "客戶序號"
        string email UK "電子郵件"
        string name "姓名"
        datetime created_at "建立時間"
    }
    ADDRESS {
        int id PK "地址序號"
        int customer_id FK "所屬客戶"
        string recipient "收件人"
        string postal_code "郵遞區號"
        string address "完整地址"
    }
    SALES_ORDER {
        int id PK "訂單序號"
        string order_no UK "訂單編號"
        int customer_id FK "下單客戶"
        int shipping_address_id FK "配送地址，可空"
        string status "訂單狀態"
        datetime ordered_at "下單時間"
    }
    ORDER_ITEM {
        int order_id PK, FK "訂單序號"
        int line_no PK "項次"
        int product_id FK "商品序號"
        int quantity "購買數量"
        decimal unit_price "成交單價"
    }
    PRODUCT {
        int id PK "商品序號"
        string sku UK "商品代碼"
        int category_id FK "商品分類，可空"
        string name "商品名稱"
        decimal price "目前售價"
    }
    CATEGORY {
        int id PK "分類序號"
        string name UK "分類名稱"
    }
    PAYMENT {
        int id PK "付款序號"
        int order_id FK "所屬訂單"
        decimal amount "付款金額"
        string status "付款狀態"
        datetime paid_at "付款時間，可空"
    }
    SHIPMENT {
        int id PK "出貨序號"
        int order_id FK "所屬訂單"
        string tracking_no UK "物流單號"
        string carrier "物流業者"
        datetime shipped_at "出貨時間"
    }
```

## 閱讀方式

- `PK`：主鍵；`FK`：外鍵；`UK`：唯一鍵。
- `||`：恰好一筆；`o|`：零或一筆；`o{`：零到多筆；`|{`：一到多筆。
- 實線表示識別關聯，虛線表示非識別關聯；是否識別取決於父表鍵是否構成子表主鍵。
- 訂單明細以 `order_id + line_no` 作為複合主鍵，同一訂單可在不同項次包含相同商品。
- 訂單至少包含一筆明細；可有多次付款及分批出貨。
- 配送地址與商品分類可空，因此訂單可沒有配送地址，商品可沒有分類。
- 明細保存成交單價，商品售價變動不會改變歷史訂單。

## 修改與限制

每行一個欄位，每行一個關聯。欄位依原始碼順序呈現；圖形位置由 Mermaid 自動配置。型別為示範用通用名稱。

這是資料模型圖，不會建立資料庫或執行約束。訂單至少一筆明細、配送地址須屬於下單客戶等規則，需要資料庫或應用程式另外實作。圖中出貨僅記錄訂單層級，未細分各批出貨的商品數量。

獨立來源檔為 `order-system.mmd`；修改本檔或來源檔時，另一份需同步更新。

