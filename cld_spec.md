# 📜 CLD v9

> **最终权威文档 · AI 与用户的共同誓言**
> **定位**：唯一权威语义源 · 零妥协执行契约 · 人机交互契约语言
> **哲学**：结构即语义，路径即归属，引用即图谱，执行即存在
> **格式**：声明式 · 人类可写 · AI 可执行 · 100% 可验证 · 零模糊 · 零隐式 · 零外部依赖
> **核心原则**：AI 精确理解执行回应 && 用户精确表达 && 可维护可拓展
> **核心优先级**：语义内核优先，语法为语义服务，绝不携带历史包袱，0向后兼容

---
> **`cl` 是 CLD 引擎保留的 universe_id，用于承载所有原生系统语义。用户不得定义任何 `xxx::cl::...` 路径的原语。所有系统枚举、Schema、Action 模板均位于 `cl::core` 域下。**
---

## 🧱 一、10 大基石原语（World Existence Primitives）

> 构成世界存在性的最小完备集合。缺一则世界无法运行。语义永不变更，哲学向后兼容，形式向前纯净。

---

### 0. `@World` —— 世界执行契约（唯一入口）

> **`@World` 是一个具名的、可多实例的语义容器原语，代表一个自治因果域（Autonomous Causal Domain）。**
> CLD **允许多个 `@World` 实例并存**，每个实例定义一个独立世界。
> **执行契约语义**：“唯一入口” 指 **每个世界实例的执行上下文以其 `@World` 为根**

```clj
@World[world::<universe_id>] {
  origin: ref(@Origin[origin::<universe_id>::<origin_name>]),
  timeline: ref(@Timeline[timeline::<universe_id>::<timeline_name>]),
  entities: [ ref(@Niche[entity::<universe_id>::<category>::<niche_name>]), ... ],
  constraints?: [ ref(@Constraint[constraint::<universe_id>::<domain>::<name>]), ... ],
  rules: [ ref(@Rule[rule::<universe_id>::<domain>::<rule_name>]), ... ],
  scheduler?: ref(@Scheduler[scheduler::<universe_id>::<scheduler_name>])
}
```

> 将“全局状态”迁移至 `entity::<universe_id>::system::*` + `slot::<universe_id>::system::*`

---

### 1. `@Origin` —— 世界初始契约（World Genesis Contract）

```clj
@Enum[enum::cl::world::CausalTension] {
  variants: [ determinism_vs_contingency, ...]
}
@Enum[enum::cl::world::TemporalTopology] {
  variants: [ linear, cyclic, branching, fragmented, manifold, ... ]
}
;; 每个张力条目
@Schema[schema::cl::world::CausalTensionSpec] {
  tension: ref(@Enum[enum::cl::world::CausalTension]),
  weight: integer  ;; ∈ [1, 100]
}

@Origin[origin::<universe_id>::<origin_name>] {
  causal_epoch: integer,    ;; MUST be ≥ 0. Defines the logical zero-point of causal numbering.
                            ;; Negative values are ILLEGAL and violate world genesis semantics.
  causal_tensions: [                ;; 结构字面量 CausalTensionSpec 列表, 总和权重为固定值 100
    { tension: ref(@Enum[enum::cl::world::CausalTension]::<variant>), weight: integer },
    ...
  ],
  temporal_topology: ref(@Enum[enum::cl::world::TemporalTopology]::<variant>),
  immutable_anchors: [
    ref(@CoreEvent[core_event::<universe_id>::<domain>::<core_event_name>]),
    ...
  ]
}
```

---

### 2. `@Timeline` —— 时间拓扑

```clj
@Enum[enum::cl::timeline::TimelineType] { variants: [ causal_manifold, event_graph, ... ] }
@Enum[enum::cl::timeline::TimelineFeature] { variants: [ branching, converging, local_loop, time_jump, ... ] }
@Timeline[timeline::<universe_id>::<timeline_name>] {
  type: ref(@Enum[enum::cl::timeline::TimelineType]::<variant>),
  features: [ ref(@Enum[enum::cl::timeline::TimelineFeature]::<variant>), ... ]
}
```

---

### 3. `@Event` —— 最小变迁（可执行影响）

```clj
@Event[event::<universe_id>::<domain>::<event_name>]{
  description: string,
  emotional_peak: integer,     ;; ∈ [0, 100]
  effects?: [ ref(@Effect[effect::<universe_id>::<domain>::<effect_name>]), ... ]
}
```

---

### 4. `@CoreEvent` —— 不可篡改基石

```clj
@CoreEvent[core_event::<universe_id>::<domain>::<core_event_name>] {
  description: string,
  emotional_peak: integer,     ;; ∈ [0, 100]
  effects?: [ ref(@Effect[effect::<universe_id>::<domain>::<effect_name>]), ... ]
}
```

---

### 5. `@Niche` —— 行为体状态逻辑容器

```clj
@Niche[entity::<universe_id>::<category>::<niche_name>] {
  owns_slots?: [ ref(@StateSlot[slot::<universe_id>::<domain>::<slot_name>]), ... ],
  constraints?: [ ref(@Constraint[constraint::<universe_id>::<domain>::<name>]), ... ],
  transition_rules?: [
    {
      when: ref(@Predicate[Predicate::<universe_id>::<domain>::<Predicate_name>]),
      then: ref(@Action[action::<universe_id>::<domain>::<action_name>])
    },
    ...
  ],
  behavior_emergence?: ref(>>BehaviorEmitter[emit::<universe_id>::<domain>::<emitter_name>]),
  information_horizon?: ref(@Horizon[horizon::<universe_id>::<horizon_name>]),
  lifecycle?: {
    activation_anchor?: ref(@CoreEvent[core_event::<universe_id>::<domain>::<name>]),
    deactivation_anchor?: ref(@CoreEvent[core_event::<universe_id>::<domain>::<name>]),
    destruction_anchor?: ref(@CoreEvent[core_event::<universe_id>::<domain>::<name>]),
    valid_eras?: [ ref(@Era[era::<universe_id>::<era_name>]), ... ],
    era_behaviors?: [
      {
        era: ref(@Era[era::<universe_id>::<era_name>]),
        behavior_emergence: ref(>>BehaviorEmitter[emit::<universe_id>::<domain>::<emitter_name>]),
        constraints?: [
          ref(@Constraint[constraint::<universe_id>::<domain>::<name>]),
          ...
        ]
      },
      ...
    ]
  }
}
```

> ✅ **推荐分类**：
>
> - `entity::<universe_id>::system::core`（系统代理）
> - `entity::<universe_id>::citizen::alice`（NPC）
> - `entity::<universe_id>::faction::guild_of_wires`（组织）

#### **语义契约规则**

##### 1. **`valid_eras` 字段语义**

