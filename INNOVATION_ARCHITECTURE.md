# 业务创新挖掘平台架构图

## 系统整体架构

```mermaid
graph TB
    subgraph "数据输入层"
        A1[业务文档<br/>规划材料/需求文档/领导讲话]
        A2[外部技术趋势<br/>GitHub/今日头条/微信/博客/arXiv]
    end

    subgraph "数据处理层"
        B1[文档解析器<br/>提取痛点和需求]
        B2[趋势分析器<br/>评估技术成熟度]
    end

    subgraph "AI智能体协作层 - CrewAI"
        C1[技术趋势分析师<br/>分析AI技术进展]
        C2[业务痛点分析师<br/>识别业务需求]
        C3[技术匹配专家<br/>匹配技术与场景]
        C4[创新方案设计师<br/>设计完整方案]
        C5[可行性分析师<br/>四维度评估]
    end

    subgraph "分析引擎"
        D1[CrewAI协作引擎]
        D2[GPT-4大模型]
    end

    subgraph "输出生成层"
        E1[创新方案<br/>痛点/技术/方案/计划]
        E2[可行性报告<br/>技术/业务/经济/实施]
        E3[决策建议<br/>推荐级别/后续步骤]
    end

    subgraph "数据存储层"
        F1[(PostgreSQL<br/>业务文档/技术趋势/方案)]
    end

    subgraph "应用展示层"
        G1[React前端<br/>方案展示/报告查看]
    end

    A1 --> B1
    A2 --> B2
    B1 --> C2
    B2 --> C1

    C1 --> D1
    C2 --> D1
    C3 --> D1
    C4 --> D1
    C5 --> D1

    D1 <--> D2

    D1 --> E1
    D1 --> E2
    D1 --> E3

    B1 --> F1
    B2 --> F1
    E1 --> F1
    E2 --> F1
    E3 --> F1

    F1 --> G1

    style C1 fill:#e1f5ff
    style C2 fill:#e1f5ff
    style C3 fill:#e1f5ff
    style C4 fill:#e1f5ff
    style C5 fill:#e1f5ff
    style D1 fill:#fff7e6
    style D2 fill:#fff7e6
```

## 5个AI智能体协作流程

```mermaid
sequenceDiagram
    participant U as 用户/系统
    participant A1 as 技术趋势分析师
    participant A2 as 业务痛点分析师
    participant A3 as 技术匹配专家
    participant A4 as 创新方案设计师
    participant A5 as 可行性分析师
    participant LLM as GPT-4大模型

    U->>A1: 输入技术趋势数据
    A1->>LLM: 分析技术趋势
    LLM-->>A1: 技术总结+关键技术+应用场景

    U->>A2: 输入业务文档
    A2->>LLM: 分析业务痛点
    LLM-->>A2: 痛点列表+需求+场景

    A1->>A3: 传递技术分析结果
    A2->>A3: 传递痛点分析结果
    A3->>LLM: 匹配技术与业务
    LLM-->>A3: 技术业务匹配+高价值机会

    A3->>A4: 传递匹配结果
    A4->>LLM: 设计创新方案
    LLM-->>A4: 完整方案(概述+计划+收益)

    A4->>A5: 传递创新方案
    A5->>LLM: 可行性分析
    LLM-->>A5: 四维度评估+综合评分+建议

    A5->>U: 返回最终结果
```

## 技术趋势数据采集架构

```mermaid
graph LR
    subgraph "外部数据源"
        S1[GitHub Trending<br/>开源项目]
        S2[今日头条<br/>行业资讯]
        S3[微信公众号<br/>技术文章]
        S4[技术博客<br/>Medium/CSDN]
        S5[arXiv<br/>学术论文]
    end

    subgraph "数据采集层"
        C1[爬虫引擎]
        C2[API接口]
    end

    subgraph "数据处理"
        P1[内容提取]
        P2[去重清洗]
        P3[AI摘要]
    end

    subgraph "数据评估"
        E1[成熟度评估]
        E2[重要性评分]
        E3[应用场景识别]
    end

    subgraph "存储"
        DB[(技术趋势库)]
    end

    S1 --> C1
    S2 --> C2
    S3 --> C1
    S4 --> C1
    S5 --> C2

    C1 --> P1
    C2 --> P1

    P1 --> P2
    P2 --> P3

    P3 --> E1
    E1 --> E2
    E2 --> E3

    E3 --> DB
```

## 可行性分析四维度架构

```mermaid
graph TB
    subgraph "输入"
        I[创新方案]
    end

    subgraph "技术可行性分析"
        T1[技术成熟度评估]
        T2[技术架构分析]
        T3[团队能力评估]
        T4[技术风险识别]
        T5[技术可行性评分<br/>0-100]
    end

    subgraph "业务可行性分析"
        B1[业务价值分析]
        B2[业务适配度评估]
        B3[用户接受度预测]
        B4[业务风险识别]
        B5[业务可行性评分<br/>0-100]
    end

    subgraph "经济可行性分析"
        E1[投入成本分析]
        E2[预期收益预测]
        E3[ROI计算]
        E4[投资回收期]
        E5[经济可行性评分<br/>0-100]
    end

    subgraph "实施可行性分析"
        M1[资源需求分析]
        M2[实施难度评估]
        M3[时间合理性]
        M4[风险控制措施]
        M5[实施可行性评分<br/>0-100]
    end

    subgraph "综合评估"
        F1[综合评分计算]
        F2[推荐级别判定]
        F3[后续步骤建议]
    end

    subgraph "输出"
        O[可行性报告]
    end

    I --> T1
    T1 --> T2 --> T3 --> T4 --> T5

    I --> B1
    B1 --> B2 --> B3 --> B4 --> B5

    I --> E1
    E1 --> E2 --> E3 --> E4 --> E5

    I --> M1
    M1 --> M2 --> M3 --> M4 --> M5

    T5 --> F1
    B5 --> F1
    E5 --> F1
    M5 --> F1

    F1 --> F2
    F2 --> F3
    F3 --> O

    style T5 fill:#e6f7ff
    style B5 fill:#f6ffed
    style E5 fill:#fff7e6
    style M5 fill:#fff0f6
    style F1 fill:#f9f0ff
```

