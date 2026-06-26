# แผนภาพสถาปัตยกรรมระบบ (System Architecture)

```mermaid
graph TD
    %% Styling Definitions
    classDef frontend fill:#3f37c9,stroke:#333,stroke-width:2px,color:#fff;
    classDef backend fill:#065a43,stroke:#333,stroke-width:2px,color:#fff;
    classDef database fill:#9a5200,stroke:#333,stroke-width:2px,color:#fff;
    classDef subBlock fill:#ffffff,stroke:#666,stroke-width:1px,color:#000;
    classDef storage fill:#1b4332,stroke:#fff,stroke-dasharray: 5 5,color:#fff;

    %% --- Frontend Layer ---
    subgraph Frontend_Layer ["Frontend Layer — ร้านขายรูปศิลปะ (React / Next.js · SSR · Mobile-first)"]
        direction LR
        F1[Art gallery<br/>Browse artwork] ::: subBlock
        F2[Artist profile<br/>Portfolio · bio] ::: subBlock
        F3[Artwork detail<br/>Zoom · size · price] ::: subBlock
        F4[Cart / checkout<br/>Print size · framing] ::: subBlock
        F5[Artist panel<br/>Upload · manage] ::: subBlock
        Auth["สมัครสมาชิก / Login<br/>JWT · OAuth2 (Google)<br/>บันทึก Wishlist & ประวัติการสั่งซื้อ"] ::: subBlock
    end
    style Frontend_Layer fill:#3b3395,stroke:#fff,stroke-width:2px,color:#fff

    %% --- Backend Layer ---
    subgraph Backend_Layer ["Backend Layer — Business Logic (Node.js / Express · Docker · GitHub Actions · AWS)"]
        direction TB
        B1[API Gateway<br/>Auth · rate limit] ::: subBlock
        B2[Artwork service<br/>Upload · metadata] ::: subBlock
        B3[Order service<br/>Print size · shipping] ::: subBlock
        B4[Payment service<br/>Stripe · PromptPay] ::: subBlock
        
        S3["Cloud storage: AWS S3<br/>เก็บไฟล์ต้นฉบับ (high-res) + ไฟล์ Watermark สำหรับ Preview"] ::: storage
    end
    style Backend_Layer fill:#0b5345,stroke:#fff,stroke-width:2px,color:#fff

    %% --- Database Layer ---
    subgraph Database_Layer ["Database Layer — จัดเก็บข้อมูล (Relational + NoSQL + Cache)"]
        direction LR
        subgraph MySQL_DB ["Relational DB — MySQL"]
            DB1["Users · Artists · Artworks<br/>Orders · Payments · Categories<br/>ข้อมูลธุรกรรม ต้องการความถูกต้องสูง"] ::: subBlock
        end
        style MySQL_DB fill:#7e5109,stroke:#fff,color:#fff
        
        subgraph NoSQL_Redis ["NoSQL + Redis"]
            DB2["MongoDB — Reviews · Tags · Search index<br/>Redis — Session · Artwork cache<br/>เพิ่มความเร็ว ลด load บน MySQL"] ::: subBlock
        end
        style NoSQL_Redis fill:#7e5109,stroke:#fff,color:#fff
    end
    style Database_Layer fill:#784212,stroke:#fff,stroke-width:2px,color:#fff

    %% --- Connections (Data Flow) ---
    Frontend_Layer -->|REST API| B1
    B1 --> B2
    B1 --> B3
    B1 --> B4
    
    B2 <--> S3
    
    B2 -->|Query| Database_Layer
    B3 -->|Query| Database_Layer
    B4 -->|Query| Database_Layer