- **类型**：可选的非空引用列表 `List<ref(@Era[...])>`，或显式空列表 `[]`。
- **若 `lifecycle.valid_eras` 未声明** → 等价于 `valid_eras: []`（永久有效）。
- **若 `valid_eras: []`** → 该 `@Niche` **跨越所有纪元**，不受任何 `@Era` 时间边界限制。
- **若 `valid_eras: [era::A, era::B, ...]`** → 仅在所列 `@Era` 的时间区间内逻辑有效。
- **纪元时间区间定义**：
  - `@Era` 必须有 `start_anchor`（加载器强制校验）。
  - 若无 `end_anchor`，则区间为 `[start_anchor, +∞)`。
  - 区间由 `@CoreEvent` 锚定，确保因果可比性。

##### 2. **生命周期事件与纪元协同规则**

- **激活抑制**：若 `activation_anchor` 发生在 **所有 `valid_eras` 覆盖区间之外**，则激活被抑制（不触发状态初始化）。
- **销毁优先**：`destruction_anchor` 触发即终结 `@Niche`，**无视纪元有效性**。
- **去激活**：`deactivation_anchor` 仅在 `@Niche` 处于激活态且位于有效纪元内时生效。

##### 3. **`era_behaviors` 字段语义（显式覆盖机制）**

- **非必需**：仅当某纪元需**差异化行为或约束**时才声明。
- **行为覆盖**：若当前纪元 `E` 匹配 `era_behaviors[i].era`，则：
  - 使用 `era_behaviors[i].behavior_emergence` 替代顶层 `behavior_emergence`；
  - 合并 `era_behaviors[i].constraints` 与顶层 `constraints`（交集或叠加，由引擎策略决定，但必须可配置）。
- **无匹配时**：回退至顶层 `behavior_emergence` 与 `constraints`。

> 所有行为差异必须通过 **结构存在性** 表达：
>
> - 有 `era_behaviors` → 自适应；
> - 无 `era_behaviors` 但有 `valid_eras` → 连续或间歇（由锚点决定）；
> - 无 `lifecycle` 或 `valid_eras: []` → 永久连续。

##### 4. **加载器验证规则**

- 所有 `ref(@Era[...])` 必须解析到已声明的 `@Era` 实体。
- 所有被引用的 `@Era` 必须定义 `start_anchor`。
- 若 `era_behaviors` 中的 `era` 未出现在 `valid_eras` 中 → **加载错误**（行为映射必须受限于有效纪元）。
- 引擎必须支持 **纪元时序可判定性分析**，若检测到 `@Era` 时间循环或不可比事件 → 报错。

##### 5. **哲学对齐**

- ✅ **结构即语义**：是否自适应？看 `era_behaviors` 是否存在。是否跨纪元？看 `valid_eras` 列表内容。
- ✅ **零隐式**：无 `mode` 枚举，无默认行为猜测，无“隐式延续”。
- ✅ **引用即图谱**：`valid_eras` 和 `era_behaviors.era` 构成生命周期子图，可被遍历、分析、可视化。
- ✅ **执行即存在**：运行时调度器必须在每次状态变更前，验证当前因果时刻 ∈ ⋃(era.time_interval for era in valid_eras)。

---

#### **合规示例**

```clj
@Era[era::mythos::age_of_dragons] {
  start_anchor: ref(@CoreEvent[evt::mythos::first_dragon_awakens]),
  end_anchor: ref(@CoreEvent[evt::mythos::dragon_king_falls])
}

@Era[era::mythos::age_of_mortals] {
  start_anchor: ref(@CoreEvent[evt::mythos::dragon_king_falls])
}

@Niche[entity::mythos::being::elder_dragon] {
  owns_slots: [ ref(@StateSlot[slot::mythos::dragon::scales]) ],
  lifecycle: {
    activation_anchor: ref(@CoreEvent[evt::mythos::first_dragon_awakens]),
    destruction_anchor: ref(@CoreEvent[evt::mythos::dragon_king_falls]),
    valid_eras: [
      ref(@Era[era::mythos::age_of_dragons])
    ]
  }
}

@Niche[entity::mythos::law::balance] {
  behavior_emergence: ref(>>BehaviorEmitter[emit::mythos::law::neutral_balance]),
  lifecycle: {
    valid_eras: [],
    era_behaviors: [
      {
        era: ref(@Era[era::mythos::age_of_dragons]),
        behavior_emergence: ref(>>BehaviorEmitter[emit::mythos::law::dragon_balance]),
        constraints: [ ref(@Constraint[cons::mythos::law::no_mortal_interference]) ]
      },
      {
        era: ref(@Era[era::mythos::age_of_mortals]),
        behavior_emergence: ref(>>BehaviorEmitter[emit::mythos::law::mortal_balance])
      }
    ]
  }
}
```

---

### 6. `@Rule` —— 条件触发器

```clj
@Rule[rule::<universe_id>::<domain>::<rule_name>] {
  when: ref(@Predicate[Predicate::<universe_id>::<domain>::<Predicate_name>]),
  then_emit: ref(@Event[event::<universe_id>::<domain>::<event_name>])
           | [ ref(@Event[event::<universe_id>::<domain>::<event_name>]), ... ]
}
```

---

### 7. `@Scheduler` 调度器

> 调度器定义世界状态推进的节奏、行为与一致性契约。
> 所有结构字段（如 `tick_spec`, `tick_behavior`, `consistency_contract`）必须使用结构字面量，禁止 `ref(@Schema[...])`。

