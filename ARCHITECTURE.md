# 会议助手多Agent架构图

## 1. 整体系统架构图

```mermaid
graph TB
    subgraph "前端层 Frontend"
        UI[React 用户界面<br/>EnhancedMeetingPage]
        Upload[上传音频<br/>选择模板类型]
        Display[展示分析结果<br/>章节/决策/待办]
    end

    subgraph "API层 FastAPI"
        Router[API Router<br/>meetings.py]
        Upload_EP[POST /upload]
        Analyze_EP[POST /{id}/analyze]
        Minutes_EP[GET /{id}/minutes]
        Catchup_EP[GET /{id}/catchup]
    end

    subgraph "AI处理层"
        AudioProc[音频处理器<br/>audio_processor]
        EnhancedCrew[增强版会议分析Crew<br/>enhanced_meeting_crew]
        TemplateEngine[模板引擎<br/>meeting_templates]
    end

    subgraph "多Agent协作 CrewAI"
        Agent1[会议结构分析师<br/>Structure Analyst]
        Agent2[决策识别专家<br/>Decision Expert]
        Agent3[任务提取专家<br/>Task Extractor]
        Agent4[说话人观点分析师<br/>Speaker Analyst]
        Agent5[会议总结专家<br/>Summary Expert]
    end

    subgraph "外部服务 External Services"
        Whisper[OpenAI Whisper API<br/>语音转文本]
        Diarization[pyannote.audio<br/>说话人识别]
        GPT4[GPT-4 Turbo<br/>智能分析]
    end

    subgraph "数据存储层 Database"
        DB[(PostgreSQL)]
        MeetingTable[MeetingMinute<br/>会议主表]
        ChapterTable[MeetingChapter<br/>章节表]
        DecisionTable[KeyDecision<br/>决策表]
        TodoTable[TodoItem<br/>待办表]
        SpeakerTable[Speaker<br/>说话人表]
    end

    UI --> Upload
    UI --> Display
    Upload --> Upload_EP
    Display --> Router

    Upload_EP --> AudioProc
    Analyze_EP --> AudioProc
    Analyze_EP --> EnhancedCrew
    Minutes_EP --> TemplateEngine
    Catchup_EP --> EnhancedCrew

    AudioProc --> Whisper
    AudioProc --> Diarization

    EnhancedCrew --> Agent1
    EnhancedCrew --> Agent2
    EnhancedCrew --> Agent3
    EnhancedCrew --> Agent4
    EnhancedCrew --> Agent5

    Agent1 --> GPT4
    Agent2 --> GPT4
    Agent3 --> GPT4
    Agent4 --> GPT4
    Agent5 --> GPT4

    Router --> DB
    DB --> MeetingTable
    MeetingTable --> ChapterTable
    MeetingTable --> DecisionTable
    MeetingTable --> TodoTable
    MeetingTable --> SpeakerTable

    style Agent1 fill:#e1f5ff
    style Agent2 fill:#fff3e0
    style Agent3 fill:#f3e5f5
    style Agent4 fill:#e8f5e9
    style Agent5 fill:#fce4ec
```

## 2. 多Agent协作流程图

