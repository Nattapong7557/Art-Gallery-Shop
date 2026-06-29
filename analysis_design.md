# 📋 เอกสารวิเคราะห์และออกแบบระบบ (Analysis & Design Document)
## ระบบร้านขายรูปภาพศิลปะออนไลน์ — Art Gallery Platform
**จัดทำโดย:** นัธทวัฒน์ ปิ่นปั่น, กษิดิ์เดช เนียมทอง, ณัฐพงศ์ จุลละลุจิ, ธนชล แจงเจริญ, นันธวัช สงนุ้ย

---

## 1. บทนำ (Introduction)

### 1.1 วัตถุประสงค์ของระบบ (System Objectives)
ระบบร้านขายรูปภาพศิลปะออนไลน์ (**Art Gallery Platform**) พัฒนาขึ้นเพื่อเป็นแพลตฟอร์มที่เชื่อมต่อระหว่าง **ศิลปิน (Artists)** ผู้สร้างสรรค์ผลงาน และ **ลูกค้า (Customers)** ผู้สนใจสั่งซื้อผลงานศิลปะทั้งในรูปแบบไฟล์ดิจิทัลและงานพิมพ์คุณภาพสูง (Print on Demand)

### 1.2 ขอบเขตของระบบ (System Scope)
- รองรับการลงทะเบียนและเข้าสู่ระบบ (JWT / OAuth2)
- แสดงและค้นหาผลงานศิลปะตามหมวดหมู่ สไตล์ และศิลปิน
- สั่งซื้อและชำระเงินผ่านระบบออนไลน์ (Stripe / PromptPay)
- ศิลปินอัปโหลดผลงานพร้อมระบบ Watermark อัตโนมัติ
- ระบบจัดการคำสั่งซื้อ การพิมพ์ และจัดส่ง

### 1.3 ผู้ใช้งานระบบ (System Users / Stakeholders)
| ผู้ใช้ | บทบาท | ความต้องการหลัก |
|---|---|---|
| **ลูกค้า (Customer)** | ผู้ซื้อผลงานศิลปะ | ค้นหา เลือกดู สั่งซื้อ และชำระเงิน |
| **ศิลปิน (Artist)** | ผู้สร้างสรรค์ผลงาน | อัปโหลดผลงาน ตั้งราคา จัดการคำสั่งซื้อ |
| **ผู้ดูแลระบบ (Admin)** | ผู้ดูแลแพลตฟอร์ม | จัดการผู้ใช้ ตรวจสอบเนื้อหา ดูรายงาน |

---

## 2. การวิเคราะห์ความต้องการ (Requirements Analysis)

### 2.1 ความต้องการเชิงฟังก์ชัน (Functional Requirements)

#### FR-01: ระบบสมาชิกและการยืนยันตัวตน (Authentication & Authorization)
- ผู้ใช้สามารถสมัครสมาชิกด้วยอีเมลและรหัสผ่าน
- รองรับการเข้าสู่ระบบผ่าน OAuth2 (Google Account)
- ใช้ JWT (JSON Web Token) ในการจัดการ Session
- แบ่งระดับสิทธิ์การเข้าถึง: Customer, Artist, Admin

#### FR-02: ระบบแสดงผลงานศิลปะ (Art Gallery & Browsing)
- แสดงภาพตัวอย่างแบบ Watermark เพื่อปกป้องลิขสิทธิ์
- รองรับการค้นหาด้วยคำสำคัญ หมวดหมู่ แท็ก สีหลัก และช่วงราคา
- ซูมภาพเพื่อดูรายละเอียดของผลงาน
- แสดงข้อมูลศิลปินและผลงานที่เกี่ยวข้อง

#### FR-03: ระบบตะกร้าสินค้าและการสั่งซื้อ (Shopping Cart & Orders)
- เพิ่ม/ลบรายการในตะกร้าสินค้า
- เลือกขนาดพิมพ์ (A4, A3, A2, A1) และประเภทกรอบ (ไม้, อลูมิเนียม)
- คำนวณราคาค่าพิมพ์ กรอบ และค่าจัดส่งอัตโนมัติ
- บันทึกประวัติการสั่งซื้อและสถานะจัดส่ง

#### FR-04: ระบบชำระเงิน (Payment Gateway)
- รองรับการชำระเงินผ่าน Stripe (บัตรเครดิต/เดบิต)
- รองรับการชำระเงินผ่าน PromptPay (QR Code)
- แจ้งเตือนผลการชำระเงินผ่านอีเมลอัตโนมัติ

#### FR-05: ระบบจัดการสำหรับศิลปิน (Artist Dashboard)
- อัปโหลดไฟล์ภาพความละเอียดสูง (High-resolution)
- ระบบสร้าง Watermark อัตโนมัติก่อนเผยแพร่ตัวอย่าง
- ตั้งราคาขายไฟล์ดิจิทัลและราคาเริ่มต้นงานพิมพ์
- แดชบอร์ดแสดงสถิติยอดขายและจำนวนการเข้าชม

#### FR-06: ระบบรายการโปรด (Wishlist)
- บันทึกผลงานที่สนใจลงรายการโปรดส่วนตัว
- แจ้งเตือนเมื่อมีโปรโมชันหรือราคาพิเศษ

---

### 2.2 ความต้องการที่ไม่ใช่เชิงฟังก์ชัน (Non-Functional Requirements)