```clj
@Enum[enum::cl::time::Unit] {
  variants: [ nanosecond, microsecond, millisecond, second, minute, hour, day ]
}
@Enum[enum::cl::scheduler::Mode] {
  variants: [ tick_based, event_driven, hybrid ]
}
@Enum[enum::cl::scheduler::ExecutionOrder] {
  variants: [ sequential, parallel, dependency_graph ]
}
@Enum[enum::cl::scheduler::FailurePolicy] {
  variants: [ halt_on_first, continue_all, rollback ]
}
;; ── 0. 时间间隔 ───────────────────────────────
@Schema[schema::cl::time::TimeInterval] {
  unit: ref(@Enum[enum::cl::time::Unit]),
  value: integer  ;; ≥1
}
;; ── 1. 节奏规范 ───────────────────────────────
@Schema[schema::cl::scheduler::TickSpec] {
  interval: ref(@Schema[schema::cl::time::TimeInterval]),
  phase_offset?: integer,
  max_jitter?: ref(@Schema[schema::cl::time::TimeInterval])
}
;; ── 2. 执行行为 ───────────────────────────────
@Schema[schema::cl::scheduler::TickBehavior] {
  effects: [ ref(@Effect[effect::<universe_id>::<domain>::<effect_name>]) ],
  execution_order: ref(@Enum[enum::cl::scheduler::ExecutionOrder]),
  failure_policy: ref(@Enum[enum::cl::scheduler::FailurePolicy])
}
;; ── 3. 一致性契约 ─────────────────────────────
@Schema[schema::cl::scheduler::ConsistencyContract] {
  enforce_timeline: boolean,
  enforce_anchors: boolean,
  allow_retcon?: boolean
}

;; ── 调度器定义 ───────────────────────────────
@Scheduler[scheduler::<universe_id>::<scheduler_name>] {
  mode: ref(@Enum[enum::cl::scheduler::Mode]::<variant>),

  ;; 仅 tick_based / hybrid
  tick_spec?: {
    interval: {
      unit: ref(@Enum[enum::cl::time::Unit]::<unit>),
      value: integer  ;; ≥1
    },
    phase_offset?: integer,  ;; ≥ 0, optional tick offset from epoch
    max_jitter?: {
        unit: ref(@Enum[enum::cl::time::Unit]::<unit>),
        value: integer  ;; ≥1
    }
  },

  ;; 仅 tick_based / hybrid
  tick_behavior?: {
    effects: [ ref(@Effect[effect::<universe_id>::<domain>::<effect_name>]) ],
    execution_order: ref(@Enum[enum::cl::scheduler::ExecutionOrder]::<variant>),
    failure_policy: ref(@Enum[enum::cl::scheduler::FailurePolicy]::<variant>)
  },

  ;; 仅 event_driven / hybrid
  event_triggers?: [
    {
      event: ref(@Event[event::<universe_id>::<domain>::<name>]),
      effects: [ ref(@Effect[effect::<universe_id>::<domain>::<name>]), ... ],
      execution_order: ref(@Enum[enum::cl::scheduler::ExecutionOrder]::<variant>),
      failure_policy: ref(@Enum[enum::cl::scheduler::FailurePolicy]::<variant>),
      throttle?: {
        window_ticks: integer,  ;; ≥1
        max_executions: integer ;; ≥1
      }
    },
    ...
  ],

  ;; 所有模式必需
  consistency_contract: {
    enforce_timeline: boolean,
    enforce_anchors: boolean,
    allow_retcon?: boolean
  }

  ;; 调度器生命周期控制
  lifecycle?: {
    activation_anchor?: ref(@CoreEvent[core_event::<universe_id>::<domain>::<name>]),
    deactivation_anchor?: ref(@CoreEvent[core_event::<universe_id>::<domain>::<name>]),
    destruction_anchor?: ref(@CoreEvent[core_event::<universe_id>::<domain>::<name>]),
    valid_eras?: [ ref(@Era[era::<universe_id>::<era_name>]), ... ]
  }
}
```

---

### 8. `@Era` —— 历史语义层

```clj
@Enum[enum::cl::era::EraTag] {
  variants: [ technological, ... ]
}
@Era[era::<universe_id>::<era_name>] {
  start_anchor: ref(@CoreEvent[core_event::<universe_id>::<domain>::<core_event_name>]),
  end_anchor?: ref(@CoreEvent[core_event::<universe_id>::<domain>::<core_event_name>]),
  tags?: [ ref(@Enum[enum::cl::era::EraTag]::<variant>) ],
  description?: string
}
```

---

### 9. `>>BehaviorEmitter` —— 状态表达接口

> 提示工程接口

```clj
@Enum[enum::cl::behavior::Role] {
  variants: [ system, user, assistant, tool, critic, planner, observer ]
}
@Enum[enum::cl::behavior::StructuredFormat] {
  variants: [ json, yaml, toml, xml, markdown, edn, protobuf_text, ... ]
}
@Enum[enum::cl::behavior::ModelClass] {
  variants: [ text_gen, reasoning, multimodal, agent, ... ]
}
@Enum[enum::cl::behavior::KnowledgeInjectionMode] {
  variants: [ full_horizon, memory_only, none, context_window_only, time_window_only, ... ]
}
;; ── instruction types ───────────────────────────────
@Schema[schema::cl::behavior::instruction::InlineText] {
  content: string
}
@Schema[schema::cl::behavior::instruction::Structured] {
  format: ref(@Enum[enum::cl::behavior::StructuredFormat]::<variant>),
  payload_schema: ref(@Schema[schema::<universe_id>::<domain>::<name>])
  payload: <literal_matching_schema>
}
@StateSlot[slot::<universe_id>::behavior::instruction::<name>] {
  schema: ref(@Schema[schema::cl::behavior::instruction::InlineText])
    | ref(@Schema[schema::cl::behavior::instruction::Structured]),
  init: <literal_matching_schema>,
  metadata: {
    type: ref(@Enum[enum::cl::slot::SlotType]::<variant>),
    access: ref(@Enum[enum::cl::slot::SlotAccessMode]::<variant>),
    decay?: {
      mode: ref(@Enum[enum::cl::slot::SlotDecayMode]::<variant>),
      rate?: float
    },
    unit?: string,
    description?: string
  }
}
;; ── output_format types ───────────────────────────────
@Schema[schema::cl::behavior::output_format::NaturalLanguage] {
  style_hint: string
}
@Schema[schema::cl::behavior::output_format::Structured] {
  format: ref(@Enum[enum::cl::behavior::StructuredFormat]::<variant>),
  payload_schema: ref(@Schema[schema::<universe_id>::<domain>::<name>])
  payload: <literal_matching_schema>
}
@StateSlot[slot::<universe_id>::behavior::output_format::<name>] {
  schema: ref(@Schema[schema::cl::behavior::output_format::NaturalLanguage])
    | ref(@Schema[schema::cl::behavior::output_format::Structured]),
  init: <literal_matching_schema>,
  metadata: {
    type: ref(@Enum[enum::cl::slot::SlotType]::<variant>),
    access: ref(@Enum[enum::cl::slot::SlotAccessMode]::<variant>),
    decay?: {
      mode: ref(@Enum[enum::cl::slot::SlotDecayMode]::<variant>),
      rate?: float
    },
    unit?: string,
    description?: string
  }
}
;; ── 系统级基础 Schema（由 cl 宇宙提供，用户不可覆盖） ───────────────
@Schema[schema::cl::behavior::ImplementationSpec] {
  model_class: ref(@Enum[enum::cl::behavior::ModelClass]),
  model_name: string,
  temperature: float
}
;; ── 用户可定义的 StateSlot（引用上述 Schema） ───────────────────────
@StateSlot[slot::<universe_id>::behavior::implementation::<name>] {
  schema: ref(@Schema[schema::cl::behavior::ImplementationSpec])
        | ref(@Schema[schema::<universe_id>::behavior::ImplementationSpec]),
  init: <literal_matching_schema>,
  metadata: {
    type: ref(@Enum[enum::cl::slot::SlotType]::structured),
    access: ref(@Enum[enum::cl::slot::SlotAccessMode]::read_only),
    description?: string
  }
}
;; ── 行为发射器（统一引用式结构） ───────────────────────────────────
>>BehaviorEmitter[emit::<universe_id>::<domain>::<emitter_name>] {
  prompt_engine: {
    role: ref(@Enum[enum::cl::behavior::Role]::<variant>)
        | ref(@Enum[enum::<universe_id>::behavior::Role]::<variant>),
    instruction: ref(@StateSlot[slot::<universe_id>::behavior::instruction::<name>]),
    output_format: ref(@StateSlot[slot::<universe_id>::behavior::output_format::<name>]),
    knowledge_injection: ref(@Enum[enum::cl::behavior::KnowledgeInjectionMode]::<variant>)
  },
  implementation: ref(@StateSlot[slot::<universe_id>::behavior::implementation::<name>])
}
```