```mermaid
sequenceDiagram
    participant User as 用户
    participant API as FastAPI
    participant Audio as 音频处理器
    participant Crew as Enhanced Crew
    participant A1 as 结构分析师
    participant A2 as 决策专家
    participant A3 as 任务专家
    participant A4 as 观点分析师
    participant A5 as 总结专家
    participant DB as 数据库

    User->>API: 1. 上传音频文件
    API->>DB: 创建会议记录
    API->>Audio: 2. 开始分析

    Audio->>Audio: 语音转文本<br/>(Whisper)
    Audio->>Audio: 说话人识别<br/>(pyannote)
    Audio-->>API: 转录文本+说话人片段

    API->>Crew: 3. 启动AI分析

    Note over Crew,A5: Sequential Process 顺序执行

    Crew->>A1: Task 1: 章节划分
    activate A1
    A1->>A1: 分析会议结构<br/>识别议程转换
    A1-->>Crew: 返回章节列表<br/>[{title, start, end, summary}]
    deactivate A1

    Crew->>A2: Task 2: 决策提取<br/>(context: Task1结果)
    activate A2
    A2->>A2: 识别关键决策<br/>分类决策类型
    A2-->>Crew: 返回决策列表<br/>[{content, type, priority}]
    deactivate A2

    Crew->>A3: Task 3: 待办提取<br/>(context: Task1结果)
    activate A3
    A3->>A3: 识别任务分配<br/>提取负责人
    A3-->>Crew: 返回待办列表<br/>[{task, assignee, priority}]
    deactivate A3

    Crew->>A4: Task 4: 观点分析
    activate A4
    A4->>A4: 分析每位说话人<br/>提炼核心观点
    A4-->>Crew: 返回观点映射<br/>{speaker: viewpoint}
    deactivate A4

    Crew->>A5: Task 5: 生成摘要<br/>(context: Task1-4结果)
    activate A5
    A5->>A5: 综合所有信息<br/>生成会议摘要
    A5-->>Crew: 返回会议摘要文本
    deactivate A5

    Crew-->>API: 4. 返回完整分析结果

    API->>DB: 5. 保存所有数据
    DB->>DB: 保存章节
    DB->>DB: 保存决策
    DB->>DB: 保存待办
    DB->>DB: 保存说话人观点
    DB->>DB: 更新会议状态

    API-->>User: 6. 分析完成通知
```

## 3. Agent协作关系图

```mermaid
graph LR
    subgraph "输入数据"
        Input1[会议转录文本<br/>Transcript]
        Input2[说话人片段<br/>Speaker Segments]
        Input3[会议模板类型<br/>Template Type]
    end

    subgraph "Agent协作层"
        A1[🔍 会议结构分析师<br/>识别章节和议程]
        A2[⚠️ 决策识别专家<br/>提取关键决策]
        A3[✅ 任务提取专家<br/>识别待办事项]
        A4[👥 说话人观点分析师<br/>提炼核心观点]
        A5[📝 会议总结专家<br/>生成整体摘要]
    end

    subgraph "输出结果"
        Out1[章节列表<br/>Chapters]
        Out2[决策列表<br/>Decisions]
        Out3[待办列表<br/>Todos]
        Out4[观点映射<br/>Viewpoints]
        Out5[会议摘要<br/>Summary]
    end

    Input1 --> A1
    Input1 --> A2
    Input1 --> A3
    Input2 --> A4
    Input1 --> A5
    Input3 --> A1

    A1 -.context.-> A2
    A1 -.context.-> A3
    A2 -.context.-> A5
    A3 -.context.-> A5
    A4 -.context.-> A5

    A1 --> Out1
    A2 --> Out2
    A3 --> Out3
    A4 --> Out4
    A5 --> Out5

    style A1 fill:#e1f5ff,stroke:#0288d1,stroke-width:2px
    style A2 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style A3 fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    style A4 fill:#e8f5e9,stroke:#388e3c,stroke-width:2px
    style A5 fill:#fce4ec,stroke:#c2185b,stroke-width:2px
```

## 4. 数据处理流程图