| รหัส | หมวด | รายละเอียด |
|---|---|---|
| **NFR-01** | ความปลอดภัย (Security) | ใช้ HTTPS, JWT Token, Rate Limiting, Pre-signed URLs สำหรับไฟล์ S3 |
| **NFR-02** | ประสิทธิภาพ (Performance) | โหลดหน้าแรกภายใน 2 วินาที, ใช้ Redis Cache ลด Database Load |
| **NFR-03** | ความพร้อมใช้งาน (Availability) | Uptime ≥ 99.5%, ใช้ Docker + AWS สำหรับ Deployment |
| **NFR-04** | ความสามารถในการขยาย (Scalability) | รองรับผู้ใช้พร้อมกัน ≥ 1,000 คน |
| **NFR-05** | การตอบสนอง (Responsiveness) | รองรับการแสดงผลบน Desktop, Tablet, Mobile (Mobile-first) |
| **NFR-06** | ความเป็นส่วนตัว (Privacy) | เก็บรหัสผ่านแบบ Hash (bcrypt), ปฏิบัติตาม PDPA |

---

## 3. Use Case Diagram

```mermaid
graph LR
    Customer(("👤 Customer"))
    Artist(("🎨 Artist"))
    Admin(("🔧 Admin"))

    UC1["ค้นหาและเลือกดูผลงาน"]
    UC2["สมัครสมาชิก / เข้าสู่ระบบ"]
    UC3["เพิ่มสินค้าลงตะกร้า"]
    UC4["ชำระเงินสั่งซื้อ"]
    UC5["บันทึก Wishlist"]
    UC6["ดูประวัติคำสั่งซื้อ"]
    UC7["อัปโหลดผลงาน"]
    UC8["จัดการ Portfolio"]
    UC9["ดูรายงานยอดขาย"]
    UC10["จัดการผู้ใช้งาน"]
    UC11["ตรวจสอบเนื้อหา"]

    Customer --> UC1
    Customer --> UC2
    Customer --> UC3
    Customer --> UC4
    Customer --> UC5
    Customer --> UC6

    Artist --> UC2
    Artist --> UC7
    Artist --> UC8
    Artist --> UC9

    Admin --> UC10
    Admin --> UC11
```

---

## 4. User Flow Diagram

```mermaid
flowchart TD
    Start(("เริ่มต้น")) --> Browse["เข้าหน้าแรก / เลือกดูผลงาน"]
    Browse --> Search{"ค้นหาผลงาน?"}
    Search -->|ใช่| Filter["กรองตามหมวดหมู่ / ราคา / สไตล์"]
    Search -->|ไม่| Gallery["เลือกดูจากหน้า Gallery"]
    Filter --> Detail["ดูรายละเอียดผลงาน"]
    Gallery --> Detail

    Detail --> Action{"ต้องการสั่งซื้อ?"}
    Action -->|ใช่| LoginCheck{"เข้าสู่ระบบแล้ว?"}
    Action -->|ไม่| Wishlist["บันทึกลง Wishlist"]
    Wishlist --> Browse

    LoginCheck -->|ยังไม่| Login["สมัครสมาชิก / Login"]
    LoginCheck -->|แล้ว| Cart["เพิ่มลงตะกร้า"]
    Login --> Cart

    Cart --> SelectPrint["เลือกขนาดพิมพ์ / กรอบ"]
    SelectPrint --> Checkout["ดำเนินการชำระเงิน"]
    Checkout --> Payment{"เลือกวิธีชำระ"}
    Payment -->|บัตรเครดิต| Stripe["Stripe Gateway"]
    Payment -->|PromptPay| QR["สแกน QR Code"]
    Stripe --> Confirm["ยืนยันคำสั่งซื้อ"]
    QR --> Confirm

    Confirm --> Email["ส่งอีเมลยืนยัน"]
    Email --> Track["ติดตามสถานะคำสั่งซื้อ"]
    Track --> End(("สิ้นสุด"))
```

---

## 5. Entity Relationship Diagram (ERD)

```mermaid
erDiagram
    USERS ||--o{ ORDERS : places
    USERS ||--o{ WISHLISTS : has
    ARTISTS ||--o{ ARTWORKS : creates
    ARTWORKS ||--o{ ORDER_ITEMS : included_in
    ARTWORKS ||--o{ WISHLISTS : saved_in
    ARTWORKS }o--|| CATEGORIES : belongs_to
    ORDERS ||--|{ ORDER_ITEMS : contains
    ORDERS ||--|| PAYMENTS : has

    USERS {
        int id PK
        string name
        string email UK
        string password_hash
        enum role
        datetime created_at
    }

    ARTISTS {
        int id PK
        int user_id FK
        string bio
        string portfolio_url
        string bank_account
    }

    CATEGORIES {
        int id PK
        string name
        string description
    }

    ARTWORKS {
        int id PK
        int artist_id FK
        int category_id FK
        string title
        text description
        string high_res_url
        string preview_url
        decimal price_digital
        decimal price_print_base
        string tags
        datetime created_at
    }

    ORDERS {
        int id PK
        int user_id FK
        datetime order_date
        enum status
        decimal total_amount
        string shipping_address
        string tracking_number
    }

    ORDER_ITEMS {
        int id PK
        int order_id FK
        int artwork_id FK
        enum type
        string print_size
        string frame_option
        int quantity
        decimal unit_price
    }

    PAYMENTS {
        int id PK
        int order_id FK
        enum payment_method
        string transaction_id
        decimal amount
        enum status
        datetime payment_date
    }

    WISHLISTS {
        int id PK
        int user_id FK
        int artwork_id FK
        datetime added_at
    }
```

---

## 6. Sequence Diagram: กระบวนการสั่งซื้อผลงาน