> 推荐优先使用 enum::cl::behavior::Role 中的标准角色；仅当标准角色无法表达领域语义时，才定义并使用 enum::<universe_id>::behavior::Role。
> 加载器验证规则：
>
> - 所有 >>BehaviorEmitter 中的 ref(@StateSlot[...]) 的 .schema 必须匹配预定义 output_format / instruction schema
> - payload_schema 必须指向有效 @Schema 实体

---

### 10. `@Horizon` —— 信息视界

```clj
@Enum[enum::cl::horizon::HorizonType] { variants: [ causal_past, era_limited, omniscient, ... ] }
@Enum[enum::cl::horizon::PropheticScope] { variants: [ vision, dream, oracle, echo, ... ] }
@Enum[enum::cl::horizon::AmbiguityLevel] { variants: [ low, medium, high, ... ] }

@Horizon[horizon::<universe_id>::<horizon_name>] {
  type: ref(@Enum[enum::cl::horizon::HorizonType]::<variant>),
  prophetic_ability?: {
    scope: ref(@Enum[enum::cl::horizon::PropheticScope]::<variant>),
    cost: ref(@Effect[effect::<universe_id>::<domain>::<effect_name>]),
    ambiguity_level: ref(@Enum[enum::cl::horizon::AmbiguityLevel]::<variant>)
  }
}
```

---

## 🛠️ 二、10 大增强原语（Semantic Precision Extensions）

> 所有增强原语必须通过 `ref(...)` 引用，不得内联语义逻辑。

---

### 1. `@Enum` —— 枚举类型（关系/状态语义锚点）

```clj
@Enum[enum::<universe_id>::<domain>::<enum_name>] {
  variants: [ identifier, ... ]
}
```

#### 预定义枚举（系统级）

```clj
@Enum[enum::cl::core::ComparisonOp] { variants: [ eq, ne, lt, le, gt, ge, in, not_in, ... ] }
@Enum[enum::cl::core::LogicalOp] { variants: [ and, or, ... ] }
@Enum[enum::cl::core::StateOperation] { variants: [ set, add, subtract, multiply, divide, append, ... ] }
```

---

### 2. `@Action` —— 可执行行为原子

```clj
@Action[action::cl::system::EmitEvent] {
  event: ref(@Event[event::<universe_id>::<domain>::<event_name>])
}

@Action[action::cl::system::UpdateNicheSlot] {
  target_niche: ref(@Niche[entity::<universe_id>::<category>::<niche_name>]),
  slot: ref(@StateSlot[slot::<universe_id>::<domain>::<slot_name>]),
  operation: ref(@Enum[enum::cl::core::StateOperation]::<op>),
  operand: <strict_literal_matching_slot_schema>
}

@Action[action::cl::system::CallEffect] {
  effect: ref(@Effect[effect::<universe_id>::<domain>::<effect_name>])
}
```

> ⚠️ 注意：以上三者为 系统级标准动作模板说明，实际使用时 必须创建具名实例（见下文）。

#### 示例：业务语义动作（具名实例，无泛型）

```clj
@Action[action::example_world::spirit::grant_tenth_ring_to_tang_san] {
  target_niche: ref(@Niche[entity::example_world::citizen::tang_san]),
  slot: ref(@StateSlot[slot::example_world::spirit::soul_rings]),
  operation: ref(@Enum[enum::cl::core::StateOperation]::append),
  operand: {
    color: "红",
    ability: "海神之光",
    source: "深海魔鲸王"
  }
}

@Action[action::example_world::spirit::emit_sea_god_ascension_event] {
  event: ref(@Event[event::example_world::divine::sea_god_ascension])
}
```

---

### 3. `@Effect` —— 可执行副作用

> **定位**：副作用的行为序列容器 · 无作用域 · 无隐式目标 · **执行即存在**
> **哲学对齐**：
>
> - **零隐式**：所有行为必须通过 `ref(@Action)` 显式引用，禁止内联逻辑；
> - **引用即图谱**：`actions` 序列构成因果链，归属由引用路径决定；
> - **执行即验证**：若声明 `guard`，则执行前必须完成契约验证。

#### ✅ 语法定义

```clj
@Effect[effect::<universe_id>::<domain>::<name>] {
  guard?: ref(@Constraint[constraint::<universe_id>::<domain>::<name>]),
  actions: [ ref(@Action[action::<universe_id>::<domain>::<action_name>]), ... ]
}
```

> ⚠️ `@Effect` 是**行为容器**，不是**值容器**，也不是**条件容器**。

#### 🔒 语义规则（加载器强制验证）

|规则|说明|
|---|---|
|**`guard` 类型安全**|仅可引用 `@Constraint`，不可为 `@Predicate`、`@ValueExpr` 或字面量|
|**`actions` 有序且非空**|引擎按声明顺序执行；长度 ≥ 1|
|**无隐式上下文**|`@Action` 必须在其自身定义中完全指定目标（如 `target_niche`、`slot`）|
|**无作用域继承**|`@Effect` 不继承任何父级上下文（如 `@Rule` 所属的 `@World`）|
|**执行触发点**|仅由 `@Rule.then` 触发（当前）；未来可扩展为嵌套 `@Effect`|
|**引用解析**|所有 `ref(...)` 必须在加载时解析到已声明的顶级实体|
|**禁止副作用内联**|不得在 `@Effect` 内定义 `@Action` 或表达式逻辑|

#### 📚 Effect合规示例

```clj
@Effect[effect::example_world::spirit::complete_sea_god_trial] {
  guard: ref(@Constraint[constraint::example_world::spirit::tang_san_has_nine_rings]),
  actions: [
    ref(@Action[action::example_world::spirit::grant_tenth_ring_to_tang_san]),
    ref(@Action[action::example_world::spirit::emit_sea_god_ascension_event])
  ]
}
```

---

### 4. `@Schema` —— 纯数据结构声明

```clj
@Schema[schema::<universe_id>::<domain>::<name>] {
  <field_name>: <type_spec>,
  ...
}
```

其中 `<type_spec>` 必须为以下之一：

