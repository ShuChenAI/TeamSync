# TeamSync 網路通訊流程圖

## 系統架構概覽

本文件描述 TeamSync 後端系統的網路通訊架構，展示行動前端如何通過防火牆與後端互動，以及 AI 工作流程如何安全地訪問內部資料庫和企業系統。

## 網路架構圖（含 RPA 整合）

```mermaid
graph TB
    subgraph External["🌐 外部網路"]
        Frontend[📱 Frontend App<br/>React Native/Flutter]
    end

    subgraph SecurityLayer["🔒 安全層"]
        DMZ[🔥 防火牆<br/>Nginx/HAProxy]
    end

    subgraph JitsiCluster["📹 Jitsi 視訊服務集群"]
        direction LR
        JitsiWeb[🌐 Jitsi Web]
        Prosody[💬 Prosody XMPP]
        Jicofo[🎯 Jicofo]
        JVB[🎥 Jitsi Videobridge]

        JitsiWeb <--> Prosody
        Jicofo <--> Prosody
        JVB <--> Jicofo
    end

    subgraph CoreServices["⚡ 核心後端服務"]
        direction LR
        FastAPI[⚡ FastAPI Backend<br/>Port 8888]
        Mosquitto[📡 Mosquitto MQTT<br/>Port 1883/8883]
        RedisCache[💾 Redis Cache<br/>Port 6379]
        MinIO[📦 MinIO Storage<br/>Port 9000]
    end

    subgraph AILayer["🤖 AI 智能層"]
        direction LR
        AIAgent[🤖 AI Agent<br/>LangChain]
        DBTools[🛠️ DB Query Tools]
        RPATools[🤖 RPA Automation]
    end

    subgraph DataLayer["🗄️ 資料存儲層"]
        direction LR
        PostgreSQL[(🗄️ PostgreSQL)]
        MySQL[(🗄️ MySQL)]
        Qdrant[(🔍 Qdrant Vector DB)]
    end

    subgraph AutomationLayer["🔧 自動化層"]
        direction LR
        RPAServer[📊 RPA Server]
        UiPath[🔧 UiPath Orchestrator]
        RPARobots[🤖 RPA Robots]

        RPAServer --> UiPath
        UiPath --> RPARobots
    end

    subgraph EnterpriseLayer["🏢 企業應用層"]
        direction LR
        Salesforce[📇 Salesforce CRM]
        SAP[📦 SAP ERP]
        LegacyApp[🖥️ Legacy Systems]
    end

    %% External connections
    Frontend -->|HTTPS/WSS| DMZ
    Frontend -.->|MQTT over WSS| Mosquitto
    Frontend -->|Video/WebRTC| JitsiWeb
    Frontend <-->|Media Stream<br/>UDP:10000| JVB

    %% Security layer
    DMZ -->|Reverse Proxy<br/>:443→:8888| FastAPI

    %% Core service connections
    FastAPI -->|JWT Auth| JitsiWeb
    FastAPI <-->|Publish/Subscribe| Mosquitto
    FastAPI <-->|Session/Cache| RedisCache
    FastAPI <-->|File Storage| MinIO
    FastAPI -->|AI Request| AIAgent

    %% AI layer connections
    AIAgent -->|RAG Search| Qdrant
    AIAgent -->|SQL Query| DBTools
    AIAgent -->|Automation| RPATools
    DBTools -->|Query| PostgreSQL
    DBTools -->|Query| MySQL

    %% RPA connections
    RPATools -->|Trigger| RPAServer
    RPARobots -->|UI Automation| Salesforce
    RPARobots -->|UI Automation| SAP
    RPARobots -->|Screen Scraping| LegacyApp

    classDef ext fill:#ffcccc,stroke:#ff6666,stroke-width:2px
    classDef security fill:#ffffcc,stroke:#ffcc00,stroke-width:2px
    classDef jitsi fill:#ffd9b3,stroke:#ff8800,stroke-width:2px
    classDef core fill:#ccffcc,stroke:#66cc66,stroke-width:2px
    classDef ai fill:#ccccff,stroke:#6666ff,stroke-width:2px
    classDef data fill:#ffccff,stroke:#ff66ff,stroke-width:2px
    classDef rpa fill:#ffcccc,stroke:#cc3333,stroke-width:2px
    classDef ent fill:#ccffff,stroke:#0099cc,stroke-width:2px

    class Frontend ext
    class DMZ security
    class JitsiWeb,Jicofo,JVB,Prosody jitsi
    class FastAPI,Mosquitto,RedisCache,MinIO core
    class AIAgent,DBTools,RPATools ai
    class PostgreSQL,MySQL,Qdrant data
    class RPAServer,UiPath,RPARobots rpa
    class Salesforce,SAP,LegacyApp ent
```