```mermaid
sequenceDiagram
    actor Customer
    participant Frontend
    participant API as API Gateway
    participant OrderSvc as Order Service
    participant PaymentSvc as Payment Service
    participant DB as Database
    participant S3 as AWS S3

    Customer->>Frontend: เลือกผลงาน + ขนาดพิมพ์ + กรอบ
    Frontend->>API: POST /api/v1/cart/add
    API->>DB: บันทึกรายการตะกร้า
    DB-->>API: สำเร็จ
    API-->>Frontend: แสดงตะกร้าสินค้า

    Customer->>Frontend: กดชำระเงิน
    Frontend->>API: POST /api/v1/payments/checkout
    API->>OrderSvc: สร้างคำสั่งซื้อ
    OrderSvc->>DB: INSERT orders + order_items
    DB-->>OrderSvc: order_id

    OrderSvc->>PaymentSvc: เริ่มกระบวนการชำระเงิน
    PaymentSvc->>PaymentSvc: สร้าง Payment URL / QR Code
    PaymentSvc-->>Frontend: payment_url + qr_code

    Customer->>PaymentSvc: ชำระเงิน (Stripe / PromptPay)
    PaymentSvc->>DB: UPDATE payment status
    PaymentSvc-->>Frontend: ยืนยันการชำระเงินสำเร็จ

    Frontend->>API: GET /api/v1/orders/:id
    API->>DB: Query order details
    DB-->>API: order data
    API-->>Frontend: แสดงรายละเอียดคำสั่งซื้อ

    Note over Customer,S3: กรณีซื้อไฟล์ดิจิทัล
    API->>S3: Generate Pre-signed URL
    S3-->>API: download_url (หมดอายุ 24 ชม.)
    API-->>Frontend: ลิงก์ดาวน์โหลดไฟล์ต้นฉบับ
```

---

## 7. System Architecture Diagram

ดูรายละเอียดไดอะแกรมสถาปัตยกรรมระบบฉบับเต็มได้ที่ไฟล์ [architecture.mmd](architecture.mmd)

```mermaid
graph TD
    classDef subBlock fill:#ffffff,stroke:#666,stroke-width:1px,color:#000
    classDef storage fill:#1b4332,stroke:#fff,stroke-dasharray: 5 5,color:#fff

    subgraph Frontend_Layer ["Frontend Layer — ร้านขายรูปศิลปะ"]
        direction LR
        F1["Art gallery / Browse artwork"]:::subBlock
        F2["Artist profile / Portfolio"]:::subBlock
        F3["Artwork detail / Zoom / Price"]:::subBlock
        F4["Cart / Checkout / Print size"]:::subBlock
        F5["Artist panel / Upload / Manage"]:::subBlock
        Auth["Login JWT / OAuth2"]:::subBlock
    end
    style Frontend_Layer fill:#3b3395,stroke:#fff,stroke-width:2px,color:#fff

    subgraph Backend_Layer ["Backend Layer — Business Logic"]
        direction TB
        B1["API Gateway / Auth / Rate limit"]:::subBlock
        B2["Artwork service / Upload"]:::subBlock
        B3["Order service / Shipping"]:::subBlock
        B4["Payment service / Stripe"]:::subBlock
        S3["AWS S3 Cloud Storage"]:::storage
    end
    style Backend_Layer fill:#0b5345,stroke:#fff,stroke-width:2px,color:#fff

    subgraph Database_Layer ["Database Layer — Data Storage"]
        direction LR
        subgraph MySQL_DB ["Relational DB — MySQL"]
            DB1["Users / Artists / Artworks / Orders / Payments"]:::subBlock
        end
        style MySQL_DB fill:#7e5109,stroke:#fff,color:#fff
        subgraph NoSQL_Redis ["NoSQL + Redis"]
            DB2["MongoDB Reviews Tags / Redis Session Cache"]:::subBlock
        end
        style NoSQL_Redis fill:#7e5109,stroke:#fff,color:#fff
    end
    style Database_Layer fill:#784212,stroke:#fff,stroke-width:2px,color:#fff

    Frontend_Layer -->|REST API| B1
    B1 --> B2
    B1 --> B3
    B1 --> B4
    B2 <--> S3
    B2 -->|Query| Database_Layer
    B3 -->|Query| Database_Layer
    B4 -->|Query| Database_Layer
```

---

## 8. Database Schema Detail

### 8.1 ตาราง `users`
| Field | Type | Constraint | Description |
|---|---|---|---|
| `id` | INT | PK, AUTO_INCREMENT | รหัสผู้ใช้ |
| `name` | VARCHAR(100) | NOT NULL | ชื่อ-นามสกุล |
| `email` | VARCHAR(100) | UNIQUE, NOT NULL | อีเมล (สำหรับ Login) |
| `password_hash` | VARCHAR(255) | NOT NULL | รหัสผ่านที่แฮชด้วย bcrypt |
| `role` | ENUM('customer','artist','admin') | DEFAULT 'customer' | บทบาทผู้ใช้ |
| `created_at` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | วันที่สร้างบัญชี |

### 8.2 ตาราง `artists`
| Field | Type | Constraint | Description |
|---|---|---|---|
| `id` | INT | PK, AUTO_INCREMENT | รหัสศิลปิน |
| `user_id` | INT | FK → users.id | อ้างอิงผู้ใช้ |
| `bio` | TEXT | | ประวัติศิลปิน |
| `portfolio_url` | VARCHAR(300) | | ลิงก์ Portfolio |
| `bank_account` | VARCHAR(50) | | เลขบัญชีรับเงิน |