| 类型 | 说明 |
|------|------|
| `string` | UTF-8 字符串 |
| `integer` | 有符号整数（64 位） |
| `float` | IEEE 754 双精度浮点数 |
| `boolean` | `true` / `false` |
| `ref(@Enum[enum::<universe_id>::<domain>::<enum_name>])` | 枚举引用 |
| `ref(@Schema[schema::<universe_id>::<domain>::<schema_name>])` | 嵌套结构引用 |
| `[ <type_spec> ]` | 同质列表（元素类型一致） |

---

### 5. `@Relation` —— 实体间语义关系

> **定位**：描述两个具名实例之间的**有向、带权重、可衰减**的语义关系。
> **哲学对齐**：
>
> - **路径即身份**：`source`/`target` 通过完整路径引用，无匿名实体；
> - **关系即数据**：`strength`、`decay`、`metadata` 均为可查询状态；
> - **元关系支持**：允许 `@Relation` 作为 `source` 或 `target`。

```clj
@Relation[relation::<universe_id>::<domain>::<relation_name>] {
  type: ref(@Enum[enum::<universe_id>::<domain>::<enum_name>]::<variant>),
  source: ref(<any_valid_named_path>),
  target: ref(<any_valid_named_path>),
  strength: float,             ;; ∈ [0, 1]
  metadata?: ref(@Schema[schema::<universe_id>::<domain>::<metadata_schema>]),
  decay?: {
    rate: float,               ;; ≥ 0
    on_event: ref(@Event[event::<universe_id>::<domain>::<event_name>])
  }
}
```

#### 🔒 合法引用目标（`source` / `target`）

`ref(...)` 必须指向以下**具名顶级实体**之一：

|原语类型|路径前缀示例|说明|
|---|---|---|
|`@World`|`world::<universe_id>::<name>`|宇宙/世界实例|
|`@Niche`|`entity::<universe_id>::<category>::<name>`|实体实例（主要用例）|
|`@Event`|`event::<universe_id>::<domain>::<name>`|用户定义事件|
|`@CoreEvent`|`core_event::<universe_id>::<domain>::<name>`|系统事件|
|`@Era`|`era::<universe_id>::<name>`|时代/纪元|
|`@Relation`|`relation::<universe_id>::<domain>::<name>`|支持元关系（relation about relation）|

#### 🔒 加载器验证规则

1. **`type` 必须为枚举引用**，且该枚举已声明；
2. **`source` 与 `target` 必须解析到已加载的具名实例**；
3. **`strength` 必须满足**：`0.0 ≤ strength ≤ 1.0`；
4. **`decay.rate` 必须 ≥ 0.0**；
5. **`decay.on_event` 必须引用有效的 `@Event`**；
6. **`metadata`（若存在）必须引用有效的 `@Schema`**，且该 schema 用于描述关系附加数据；
7. **禁止自环？** → **允许**（`source == target` 合法，如“自反关系”）；
8. **路径格式合法性**：必须符合 CLD 命名规范（`<kind>::<universe>::<domain>::<name>`）。

---

### 6. `@Constraint` —— 状态一致性契约

> **定位**：声明式状态不变量 · 执行时自动验证 · 违约即响应
> **哲学对齐**：
>
> - **执行即存在**：验证是执行流程的原子组成部分，非可选钩子；
> - **零隐式**：约束必须显式声明、显式引用，无默认、无继承、无隐式激活；
> - **引用即图谱**：归属与作用域由引用路径决定，非词法位置。

---

#### 📐 语法定义

```clj
@Constraint[constraint::<universe_id>::<domain>::<name>] {
  condition: ref(@Predicate[Predicate::<universe_id>::<domain>::<Predicate_name>]),
  on_violation: {
    policy: ref(@Enum[enum::cl::constraint::ConstraintViolationPolicy]::<variant>),
    violation_event?: ref(@Event[event::<universe_id>::<domain>::<event_name>]),
    message?: string
  }
}
```

> ✅ **必须作为顶级原语独立声明**，**禁止内联或匿名定义**。

---

#### 🔧 系统级枚举依赖（引擎预置 · 不可覆盖）

```clj
@Enum[enum::cl::constraint::ConstraintViolationPolicy] {
  variants: [ halt, rollback, log_and_continue, emit_event ]
}
```

| 策略 | 语义 | 使用约束 |
|------|------|----------|
| `halt` | **立即终止当前执行上下文**（如 `@Effect`），抛出 `ConstraintViolationError`，**所有状态变更回滚** | 默认策略，推荐用于业务关键约束 |
| `rollback` | 回滚本次执行单元（如 `@Effect`）引起的所有状态写入（需引擎支持事务性状态管理） | 仅在引擎声明支持事务时可用 |
| `log_and_continue` | 仅记录违约日志（含 `message`），**继续执行后续动作** | **仅限调试/观测场景**，违背契约精神，生产环境禁用 |
| `emit_event` | **不中断执行**，触发指定 `violation_event`，由用户通过 `@Rule` 自定义响应逻辑 | 必须提供 `violation_event` 字段 |

> ⚠️ 若 `policy = emit_event` 但未提供 `violation_event`，**加载器拒绝加载**（违反“零隐式”）。

---

#### 📍 归属与激活规则（路径即作用域）

`@Constraint` **本身无执行上下文**，必须被以下实体**显式引用**才能生效：

| 引用位置 | 作用域 | 验证时机 |
|----------|--------|----------|
| `@Niche.constraints` | **局部约束**：仅对该 niche 拥有的 `@StateSlot` 写入生效 | 当该 niche 的任何 `@Action` 尝试修改其 `owns_slots` 中的状态时 |
| `@World.constraints` | **全局约束**：对世界内所有状态变更生效 | 每次任何 `@Action` 修改状态前（全局守卫） |
| `@Effect.guard` | **前置执行契约**：在 `@Effect` 执行前验证 | 在 `actions` 执行前，**不修改状态**，仅验证当前状态是否满足 `condition` |

> ✅ **同一 `@Constraint` 可被多处引用**（如既作全局守卫，又作 effect guard），语义一致。

---

#### 📚 Constraint完整示例