## 数据模型关系图

```mermaid
erDiagram
    BusinessDocument ||--o{ InnovationProposal : "生成"
    InnovationProposal ||--|| FeasibilityReport : "包含"
    InnovationProposal }o--o{ TechTrend : "匹配"

    BusinessDocument {
        int id PK
        string title
        string doc_type
        string department
        json extracted_pain_points
        json extracted_requirements
        string status
    }

    TechTrend {
        int id PK
        string title
        string source
        string category
        json key_technologies
        json potential_applications
        string maturity_level
        int importance_score
    }

    InnovationProposal {
        int id PK
        string title
        string business_scenario
        json pain_points
        json matched_technologies
        text solution_overview
        text implementation_plan
        json expected_benefits
        string status
    }

    FeasibilityReport {
        int id PK
        int proposal_id FK
        text technical_feasibility
        int technical_score
        text business_feasibility
        int business_score
        text economic_feasibility
        int economic_score
        text implementation_feasibility
        int implementation_score
        int overall_score
        string recommendation
    }
```

## 前后端技术栈架构

```mermaid
graph TB
    subgraph "前端层 - React"
        F1[React 18]
        F2[Ant Design 5]
        F3[React Router]
        F4[Axios]
    end

    subgraph "API层 - FastAPI"
        A1[业务文档API]
        A2[技术趋势API]
        A3[创新方案API]
        A4[分析引擎API]
    end

    subgraph "业务逻辑层"
        L1[CrewAI编排]
        L2[5个AI Agent]
        L3[结果解析器]
    end

    subgraph "AI服务层"
        AI1[OpenAI GPT-4]
        AI2[LangChain]
    end

    subgraph "数据访问层"
        D1[SQLAlchemy ORM]
    end

    subgraph "数据库层"
        DB1[(PostgreSQL)]
    end

    F1 --> F2
    F1 --> F3
    F1 --> F4

    F4 --> A1
    F4 --> A2
    F4 --> A3
    F4 --> A4

    A1 --> L1
    A2 --> L1
    A3 --> L1
    A4 --> L1

    L1 --> L2
    L2 --> L3

    L2 --> AI1
    L2 --> AI2

    L1 --> D1
    L3 --> D1

    D1 --> DB1

    style F1 fill:#61dafb
    style A1 fill:#009688
    style L2 fill:#ff6b6b
    style AI1 fill:#10a37f
    style DB1 fill:#336791
```

## 系统工作流程

```mermaid
flowchart TD
    Start([开始]) --> Upload[上传业务文档]
    Upload --> Import[导入技术趋势]
    Import --> Select[选择分析范围]
    Select --> Trigger[触发AI分析]

    Trigger --> Agent1[智能体1: 分析技术趋势]
    Trigger --> Agent2[智能体2: 分析业务痛点]

    Agent1 --> Agent3[智能体3: 技术业务匹配]
    Agent2 --> Agent3

    Agent3 --> Agent4[智能体4: 设计创新方案]
    Agent4 --> Agent5[智能体5: 可行性分析]

    Agent5 --> Save[保存到数据库]
    Save --> Display[前端展示结果]

    Display --> Decision{决策}
    Decision -->|批准| Implement[进入实施阶段]
    Decision -->|修改| Trigger
    Decision -->|拒绝| Archive[归档]

    Implement --> Track[跟踪实施进度]
    Archive --> End([结束])
    Track --> End

    style Agent1 fill:#e1f5ff
    style Agent2 fill:#e1f5ff
    style Agent3 fill:#e1f5ff
    style Agent4 fill:#e1f5ff
    style Agent5 fill:#e1f5ff
    style Decision fill:#fff7e6
```

## 推荐级别判定逻辑

```mermaid
graph TD
    Start[综合评分] --> Check1{评分 >= 85?}
    Check1 -->|是| R1[强烈推荐<br/>Strongly Recommend]
    Check1 -->|否| Check2{评分 >= 70?}
    Check2 -->|是| R2[推荐<br/>Recommend]
    Check2 -->|否| Check3{评分 >= 60?}
    Check3 -->|是| R3[有条件推荐<br/>Conditional]
    Check3 -->|否| R4[不推荐<br/>Not Recommend]

    R1 --> Action1[建议立即启动项目]
    R2 --> Action2[建议推进实施]
    R3 --> Action3[需要优化方案]
    R4 --> Action4[不建议实施]

    style R1 fill:#52c41a
    style R2 fill:#1890ff
    style R3 fill:#faad14
    style R4 fill:#f5222d
```

---

**架构说明**：

1. **数据输入层**：收集行内业务文档和行外技术趋势
2. **AI智能体层**：5个专业智能体分工协作分析
3. **分析引擎**：基于CrewAI和GPT-4的智能分析
4. **输出生成层**：生成创新方案和可行性报告
5. **应用展示层**：React前端可视化展示

**架构版本**：v1.0
**更新日期**：2025-11-17