### 8.3 ตาราง `artworks`
| Field | Type | Constraint | Description |
|---|---|---|---|
| `id` | INT | PK, AUTO_INCREMENT | รหัสผลงาน |
| `artist_id` | INT | FK → artists.id | ศิลปินเจ้าของผลงาน |
| `category_id` | INT | FK → categories.id | หมวดหมู่ |
| `title` | VARCHAR(200) | NOT NULL | ชื่อผลงาน |
| `description` | TEXT | | คำอธิบายผลงาน |
| `high_res_url` | VARCHAR(500) | NOT NULL | URL ไฟล์ต้นฉบับ (S3 Private) |
| `preview_url` | VARCHAR(500) | NOT NULL | URL ตัวอย่าง Watermark (S3 Public) |
| `price_digital` | DECIMAL(10,2) | NOT NULL | ราคาไฟล์ดิจิทัล (บาท) |
| `price_print_base` | DECIMAL(10,2) | | ราคาเริ่มต้นงานพิมพ์ (บาท) |
| `tags` | VARCHAR(500) | | แท็กคั่นด้วย comma |
| `created_at` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | วันที่อัปโหลด |

### 8.4 ตาราง `orders`
| Field | Type | Constraint | Description |
|---|---|---|---|
| `id` | INT | PK, AUTO_INCREMENT | รหัสคำสั่งซื้อ |
| `user_id` | INT | FK → users.id | ผู้สั่งซื้อ |
| `order_date` | DATETIME | NOT NULL | วันที่สั่ง |
| `status` | ENUM('pending','paid','printing','shipped','completed','cancelled') | DEFAULT 'pending' | สถานะ |
| `total_amount` | DECIMAL(10,2) | NOT NULL | ยอดรวม |
| `shipping_address` | TEXT | | ที่อยู่จัดส่ง |
| `tracking_number` | VARCHAR(100) | | เลขพัสดุ |

### 8.5 ตาราง `order_items`
| Field | Type | Constraint | Description |
|---|---|---|---|
| `id` | INT | PK, AUTO_INCREMENT | รหัสรายการ |
| `order_id` | INT | FK → orders.id | คำสั่งซื้อ |
| `artwork_id` | INT | FK → artworks.id | ผลงาน |
| `type` | ENUM('digital','print') | NOT NULL | ประเภทสินค้า |
| `print_size` | VARCHAR(10) | | ขนาดพิมพ์ (A4, A3, A2, A1) |
| `frame_option` | VARCHAR(50) | | ตัวเลือกกรอบ |
| `quantity` | INT | DEFAULT 1 | จำนวน |
| `unit_price` | DECIMAL(10,2) | NOT NULL | ราคาต่อหน่วย |

### 8.6 ตาราง `payments`
| Field | Type | Constraint | Description |
|---|---|---|---|
| `id` | INT | PK, AUTO_INCREMENT | รหัสการชำระเงิน |
| `order_id` | INT | FK → orders.id | คำสั่งซื้อ |
| `payment_method` | ENUM('credit_card','promptpay') | NOT NULL | วิธีชำระเงิน |
| `transaction_id` | VARCHAR(200) | | รหัสธุรกรรม |
| `amount` | DECIMAL(10,2) | NOT NULL | จำนวนเงิน |
| `status` | ENUM('pending','success','failed','refunded') | DEFAULT 'pending' | สถานะ |
| `payment_date` | DATETIME | | วันที่ชำระ |

---

## 9. API Endpoints Summary

### 9.1 Authentication
| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/v1/auth/register` | สมัครสมาชิกใหม่ |
| POST | `/api/v1/auth/login` | เข้าสู่ระบบ (รับ JWT Token) |
| POST | `/api/v1/auth/google` | เข้าสู่ระบบผ่าน Google OAuth2 |
| POST | `/api/v1/auth/refresh` | ต่ออายุ Token |

### 9.2 Artworks
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/v1/artworks` | ดึงรายการผลงานทั้งหมด (พร้อม filter/search) |
| GET | `/api/v1/artworks/:id` | ดึงรายละเอียดผลงาน |
| POST | `/api/v1/artworks` | อัปโหลดผลงานใหม่ (Artist only) |
| PUT | `/api/v1/artworks/:id` | แก้ไขข้อมูลผลงาน (Artist only) |
| DELETE | `/api/v1/artworks/:id` | ลบผลงาน (Artist/Admin only) |

### 9.3 Orders & Payments
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/v1/orders` | ดูรายการคำสั่งซื้อของตัวเอง |
| GET | `/api/v1/orders/:id` | ดูรายละเอียดคำสั่งซื้อ |
| POST | `/api/v1/cart/add` | เพิ่มสินค้าลงตะกร้า |
| POST | `/api/v1/payments/checkout` | เริ่มชำระเงิน |
| POST | `/api/v1/payments/webhook` | รับการแจ้งเตือนจาก Payment Gateway |

### 9.4 Wishlist
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/v1/wishlist` | ดูรายการโปรด |
| POST | `/api/v1/wishlist` | เพิ่มผลงานลงรายการโปรด |
| DELETE | `/api/v1/wishlist/:artwork_id` | ลบออกจากรายการโปรด |

---

## 10. เทคโนโลยีที่ใช้ (Technology Stack)

| Layer | เทคโนโลยี | เหตุผล |
|---|---|---|
| **Frontend** | React / Next.js (SSR) | SEO ดี, โหลดเร็ว, Mobile-first |
| **Backend** | Node.js / Express.js | Non-blocking I/O, npm ecosystem ใหญ่ |
| **Database (SQL)** | MySQL 8 | ACID Transactions, ข้อมูลธุรกรรมมั่นคง |
| **Database (NoSQL)** | MongoDB | Schema ยืดหยุ่น สำหรับ reviews/tags |
| **Cache** | Redis | Session management, ลด DB load |
| **Storage** | AWS S3 | เก็บไฟล์ภาพ High-res + Watermark |
| **Payment** | Stripe + PromptPay API | รองรับบัตรเครดิตและ QR Payment |
| **Auth** | JWT + OAuth2 (Google) | ปลอดภัย, Stateless |
| **DevOps** | Docker + GitHub Actions | CI/CD Pipeline อัตโนมัติ |
| **Hosting** | AWS EC2 / Elastic Beanstalk | ขยายขนาดได้ง่าย |