```clj
;; 1. 声明状态槽
@StateSlot[slot::example_world::spirit::soul_rings] {
  type: ref(@Enum[enum::cl::core::ValueType]::integer),
  initial: 0
}

;; 2. 声明原子查询（严格字面量）
@Predicate[Predicate::example_world::spirit::soul_rings_count_le_10] {
  field: ref(@StateSlot[slot::example_world::spirit::soul_rings]),
  op: ref(@Enum[enum::cl::core::ComparisonOp]::le),
  value: 10  ;; <strict_literal>，合法
}

;; 3. 声明约束（独立顶级实体）
@Constraint[constraint::example_world::spirit::max_soul_rings] {
  condition: ref(@Predicate[Predicate::example_world::spirit::soul_rings_count_le_10]),
  on_violation: {
    policy: ref(@Enum[enum::cl::constraint::ConstraintViolationPolicy]::halt),
    message: "魂师魂环数量不可超过10"
  }
}

;; 4a. 局部引用：绑定到具体 Niche
@Niche[niche::example_world::citizen::tang_san] {
  owns_slots: [ ref(@StateSlot[slot::example_world::spirit::soul_rings]) ],
  constraints: [ ref(@Constraint[constraint::example_world::spirit::max_soul_rings]) ]
}

;; 4b. 或全局引用（可选）
@World[world::example_world] {
  entities: [ ref(@Niche[niche::example_world::citizen::tang_san]) ],
  constraints: [ ref(@Constraint[constraint::example_world::spirit::max_soul_rings]) ]  ;; 全局生效
}

;; 4c. 或作为 Effect 守卫（前置验证）
@Effect[effect::spirit::absorb_soul_ring] {
  guard: ref(@Constraint[constraint::example_world::spirit::max_soul_rings]),  ;; 执行前检查
  actions: [ ref(@Action[action::example_world::spirit::increment_soul_rings]) ]
}
```

---

#### 🛡️ 引擎执行语义

- **状态变更时验证**（来自 `@Niche`/`@World` 引用）：
  - 在 `@Action` 写入状态**前**，收集所有相关约束；
  - 模拟写入后状态，验证 `condition`；
  - 若任一约束违约，按其 `policy` 处理（如 `halt` 则丢弃本次写入）。

- **Effect 前置验证**（来自 `@Effect.guard`）：
  - 在执行任何 `actions` **之前**，以**当前状态**验证 `condition`；
  - 若违约，按 `policy` 处理（如 `halt` 则跳过整个 `@Effect`）；
  - **不修改状态**，因此不触发状态变更约束。

---

### 7. `@StateSlot` —— 状态的最小存在单元

```clj
@Enum[enum::cl::slot::SlotType] { variants: [ discrete, continuous, structured, ... ] }
@Enum[enum::cl::slot::SlotAccessMode] { variants: [ read_only, mutable, transient, ... ] }
@Enum[enum::cl::slot::SlotDecayMode] { variants: [ none, linear_per_tick, exponential, ... ] }

@StateSlot[slot::<universe_id>::<domain>::<slot_name>] {
  schema: ref(@Schema[schema::<universe_id>::<domain>::<schema_name>]),
  init: <literal_matching_schema>,
  metadata: {
    type: ref(@Enum[enum::cl::slot::SlotType]::<variant>),
    access: ref(@Enum[enum::cl::slot::SlotAccessMode]::<variant>),
    decay?: {
      mode: ref(@Enum[enum::cl::slot::SlotDecayMode]::<variant>),
      rate?: float
    },
    unit?: string,
    description?: string
  }
}
```

> ✅ **所有状态必须通过 `@StateSlot` 声明**，归属由 `<universe_id>::<domain>` 和拥有者 `@Niche` 共同确定。

---

### 8. `@Predicate` —— 布尔状态谓词

> **定位**：纯声明式状态谓词 · 无运行时 · 静态全验证
> **哲学对齐**：
>
> - **无运行时**：禁止任何动态计算、函数调用、时间引用；
> - **静态全验证**：所有字面量、引用、结构在加载时校验；
> - **模式互斥**：原子与复合查询不可混合。

#### ✅ 语法定义（二选一，不可混用）

```clj
;; ── 原子查询：单字段比较 ───────────────────────────────
@Predicate[Predicate::<universe_id>::<domain>::<name>] {
  field: ref(@StateSlot[slot::<universe_id>::<domain>::<slot_name>]),
  op: ref(@Enum[enum::cl::core::ComparisonOp]::<variant>),
  value: <strict_literal>
}

;; ── 复合查询：逻辑组合 ───────────────────────────────
@Predicate[Predicate::<universe_id>::<domain>::<name>] {
  logical_op: ref(@Enum[enum::cl::core::LogicalOp]::<variant>),
  subqueries: [
    ref(@Predicate[Predicate::<universe_id>::<domain>::<sub_name>]),
    ...
  ]
}
```

#### 🔒 `<strict_literal>` 的精确语义（递归、封闭、可验证）

| 类型 | 合法形式 | 验证规则 |
|------|----------|----------|
| **原子字面量** | `42`, `3.14`, `"hello"`, `true`, `false` | 必须与 `field` 所指 `@StateSlot.schema` 的顶层类型严格匹配 |
| **枚举引用** | `ref(@Enum[enum::<universe_id>::<domain>::<enum_name>]::<variant>)` | 枚举必须已声明，variant 必须在 `variants` 列表中 |
| **同质列表** | `[1,2,3]`, `["a","b"]` | 仅允许用于 `op = in` 或 `not_in`；元素类型必须一致且匹配 `field` 类型 |
| **结构对象** | `{ level: 10, type: ref(@Enum[...]) }` | **必须与 `field` 所指 `@StateSlot.schema` 的顶层结构完全同构**（字段名、类型、嵌套深度一致） |

#### 🚫 禁止事项（加载器必须拒绝）

- 同时包含 `field` 与 `logical_op`；
- `value` 中包含未声明的枚举引用、未定义的 schema 字段、动态表达式（如 `now()`）；
- 引用路径不符合命名规范（如 `slot::cl::...` 由用户定义）；
- `subqueries` 中引用不存在的 `@Predicate`。

#### 📚 Predicate合规示例

```clj
;; 原子查询：魂环数量 ≤ 9
@Predicate[Predicate::example_world::spirit::tang_san_has_nine_rings] {
  field: ref(@StateSlot[slot::example_world::spirit::soul_rings]),
  op: ref(@Enum[enum::cl::core::ComparisonOp]::le),
  value: 9
}

;; 复合查询：未死亡且魂环 < 10
@Predicate[Predicate::example_world::spirit::eligible_for_trial] {
  logical_op: ref(@Enum[enum::cl::core::LogicalOp]::and),
  subqueries: [
    ref(@Predicate[Predicate::example_world::spirit::is_alive]),
    ref(@Predicate[Predicate::example_world::spirit::soul_rings_lt_10])
  ]
}
```

---

### 9. `@ValueExpr` —— 可验证值表达式

> **定位重申**：
> `@ValueExpr` 是**值提供者（value provider）**，**永不用于布尔判断上下文**（如 `@Rule.when`、`@Constraint.condition`）。
> **唯一合法用途**：作为 `@Action.operand` 或未来扩展中的**动态值源**。

#### 系统级枚举

```clj
@Enum[enum::cl::value_expr::ExprNodeType] {
  variants: [ literal, slot_ref, aggregate, binary_op, exists, time_window, ... ]
}

@Enum[enum::cl::value_expr::AggregateFunc] {
  variants: [ sum, count, avg, min, max, distinct_count, ... ]
}

@Enum[enum::cl::value_expr::ArithmeticOp] {
  variants: [ add, subtract, multiply, divide, modulo, ... ]
}

@Enum[enum::cl::value_expr::WindowAggregate] {
  variants: [ count, latest, earliest, exists, ... ]
}
```