```mermaid
flowchart TD
    Start([用户上传音频]) --> Upload{选择会议类型}

    Upload -->|标准会议| Type1[template_type=standard]
    Upload -->|总行汇报会| Type2[template_type=report]
    Upload -->|周例会| Type3[template_type=weekly]
    Upload -->|决策会议| Type4[template_type=decision]

    Type1 --> SaveFile[保存音频文件]
    Type2 --> SaveFile
    Type3 --> SaveFile
    Type4 --> SaveFile

    SaveFile --> CreateRecord[创建会议记录<br/>status=processing]

    CreateRecord --> Transcribe[Whisper转录<br/>音频→文本]

    Transcribe --> Diarize[pyannote识别<br/>说话人分离]

    Diarize --> Merge[合并转录与说话人<br/>生成带标记的片段]

    Merge --> CrewAnalysis{启动Crew分析}

    CrewAnalysis --> Task1[Task 1<br/>结构分析师<br/>章节划分]

    Task1 --> Task2[Task 2<br/>决策专家<br/>提取决策]
    Task1 --> Task3[Task 3<br/>任务专家<br/>提取待办]

    Task2 --> Task5[Task 5<br/>总结专家<br/>生成摘要]
    Task3 --> Task5

    CrewAnalysis --> Task4[Task 4<br/>观点分析师<br/>分析发言]

    Task4 --> Task5

    Task5 --> SaveResults{保存结果}

    SaveResults --> SaveChapters[保存章节<br/>MeetingChapter]
    SaveResults --> SaveDecisions[保存决策<br/>KeyDecision]
    SaveResults --> SaveTodos[保存待办<br/>TodoItem]
    SaveResults --> SaveSpeakers[保存说话人<br/>Speaker]
    SaveResults --> UpdateMeeting[更新会议<br/>status=completed]

    UpdateMeeting --> End([分析完成])

    style Start fill:#4caf50,color:#fff
    style End fill:#4caf50,color:#fff
    style Task1 fill:#e1f5ff
    style Task2 fill:#fff3e0
    style Task3 fill:#f3e5f5
    style Task4 fill:#e8f5e9
    style Task5 fill:#fce4ec
```

## 5. Agent详细功能说明

### 🔍 Agent 1: 会议结构分析师 (Structure Analyst)

**职责**:
- 分析会议转录文本的逻辑结构
- 识别议程阶段和话题转换
- 将会议划分为3-6个逻辑章节

**输入**:
- 完整转录文本
- 会议模板类型

**输出**:
```json
[
  {
    "chapter_title": "开场致辞",
    "start_time": 0,
    "end_time": 300,
    "summary": "会议主持人介绍本次会议的背景和目标"
  }
]
```

**算法策略**:
- 基于话题语义相似度聚类
- 检测说话人转换和议题关键词
- 估算时间戳（基于文本长度）

---

### ⚠️ Agent 2: 决策识别专家 (Decision Expert)

**职责**:
- 从讨论中识别明确的决策点
- 分类决策类型和优先级
- 记录参与决策的人员

**输入**:
- 完整转录文本
- 章节信息（作为上下文）

**输出**:
```json
[
  {
    "decision_content": "批准XX项目预算增加500万",
    "decision_type": "approval",
    "participants": ["张总", "李主任"],
    "priority": "high",
    "timestamp": 1200
  }
]
```

**识别规则**:
- 关键词：决定、批准、同意、确定、通过
- 决策类型：strategic（战略）、operational（运营）、approval（审批）
- 优先级：基于金额、时间紧迫度、影响范围

---

### ✅ Agent 3: 任务提取专家 (Task Extractor)

**职责**:
- 识别明确和隐含的待办任务
- 提取负责人和截止时间
- 评估任务优先级

**输入**:
- 完整转录文本
- 章节信息（作为上下文）

**输出**:
```json
[
  {
    "task_content": "在本月底前完成市场调研报告",
    "assignee": "市场部王经理",
    "deadline": "2024-01-31",
    "priority": "high",
    "timestamp": 1800
  }
]
```

**识别模式**:
- 明确分配：某某负责、请某某完成
- 隐含任务：需要、要、应该、计划
- 时间识别：本周、下月、XX日前

---

### 👥 Agent 4: 说话人观点分析师 (Speaker Analyst)

**职责**:
- 分析每位说话人的所有发言
- 提炼核心观点和关注重点
- 总结立场和态度

**输入**:
- 按说话人分组的发言片段

**输出**:
```json
{
  "SPEAKER_00": "张总强调要加快数字化转型，建议引入AI技术提升效率，同时关注数据安全问题",
  "SPEAKER_01": "李主任汇报了Q3业绩，指出存在的三个主要挑战，提出了改进方案"
}
```

**分析要点**:
- 主要观点（what）
- 关注重点（concern）
- 提出的建议（suggestion）

---

### 📝 Agent 5: 会议总结专家 (Summary Expert)