---

## 11. Software Architecture (สถาปัตยกรรมซอฟต์แวร์)

สำหรับธุรกิจ SMEs ที่มีทั้งระบบขายสินค้าออนไลน์ ระบบสมาชิก การชำระเงิน และการเชื่อมต่อคลังสินค้า สามารถวิเคราะห์ **Software Architecture** ตามองค์ประกอบหลัก 3 ชั้น ได้ดังนี้:

```mermaid
graph TD
    classDef layerTitle fill:#1e293b,stroke:#475569,stroke-width:2px,color:#fff,font-weight:bold
    classDef frontend fill:#3b3395,stroke:#818cf8,stroke-width:1px,color:#fff
    classDef backend fill:#0b5345,stroke:#34d399,stroke-width:1px,color:#fff
    classDef database fill:#7e5109,stroke:#f59e0b,stroke-width:1px,color:#fff
    classDef external fill:#831843,stroke:#ec4899,stroke-width:1px,color:#fff

    User(("👤 Users / Customers"))
    Artist(("🎨 Artists"))
    Admin(("🔧 Admin"))

    subgraph FL ["1. Frontend Architecture"]
        FW["Web App - React / Next.js"]:::frontend
        FM["Mobile App - Flutter"]:::frontend
        FA["Admin Dashboard"]:::frontend
    end

    subgraph BL ["2. Backend Architecture"]
        AG["API Gateway"]:::backend
        AS["Auth Service"]:::backend
        PS["Product Service"]:::backend
        OS["Order Service"]:::backend
        PY["Payment Service"]:::backend
        NS["Notification Service"]:::backend
    end

    subgraph DL ["3. Database Architecture"]
        SQL["MySQL - Relational DB"]:::database
        NoSQL["MongoDB - NoSQL DB"]:::database
        Cache["Redis - Cache / Session"]:::database
        S3["AWS S3 - File Storage"]:::database
    end

    subgraph EXT ["External Services"]
        Stripe["Stripe Gateway"]:::external
        Google["Google OAuth2"]:::external
        CDN["CloudFront CDN"]:::external
    end

    User --> FW
    Artist --> FW
    Admin --> FA
    User --> FM

    FW --> AG
    FM --> AG
    FA --> AG

    AG --> AS
    AG --> PS
    AG --> OS
    AG --> PY

    AS --> SQL
    AS --> Cache
    PS --> SQL
    PS --> S3
    OS --> SQL
    PY --> SQL

    PS --> NoSQL
    NS --> NoSQL

    PY --> Stripe
    AS --> Google
    FW --> CDN
```

---

### 11.1 Frontend Architecture (ส่วนที่ผู้ใช้งานโต้ตอบกับระบบ)

#### หน้าที่หลักของ Frontend
| หน้าที่ | รายละเอียด |
|---|---|
| แสดงหน้าเว็บไซต์ / Mobile App | หน้า Landing Page, Gallery, รายละเอียดสินค้า |
| ระบบสมัครสมาชิก / Login | ฟอร์มสมัครสมาชิก, Login ด้วยอีเมลหรือ Google |
| แสดงสินค้า | Gallery ผลงานศิลปะ, ค้นหา, กรองตามหมวดหมู่/ราคา/สไตล์ |
| ตะกร้าสินค้า (Cart) | เพิ่ม/ลบรายการ, เลือกขนาดพิมพ์, เลือกกรอบ |
| ระบบชำระเงิน | Stripe (บัตรเครดิต), PromptPay (QR Code) |
| Dashboard ผู้ดูแลระบบ | จัดการผู้ใช้, ตรวจสอบเนื้อหา, ดูรายงานยอดขาย |

#### โครงสร้างสถาปัตยกรรม Frontend

**Component-Based Architecture / Single Page Application (SPA)**

```mermaid
graph TD
    classDef component fill:#312e81,stroke:#818cf8,stroke-width:1px,color:#fff
    classDef page fill:#1e3a5f,stroke:#38bdf8,stroke-width:1px,color:#fff
    classDef shared fill:#3f3f46,stroke:#a1a1aa,stroke-width:1px,color:#fff

    subgraph SPA ["Single Page Application - React / Next.js"]
        direction TB

        subgraph Pages ["Pages / Routes"]
            P1["/ Home Page"]:::page
            P2["/gallery - Gallery"]:::page
            P3["/artwork/:id - Detail"]:::page
            P4["/cart - Shopping Cart"]:::page
            P5["/checkout - Checkout"]:::page
            P6["/login - Auth Page"]:::page
            P7["/artist/dashboard - Artist Panel"]:::page
            P8["/admin - Admin Dashboard"]:::page
        end

        subgraph Components ["Reusable Components"]
            C1["Navbar"]:::component
            C2["ArtworkCard"]:::component
            C3["SearchFilter"]:::component
            C4["CartItem"]:::component
            C5["PaymentForm"]:::component
            C6["ImageZoom"]:::component
        end

        subgraph State ["State Management - Redux / Zustand"]
            S1["Auth State"]:::shared
            S2["Cart State"]:::shared
            S3["Product State"]:::shared
        end
    end

    P1 --> C1
    P1 --> C2
    P2 --> C2
    P2 --> C3
    P3 --> C6
    P4 --> C4
    P5 --> C5

    C2 --> S3
    C4 --> S2
    C1 --> S1
```