#### 📐 @ValueExpr 语法定义

```clj
@ValueExpr[ValueExpr::<universe_id>::<domain>::<name>] {
  expr: <ExprNode>
}
```

其中 `<ExprNode>` **必须且仅能包含以下六种结构之一**（由 `ExprNodeType` 枚举 discriminant 决定）：

##### 1. **字面量（literal）**

```clj
{ literal: <strict_literal> }
```

- `<strict_literal>` 必须为以下之一：
  - 整数（如 `42`）
  - 浮点数（如 `3.14`，**禁止 NaN/Inf**）
  - 字符串（如 `"hello"`，**必须双引号**）
  - 布尔（`true` / `false`）
  - `null`（仅用于可空字段）

> ❌ 禁止：对象、数组、未转义字符串、表达式模板。

---

##### 2. **状态槽引用（slot_ref）**

```clj
{ slot_ref: {
    slot: ref(@StateSlot[slot::<universe_id>::<domain>::<slot_name>]),
    path?: [ "field1", "field2", ... ]  ;; 可选字段路径
  }
}
```

- `path` 中的每个字段名必须在 `@StateSlot.schema` 声明的嵌套结构中存在；
- 若 `@StateSlot` 为原子类型（如 `int`），则 `path` 必须省略；
- 返回值类型 = 路径末端字段的类型。

---

##### 3. **聚合（aggregate）**

```clj
{ aggregate: {
    func: ref(@Enum[enum::cl::value_expr::AggregateFunc]::<variant>),
    target: ref(@Niche[entity::<universe_id>::<category>::<name>])
          | ref(@Relation[relation::<universe_id>::<domain>::<name>])
          | ref(@Collection[entity::<universe_id>::<domain>::<name>])
          | ref(@Collection[relation::<universe_id>::<domain>::<name>]),
    field_path: [ "field1", "field2", ... ]  ;; 必须与目标实体的 slot schema 对齐
  }
}
```

> 🔒 **关键修订**：`target` 现在**支持 `@Collection`**，但仅限以下两种前缀：
>
> - `entity::...` → 解包为多个 `@Niche` 实例
> - `relation::...` → 解包为多个 `@Relation` 实例

- `field_path` 必须指向目标 `@Niche` 或 `@Relation` 所拥有的 `@StateSlot` 中的字段；
- 若 `func = count`，则 `field_path` 必须为 `[]`（计数不依赖字段）；
- 若 `func = distinct_count`，则 `field_path` 必须指向可哈希类型（string/int/enum）。

---

##### 4. **二元算术运算（binary_op）**

```clj
{ binary_op: {
    op: ref(@Enum[enum::cl::value_expr::ArithmeticOp]::<variant>),
    left: <ExprNode>,
    right: <ExprNode>
  }
}
```

- 递归结构，支持任意深度嵌套；
- 加载器必须验证：`left` 和 `right` 的**运行时类型兼容**（如 `add` 要求两边均为 number）；
- 布尔值**不可参与算术运算**（除非 `op = multiply` 用于掩码，但 CLD 当前禁止）。

---

##### 5. **存在量词（exists）**

```clj
{ exists: {
    target_set: ref(@Niche[entity::<universe_id>::<category>::<name>])
              | ref(@Relation[relation::<universe_id>::<domain>::<name>])
              | ref(@Collection[entity::<universe_id>::<domain>::<name>])
              | ref(@Collection[relation::<universe_id>::<domain>::<name>]),
    condition: ref(@Predicate[Predicate::<universe_id>::<domain>::<name>])
  }
}
```

> 🔒 **关键修订**：`target_set` 同样支持 `@Collection[entity::...]` 和 `@Collection[relation::...]`

- 返回值类型：`boolean`
- 语义：**是否存在至少一个目标实体，满足 `@Predicate` 条件**
- `@Predicate` 必须接受与 `target_set` 类型匹配的输入（加载器验证）

> ⚠️ 尽管返回布尔值，**仍不可用于 `@Rule.when`** —— 仅可用于 `@Action.operand`（如设置 flag）

---

##### 6. **时间窗口聚合（time_window）**

```clj
{ time_window: {
    event_type: ref(@Event[event::<universe_id>::<domain>::<name>]),
    window_ticks: integer,  ;; ≥ 1，表示回溯多少个逻辑 tick
    aggregate?: ref(@Enum[enum::cl::value_expr::WindowAggregate]::<variant>)
  }
}
```

- `aggregate` 默认为 `count`（若省略）；
- 仅当 `event_type` 的 schema 包含值字段时，才允许 `latest`/`earliest`；
- 返回值类型取决于 `aggregate`：
  - `count` / `exists` → `int` / `boolean`
  - `latest` / `earliest` → 事件 payload 类型

---

#### 🔒 加载器验证规则（强制执行）

对每个 `@ValueExpr` 的 `expr` 字段：

1. **单字段约束**：必须**恰好包含一个字段**，且字段名 ∈ `ExprNodeType`；
2. **结构递归验证**：所有嵌套 `ExprNode` 同样遵守本规则；
3. **引用解析**：所有 `ref(...)` 必须指向已加载的顶级实体；
4. **路径对齐**：
    - `slot_ref.path` 和 `aggregate.field_path` 必须在目标 schema 中存在；
    - 字段类型必须匹配操作语义（如 `sum` 要求数值型）；
5. **Collection 类型安全**：
    - 若 `target` 或 `target_set` 是 `@Collection`，则其 `kind_prefix` 必须为 `entity` 或 `relation`；
    - 其 `elements` 必须全为 `@Niche`（对应 `entity`）或 `@Relation`（对应 `relation`）；
6. **禁止额外字段**：如 `type`, `comment`, `description` 等均非法；
7. **类型一致性**（未来可选，当前建议）：记录表达式返回类型，用于 `@Action.operand` 与 `@StateSlot` 的类型匹配。

---

#### 📚 使用示例

##### 示例 1：通过 Collection 聚合

```clj
@Collection[entity::example_world::system::citizens] {
  elements: [
    ref(@Niche[entity::example_world::citizen::Alice]),
    ref(@Niche[entity::example_world::citizen::Bob])
  ]
}

@ValueExpr[ValueExpr::example_world::stats::total_spirit_power] {
  expr: {
    aggregate: {
      func: ref(@Enum[enum::cl::value_expr::AggregateFunc]::sum),
      target: ref(@Collection[entity::example_world::system::citizens]),
      field_path: [ "spirit_power" ]  ;; 假设 slot schema 中有此字段
    }
  }
}
```

##### 示例 2：通过 Collection 存在查询