**职责**:
- 综合前4个Agent的分析结果
- 生成简洁明了的会议摘要
- 突出会议核心成果

**输入**:
- 所有前置Agent的输出结果

**输出**:
```text
本次总行季度汇报会共有5个部门进行了工作汇报。
会议主要讨论了数字化转型、市场拓展和风险管控三大议题。
达成了3项重要决策，包括批准AI项目预算、启动新市场调研等。
会后需要跟进8项具体任务，责任已明确到各部门负责人。
```

**生成策略**:
- 200-300字简洁摘要
- 突出核心议题和成果
- 适合未参会人员快速了解

---

## 6. 技术栈总结

```mermaid
graph TB
    subgraph "AI技术栈"
        CrewAI[CrewAI<br/>多Agent协作框架]
        LangChain[LangChain<br/>LLM应用框架]
        GPT4[GPT-4 Turbo<br/>大语言模型]
        Whisper[Whisper API<br/>语音识别]
        Pyannote[pyannote.audio<br/>说话人识别]
    end

    subgraph "后端技术栈"
        FastAPI[FastAPI<br/>Web框架]
        SQLAlchemy[SQLAlchemy<br/>ORM框架]
        Pydantic[Pydantic<br/>数据验证]
        PostgreSQL[(PostgreSQL<br/>数据库)]
    end

    subgraph "前端技术栈"
        React[React 18<br/>UI框架]
        AntD[Ant Design 5<br/>组件库]
        Axios[Axios<br/>HTTP客户端]
        Router[React Router 6<br/>路由管理]
    end

    CrewAI --> GPT4
    CrewAI --> LangChain
    FastAPI --> CrewAI
    FastAPI --> Whisper
    FastAPI --> Pyannote
    FastAPI --> SQLAlchemy
    SQLAlchemy --> PostgreSQL
    React --> AntD
    React --> Axios
    Axios --> FastAPI

    style CrewAI fill:#ff6b6b,color:#fff
    style GPT4 fill:#4ecdc4,color:#fff
    style FastAPI fill:#95e1d3,color:#000
    style React fill:#61dafb,color:#000
```

---

## 7. 性能优化策略

### Agent并行执行优化

```mermaid
graph TD
    Start[开始分析] --> Sequential{是否可并行}

    Sequential -->|是| Parallel[并行执行]
    Sequential -->|否| Series[顺序执行]

    Parallel --> Group1[Agent 2: 决策专家]
    Parallel --> Group2[Agent 3: 任务专家]
    Parallel --> Group3[Agent 4: 观点分析师]

    Group1 --> Wait[等待所有并行任务完成]
    Group2 --> Wait
    Group3 --> Wait

    Wait --> Final[Agent 5: 总结专家]

    Series --> Agent1[Agent 1: 结构分析师]
    Agent1 --> Context[传递上下文]
    Context --> Parallel

    Final --> End[分析完成]

    style Parallel fill:#4caf50,color:#fff
    style Series fill:#ff9800,color:#fff
```

### 关键优化点

1. **Agent 2, 3, 4 可并行执行**
   - 互不依赖，可同时调用GPT-4
   - 减少总处理时间约40%

2. **Agent 1 必须先执行**
   - 其他Agent需要章节信息作为上下文
   - 提供时间戳参考

3. **Agent 5 最后执行**
   - 需要综合前面所有结果
   - 生成最终摘要

4. **缓存策略**
   - GPT-4响应缓存
   - 转录结果缓存
   - 减少重复调用

---

## 8. 使用建议

### 适用场景
✅ 总行层级汇报会议（30-180分钟）
✅ 多部门协调会议
✅ 决策型会议
✅ 周期性例会

### 不适用场景
❌ 非正式讨论
❌ 一对一谈话
❌ 实时会议记录（当前版本）

### 最佳实践
1. 会议开始时每人自我介绍
2. 重要决策时明确表述"我们决定..."
3. 任务分配时明确责任人和时间
4. 录音质量保证（安静环境、清晰发音）

---

**文档版本**: v2.0
**创建日期**: 2025-11-17
**架构设计**: 基于CrewAI多Agent协作框架