#### เทคโนโลยีที่เหมาะสม

| ประเภท | เทคโนโลยี | เหตุผล |
|---|---|---|
| **Web Frontend** | React / Next.js | Component-based, SSR สำหรับ SEO, Ecosystem ใหญ่ |
| **Mobile Frontend** | Flutter / React Native | Cross-platform (iOS + Android) จาก Codebase เดียว |
| **State Management** | Redux Toolkit / Zustand | จัดการ State ระดับ Global (Cart, Auth, Product) |
| **UI Framework** | Material UI / Chakra UI | Component สำเร็จรูป, Responsive, Accessible |

#### สิ่งที่ควรพิจารณา

| หมวด | รายละเอียด |
|---|---|
| **UX/UI** | Responsive Design, Mobile First, User Experience ที่ลื่นไหล |
| **Security** | JWT Authentication สำหรับ API calls, OAuth2 / SSO ผ่าน Google |
| **Scalability** | ใช้ CDN (CloudFront) สำหรับ Static Assets, Browser Caching, Lazy Loading สำหรับรูปภาพ |
| **Performance** | Code Splitting, Image Optimization (WebP), Virtual Scrolling สำหรับ Gallery ขนาดใหญ่ |

---

### 11.2 Backend Architecture (ส่วนประมวลผลหลักของระบบ)

#### หน้าที่หลักของ Backend
| หน้าที่ | รายละเอียด |
|---|---|
| จัดการ Business Logic | ประมวลผลคำสั่งซื้อ, คำนวณราคา, จัดการ workflow |
| ระบบสมาชิก | สมัคร, Login, จัดการ Profile, Role-based Access |
| ระบบคำสั่งซื้อ | สร้าง Order, อัปเดตสถานะ, ติดตามการจัดส่ง |
| ระบบชำระเงิน | เชื่อมต่อ Payment Gateway, จัดการ Webhook |
| ระบบจัดการสินค้า | CRUD ผลงานศิลปะ, อัปโหลดภาพ, สร้าง Watermark |
| เชื่อมต่อ API ภายนอก | Stripe, Google OAuth, AWS S3, Email Service |

#### โครงสร้างสถาปัตยกรรม Backend

**ระยะเริ่มต้น: Monolithic Architecture** (เหมาะกับทีมเล็ก พัฒนาเร็ว)

```mermaid
graph LR
    classDef mono fill:#0b5345,stroke:#34d399,stroke-width:1px,color:#fff
    classDef db fill:#7e5109,stroke:#f59e0b,stroke-width:1px,color:#fff

    Client["Client App"] --> Monolith

    subgraph Monolith ["Monolithic Backend - Node.js / Express"]
        direction TB
        Auth["Auth Module"]:::mono
        Product["Product Module"]:::mono
        Order["Order Module"]:::mono
        Payment["Payment Module"]:::mono
        Upload["Upload Module"]:::mono
    end

    Monolith --> DB["MySQL Database"]:::db
    Monolith --> S3["AWS S3"]:::db
```

**ระยะขยายระบบ: Microservices Architecture** (แยกบริการเป็นอิสระต่อการให้บริการ)

```mermaid
graph TB
    classDef gateway fill:#1e3a5f,stroke:#38bdf8,stroke-width:2px,color:#fff
    classDef service fill:#0b5345,stroke:#34d399,stroke-width:1px,color:#fff
    classDef db fill:#7e5109,stroke:#f59e0b,stroke-width:1px,color:#fff
    classDef queue fill:#831843,stroke:#ec4899,stroke-width:1px,color:#fff

    Client["Client Apps"] --> GW

    GW["API Gateway - Kong / Nginx"]:::gateway

    subgraph Services ["Microservices"]
        direction LR
        AuthSvc["Auth Service"]:::service
        ProductSvc["Product Service"]:::service
        OrderSvc["Order Service"]:::service
        PaymentSvc["Payment Service"]:::service
        NotifSvc["Notification Service"]:::service
    end

    GW --> AuthSvc
    GW --> ProductSvc
    GW --> OrderSvc
    GW --> PaymentSvc

    MQ["Message Queue - RabbitMQ"]:::queue

    OrderSvc --> MQ
    MQ --> PaymentSvc
    MQ --> NotifSvc

    AuthSvc --> DB1["MySQL - Users"]:::db
    ProductSvc --> DB2["MySQL - Products"]:::db
    ProductSvc --> S3["AWS S3 - Images"]:::db
    OrderSvc --> DB3["MySQL - Orders"]:::db
    PaymentSvc --> DB4["MySQL - Payments"]:::db
    NotifSvc --> DB5["MongoDB - Logs"]:::db

    AuthSvc --> Redis["Redis - Sessions"]:::db
```

#### เทคโนโลยีที่เหมาะสม

| ประเภท | เทคโนโลยี | เหตุผล |
|---|---|---|
| **Backend Framework** | Node.js / Express.js | Non-blocking I/O, npm ecosystem ขนาดใหญ่, JavaScript ตลอด Stack |
| **Alternative** | Django (Python) / Spring Boot (Java) | เหมาะกับทีมที่ถนัด Python หรือ Java |
| **API Architecture** | REST API / GraphQL | REST เรียบง่าย, GraphQL ยืดหยุ่นสำหรับ query ซับซ้อน |
| **API Gateway** | Kong / Nginx / AWS API Gateway | Authentication, Routing, Rate Limiting, Load Balancing |
| **Message Queue** | RabbitMQ / Apache Kafka | สำหรับ Async Processing (ส่งอีเมล, ประมวลผลภาพ) |