```clj
@ValueExpr[ValueExpr::example_world::combat::any_elite_enemy] {
  expr: {
    exists: {
      target_set: ref(@Collection[entity::example_world::combat::elite_enemies]),
      condition: ref(@Predicate[Predicate::example_world::combat::is_alive])
    }
  }
}
```

##### 示例 3：合法布尔值用于 Action

```clj
@Action[action::example_world::ui::show_boss_warning] {
  target_niche: ref(@Niche[entity::example_world::player::main]),
  slot: ref(@StateSlot[slot::example_world::ui::show_boss_alert]),
  operation: ref(@Enum[enum::cl::core::StateOperation]::set),
  operand: ref(@ValueExpr[ValueExpr::example_world::combat::any_elite_enemy])
}
```

---

#### 🔗 语义边界总结（契约性规则 · 加载器强制）

|上下文|允许的引用|禁止的引用|
|---|---|---|
|`@Constraint.condition`|`ref(@Predicate[...])`|`ref(@ValueExpr[...])`|
|`@Rule.when`|`ref(@Predicate[...])`|`ref(@ValueExpr[...])`|
|`@Action.operand`|`ref(@ValueExpr[...])` 或 `<strict_literal>`|`ref(@Predicate[...])`|
|`@Effect.guard`|`ref(@Constraint[...])`|直接引用 `@ValueExpr` 或 `@Predicate`|

> **违反 = 加载失败**（零妥协执行契约）

---

### 10. @Collection —— 显式、类型引导的通用集合容器

> @Collection —— 显式、类型引导的通用集合容器
> 用途：
>
> - 聚合同类型顶级实体（如多个 @Niche）
> - 支持嵌套（collection of collections）
> - 作为 @ValueExpr、@Rule、@Action 等上下文中的合法引用目标
> 路径格式：
> `<kind_prefix>::<universe_id>::<domain>::<name>`
> 其中 <kind_prefix> 必须为系统预定义的集合种类前缀（见下方枚举）

```clj
@Collection[<kind_prefix>::<universe_id>::<domain>::<name>] {
    ;; 必填：元素列表，每个元素必须是已声明的顶级实体引用
    elements: [
        ref(<entity_matching_kind_prefix>),
        ...
    ]
}
```

> elements 不可为空（若允许空集，需显式支持；当前默认非空）

#### 🔒 约束规则（加载器必须强制验证）

> 路径前缀 <kind_prefix> 必须属于以下枚举之一：

```clj
@Enum[enum::cl::collection::KindPrefix] {
    variants: [
        entity,     ;; 元素必须是 @Niche
        action,     ;; 元素必须是 @Action
        relation,   ;; 元素必须是 @Relation
        predicate,  ;; 元素必须是 @Predicate
        slot,       ;; 元素必须是 @StateSlot
        collection  ;; 元素必须是 @Collection（支持嵌套）
    ]
}
```

#### 元素类型校验规则

| `kind_prefix` | 合法元素原语类型      | 示例                                                |
| ------------- | ------------- | ------------------------------------------------- |
| `entity`      | `@Niche`      | `ref(@Niche[entity::u::cat::Player])`             |
| `action`      | `@Action`     | `ref(@Action[action::u::combat::attack])`         |
| `relation`    | `@Relation`   | `ref(@Relation[relation::u::social::alliance])`   |
| `predicate`   | `@Predicate`  | `ref(@Predicate[Predicate::u::combat::is_alive])` |
| `slot`        | `@StateSlot`  | `ref(@StateSlot[slot::u::player::level])`         |
| `collection`  | `@Collection` | `ref(@Collection[entity::u::group::heroes])`      |

> ❌ 若违反（如 entity::... 中包含 @Action），加载器必须报错：
Collection 'entity::...' contains element of invalid type '@Action[...]'

#### ✅ 使用示例

```clj
@Collection[entity::example_world::system::citizens] {
  elements: [
    ref(@Niche[entity::example_world::citizen::SoulMaster]),
    ref(@Niche[entity::example_world::citizen::Blacksmith]),
    ref(@Niche[entity::example_world::citizen::Alice])
  ]
}

@Collection[collection::example_world::system::all_entity_groups] {
  elements: [
    ref(@Collection[entity::example_world::system::citizens]),
    ref(@Collection[entity::example_world::system::monsters])
  ]
}
```

#### 📌 与其他原语的关系

- @Collection 不是运行时实体，而是静态引用容器
- 仅当上下文（如 @ValueExpr.aggregate.target）显式允许引用 @Collection 时才有效
- 在 @ValueExpr 中，仅 kind_prefix = entity 或 relation 的 @Collection 可用于 aggregate/exists

---

## 🔗 三、路径即归属：结构化命名空间规则

所有原语标识符 **必须** 采用如下格式：

| 原语 | 前缀（强制） | 语义路径建议结构 |
|------|--------------|------------------|
| `@World` | `world` | `<universe_id>` |
| `@Origin` | `origin` | `<universe_id>::<name>` |
| `@Timeline` | `timeline` | `<universe_id>::<name>` |
| `@Event` | `event` | `<universe_id>::<domain>::<name>` |
| `@CoreEvent` | `core_event` | `<universe_id>::<domain>::<name>` |
| `@Niche` | `entity` | `<universe_id>::<category>::<name>` |
| `@Rule` | `rule` | `<universe_id>::<domain>::<name>` |
| `@Scheduler` | `scheduler` | `<universe_id>::<name>` |
| `@Era` | `era` | `<universe_id>::<name>` |
| `@Horizon` | `horizon` | `<universe_id>::<name>` |
| `>>BehaviorEmitter` | `emit` | `<universe_id>::<domain>::<name>` |
| `@Enum` | `enum` | `<universe_id>::<domain>::<name>` |
| `@Action` | `action` | `<universe_id>::<domain>::<name>` |
| `@Effect` | `effect` | `<universe_id>::<domain>::<name>` |
| `@Schema` | `schema` | `<universe_id>::<domain>::<name>` |
| `@Relation` | `relation` | `<universe_id>::<domain>::<name>` |
| `@Constraint` | `constraint` | `<universe_id>::<domain>::<name>` |
| `@StateSlot` | `slot` | `<universe_id>::<domain>::<name>` |
| `@Predicate` | `predicate` | `<universe_id>::<domain>::<name>` |
| `@ValueExpr` | `value_expr` | `<universe_id>::<domain>::<name>` |
| `@Collection` | `entity`/`action`/`relation`/`predicate`/`slot`/`collection`  | `<universe_id>::<domain>::<name>` |

> - 所有 **系统级枚举** 必须使用 `enum::cl::<semantic_domain>::<name>`
> - 所有 **系统级 Schema/Action/Event** 使用 `xxx::cl::<domain>::<name>`
> - 用户定义内容 **禁止使用 `universe_id = "cl"`**

---
author: <haiman1024 <alexredskaber@gmail.com>>