## TeamSync 視訊會議與智能轉錄功能架構

本節詳細說明 TeamSync 的進階功能：Jitsi 視訊會議整合與即時智能轉錄系統。

### 視訊會議與轉錄整合架構圖

```mermaid
graph TB
    subgraph ClientTier["👥 客戶端層"]
        direction LR
        WebApp["🌐 Web App<br/>Jitsi IFrame"]
        WebAudio["🎤 Web Audio API"]
    end

    subgraph GatewayTier["🚪 API 閘道層"]
        direction LR
        FastAPI["📹 FastAPI:8888"]
        WSServer["🎙️ WebSocket Server"]
    end

    subgraph VideoTier["📹 視訊服務層"]
        direction LR
        JitsiWeb["🌐 Jitsi Web:443"]
        Prosody["💬 Prosody:5222"]
        Jicofo["🎯 Jicofo:8888"]
        JVB["🎥 JVB:10000"]

        JitsiWeb -.-> Prosody
        Jicofo -.-> Prosody
        JVB -.-> Jicofo
    end

    subgraph CloudTier["☁️ 雲端服務層"]
        direction LR
        AWS["🌍 AWS Transcribe"]
    end

    subgraph ProcessingTier["⚙️ 處理層"]
        direction LR
        subgraph AICore["🤖 AI 核心"]
            AIEngine["🤖 LangChain"]
            Qdrant[("🔍 Qdrant")]
            Redis[("💾 Redis")]

            AIEngine <--> Qdrant
            AIEngine <--> Redis
        end

        subgraph Translation["🌐 翻譯服務"]
            TransSvc["🌐 Translator"]
            Google["Google API"]
            DeepL["DeepL API"]

            TransSvc --> Google
            TransSvc -.-> DeepL
        end
    end

    subgraph MessageTier["📡 訊息層"]
        MQTT["📡 Mosquitto MQTT"]
    end

    %% Main flow connections
    WebApp -->|1.請求會議| FastAPI
    FastAPI -->|2.JWT| JitsiWeb
    WebApp -->|3.WebRTC| JVB

    WebAudio -->|4.音訊| WSServer
    WSServer -->|5.簽名URL| AWS
    AWS -->|6.轉錄| WSServer

    WSServer -->|7.原文| AIEngine
    AIEngine -->|8.修正文| TransSvc
    TransSvc -->|9.發布| MQTT
    MQTT -->|10.訂閱| WebApp

    classDef client fill:#ffcccc,stroke:#ff6666,stroke-width:2px
    classDef gateway fill:#ccffcc,stroke:#66cc66,stroke-width:2px
    classDef video fill:#ffd9b3,stroke:#ff8800,stroke-width:2px
    classDef cloud fill:#e6f3ff,stroke:#0066cc,stroke-width:2px
    classDef ai fill:#ccccff,stroke:#6666ff,stroke-width:2px
    classDef message fill:#fff2cc,stroke:#d6b656,stroke-width:2px

    class WebApp,WebAudio client
    class FastAPI,WSServer gateway
    class JitsiWeb,Jicofo,JVB,Prosody video
    class AWS cloud
    class AIEngine,Qdrant,Redis,TransSvc,Google,DeepL ai
    class MQTT message
```