#### DevOps / Infrastructure

| ประเภท | เทคโนโลยี | เหตุผล |
|---|---|---|
| **Container** | Docker + Docker Compose | แยก Environment, Deploy สะดวก, Reproducible |
| **CI/CD** | GitHub Actions | Automated Testing, Build, Deploy Pipeline |
| **Cloud** | Amazon Web Services (AWS) | EC2 / ECS สำหรับ Compute, S3 สำหรับ Storage, RDS สำหรับ Database |
| **Monitoring** | Prometheus + Grafana | ติดตาม Performance, Alert เมื่อระบบมีปัญหา |
| **Logging** | ELK Stack (Elasticsearch, Logstash, Kibana) | รวมศูนย์ Log จากทุก Service, ค้นหาง่าย |

#### แผนภาพ CI/CD Pipeline

```mermaid
graph LR
    classDef dev fill:#312e81,stroke:#818cf8,stroke-width:1px,color:#fff
    classDef ci fill:#0b5345,stroke:#34d399,stroke-width:1px,color:#fff
    classDef deploy fill:#7e5109,stroke:#f59e0b,stroke-width:1px,color:#fff

    A["Developer Push Code"]:::dev --> B["GitHub Actions Triggered"]:::ci
    B --> C["Run Unit Tests"]:::ci
    C --> D["Build Docker Image"]:::ci
    D --> E["Push to Container Registry"]:::ci
    E --> F{"Environment?"}:::ci
    F -->|Staging| G["Deploy to Staging"]:::deploy
    F -->|Production| H["Deploy to AWS ECS"]:::deploy
    G --> I["Run Integration Tests"]:::ci
    I --> H
```

---

### 11.3 Database Architecture (ระบบจัดเก็บข้อมูล)

#### หน้าที่หลักของ Database
| หน้าที่ | รายละเอียด |
|---|---|
| เก็บข้อมูลผู้ใช้ | Profile, Credentials, Role, Address |
| เก็บสินค้า | ข้อมูลผลงานศิลปะ, ราคา, หมวดหมู่, Tags |
| คำสั่งซื้อ | Order, Order Items, สถานะ, ที่อยู่จัดส่ง |
| รายการชำระเงิน | Transaction, Payment Method, Status |
| ประวัติการทำงาน | Activity Log, Audit Trail, Session |

#### โครงสร้าง Database ตามรูปแบบฐานข้อมูล

**Relational Database (SQL)** — เหมาะกับข้อมูลธุรกรรม ระบบสั่งซื้อ ความถูกต้องของข้อมูลสูง

| ฐานข้อมูล | ใช้เก็บข้อมูล | จุดเด่น |
|---|---|---|
| **MySQL** | Users, Artists, Artworks, Orders, Payments, Categories | ACID Compliance, เหมาะกับข้อมูลธุรกรรม |
| **PostgreSQL** | ทางเลือก — รองรับ JSON, Full-text Search | Advanced features, Extensible |
| **Microsoft SQL Server** | ทางเลือกองค์กรขนาดใหญ่ | Enterprise-grade, BI Integration |

**NoSQL Database** — เหมาะกับข้อมูล Log, Session, Big Data, Recommendation

| ฐานข้อมูล | ใช้เก็บข้อมูล | จุดเด่น |
|---|---|---|
| **MongoDB** | Reviews, Tags, Search Index, Activity Logs | Schema ยืดหยุ่น, Document-based |
| **Redis** | Session, Cache, Cart (ชั่วคราว), Rate Limiting | In-memory, เร็วมาก, TTL support |

#### แผนภาพ Database Architecture

```mermaid
graph TB
    classDef sql fill:#1e3a5f,stroke:#38bdf8,stroke-width:1px,color:#fff
    classDef nosql fill:#7e5109,stroke:#f59e0b,stroke-width:1px,color:#fff
    classDef cache fill:#831843,stroke:#ec4899,stroke-width:1px,color:#fff
    classDef storage fill:#1b4332,stroke:#34d399,stroke-dasharray: 5 5,color:#fff

    App["Backend Application"] --> ReadWrite

    subgraph ReadWrite ["Data Access Layer"]
        ORM["ORM - Sequelize / Prisma"]
    end

    subgraph SQL_DB ["Relational Database - MySQL 8"]
        direction LR
        T1["users"]:::sql
        T2["artists"]:::sql
        T3["artworks"]:::sql
        T4["categories"]:::sql
        T5["orders"]:::sql
        T6["order_items"]:::sql
        T7["payments"]:::sql
        T8["wishlists"]:::sql
    end

    subgraph NoSQL_DB ["NoSQL - MongoDB"]
        direction LR
        M1["reviews"]:::nosql
        M2["tags"]:::nosql
        M3["activity_logs"]:::nosql
        M4["search_index"]:::nosql
    end

    subgraph Cache_DB ["Cache Layer - Redis"]
        direction LR
        R1["Session Store"]:::cache
        R2["Artwork Cache"]:::cache
        R3["Cart Temp Data"]:::cache
        R4["Rate Limiter"]:::cache
    end

    subgraph File_Storage ["File Storage - AWS S3"]
        direction LR
        FS1["High-res Originals - Private"]:::storage
        FS2["Watermarked Previews - Public"]:::storage
        FS3["User Avatars"]:::storage
    end

    ORM --> SQL_DB
    App --> NoSQL_DB
    App --> Cache_DB
    App --> File_Storage
```

#### Database Design Principles

| หลักการ | รายละเอียด |
|---|---|
| **Normalization** | ออกแบบตาราง SQL ให้อยู่ในรูปแบบ 3NF (Third Normal Form) เพื่อลดความซ้ำซ้อนของข้อมูล |
| **Indexing** | สร้าง Index บนคอลัมน์ที่ใช้ค้นหาบ่อย เช่น `email`, `artwork_id`, `order_date` |
| **Foreign Keys** | กำหนด FK Constraints เพื่อรักษา Referential Integrity ระหว่างตาราง |
| **Backup & Recovery** | Automated Daily Backup, Point-in-Time Recovery, Cross-region Replication |
| **Partitioning** | แบ่ง Partition ตาราง `orders` และ `payments` ตามเดือน เมื่อข้อมูลมีขนาดใหญ่ |
| **Read Replica** | ใช้ Read Replica สำหรับ Query ที่ไม่ต้องการ Real-time เพื่อลด Load บน Primary DB |

#### ตัวอย่าง SQL Schema

```sql
-- สร้างตาราง users
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role ENUM('customer', 'artist', 'admin') DEFAULT 'customer',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_email (email),
    INDEX idx_role (role)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- สร้างตาราง artworks
CREATE TABLE artworks (
    id INT PRIMARY KEY AUTO_INCREMENT,
    artist_id INT NOT NULL,
    category_id INT NOT NULL,
    title VARCHAR(200) NOT NULL,
    description TEXT,
    high_res_url VARCHAR(500) NOT NULL,
    preview_url VARCHAR(500) NOT NULL,
    price_digital DECIMAL(10,2) NOT NULL,
    price_print_base DECIMAL(10,2),
    tags VARCHAR(500),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (artist_id) REFERENCES artists(id) ON DELETE CASCADE,
    FOREIGN KEY (category_id) REFERENCES categories(id),
    INDEX idx_artist (artist_id),
    INDEX idx_category (category_id),
    FULLTEXT INDEX idx_search (title, description, tags)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- สร้างตาราง orders
CREATE TABLE orders (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    order_date DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    status ENUM('pending','paid','printing','shipped','completed','cancelled') DEFAULT 'pending',
    total_amount DECIMAL(10,2) NOT NULL,
    shipping_address TEXT,
    tracking_number VARCHAR(100),
    FOREIGN KEY (user_id) REFERENCES users(id),
    INDEX idx_user (user_id),
    INDEX idx_status (status),
    INDEX idx_date (order_date)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

---

### 11.4 สรุปภาพรวม Software Architecture

```mermaid
graph TB
    classDef user fill:#312e81,stroke:#818cf8,stroke-width:2px,color:#fff
    classDef frontend fill:#3b3395,stroke:#818cf8,stroke-width:1px,color:#fff
    classDef gateway fill:#1e3a5f,stroke:#38bdf8,stroke-width:2px,color:#fff
    classDef service fill:#0b5345,stroke:#34d399,stroke-width:1px,color:#fff
    classDef db fill:#7e5109,stroke:#f59e0b,stroke-width:1px,color:#fff
    classDef ext fill:#831843,stroke:#ec4899,stroke-width:1px,color:#fff

    U1(("👤 Customer")):::user
    U2(("🎨 Artist")):::user
    U3(("🔧 Admin")):::user

    subgraph FRONT ["Layer 1: Frontend Architecture"]
        Web["Web App - React/Next.js - SPA"]:::frontend
        Mobile["Mobile App - Flutter"]:::frontend
        AdminUI["Admin Dashboard"]:::frontend
    end

    U1 --> Web
    U1 --> Mobile
    U2 --> Web
    U3 --> AdminUI

    subgraph BACK ["Layer 2: Backend Architecture"]
        GW["API Gateway - Authentication - Routing - Rate Limit"]:::gateway

        subgraph MONO ["Phase 1: Monolithic"]
            M1["Express.js - All-in-One"]:::service
        end

        subgraph MICRO ["Phase 2: Microservices"]
            MS1["Auth Service"]:::service
            MS2["Product Service"]:::service
            MS3["Order Service"]:::service
            MS4["Payment Service"]:::service
            MS5["Notification Service"]:::service
        end
    end

    Web --> GW
    Mobile --> GW
    AdminUI --> GW

    GW --> MONO
    GW --> MICRO

    subgraph DATA ["Layer 3: Database Architecture"]
        MySQL["MySQL 8 - Transactions - Users / Orders / Payments"]:::db
        Mongo["MongoDB - Reviews / Tags / Logs"]:::db
        RedisDB["Redis - Session / Cache"]:::db
        AWS["AWS S3 - Images"]:::db
    end

    MONO --> DATA
    MICRO --> DATA

    subgraph EXTERNAL ["External Services"]
        StripeAPI["Stripe Payment"]:::ext
        GoogleAPI["Google OAuth2"]:::ext
        CDNAPI["CloudFront CDN"]:::ext
        EmailAPI["SendGrid Email"]:::ext
    end

    BACK --> EXTERNAL
```

| Layer | สถาปัตยกรรม | เทคโนโลยีหลัก | หน้าที่ |
|---|---|---|---|
| **Frontend** | Component-Based / SPA | React, Next.js, Flutter | แสดง UI, จัดการ State, เรียก API |
| **Backend** | Monolithic → Microservices | Node.js, Express, Docker | Business Logic, API, Authentication |
| **Database** | SQL + NoSQL + Cache | MySQL, MongoDB, Redis | จัดเก็บข้อมูล, Session, Caching |
| **Infrastructure** | Cloud-native | AWS, Docker, GitHub Actions | CI/CD, Hosting, Storage, CDN |

