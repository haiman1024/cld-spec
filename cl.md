# cl universe official content manifest

> **目标：构建完整、自洽、可验证的 `cl` 官方宇宙内容清单**
> 所有内容必须：
>
> - 路径为 `xxx::cl::...`
> - 由 CLD 引擎预置、不可由用户覆盖或定义
> - 用于支撑用户宇宙的语义表达与执行契约

---

## 📦 一、系统级枚举（`enum::cl::...`）

### 1. 世界语义

```clj
@Enum[enum::cl::world::CausalTension] {
  variants: [ determinism_vs_contingency, order_vs_chaos, permanence_vs_flux ]
}
@Enum[enum::cl::world::TemporalTopology] {
  variants: [ linear, cyclic, branching, fragmented, manifold, quantum ]
}
```

> CausalTension 可扩展，但必须保持语义正交性；权重总和恒为 100

### 2. 时间与调度

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
```

### 3. 行为与提示工程

```clj
@Enum[enum::cl::behavior::Role] {
  variants: [ system, user, assistant, tool, critic, planner, observer ]
}
@Enum[enum::cl::behavior::StructuredFormat] {
  variants: [ json, yaml, toml, xml, markdown, edn, protobuf_text, csv, tsv ]
}
@Enum[enum::cl::behavior::ModelClass] {
  variants: [ text_gen, reasoning, multimodal, agent, embedding, reranker ]
}
@Enum[enum::cl::behavior::KnowledgeInjectionMode] {
  variants: [ full_horizon, memory_only, none, context_window_only, time_window_only ]
}
```

> 推荐优先使用此标准角色集；仅当语义无法表达时才定义用户级 `enum::<universe_id>::behavior::Role`

### 4. 约束与验证

```clj
@Enum[enum::cl::constraint::ConstraintViolationPolicy] {
  variants: [ halt, rollback, log_and_continue, emit_event ]
}
```

### 5. 核心操作语义

#### 1. 基础值类型（ValueType）

```clj
@Enum[enum::cl::core::ValueType] {
  variants: [ string, integer, float, boolean, null ]
}
```

> **说明**：
>
> - `null` 用于可空字段或初始化占位，仅在 `@StateSlot` 支持可空 schema 时合法
> - 所有 `<strict_literal>` 必须与上述类型之一严格匹配

```clj
@Schema[schema::cl::core::ValueType::string] {
    value: string
}
@Schema[schema::cl::core::ValueType::integer] {
    value: integer
}
@Schema[schema::cl::core::ValueType::float] {
    value: float
}
@Schema[schema::cl::core::ValueType::boolean] {
    value: boolean
}
@Schema[schema::cl::core::ValueType::null] {
    value: null
}
```

---

#### 2. 比较操作符（ComparisonOp）

```clj
@Enum[enum::cl::core::ComparisonOp] {
  variants: [ eq, ne, lt, le, gt, ge, in, not_in, exists, not_exists ]
}
```

> **语义说明**：
>
> - `eq` / `ne`：全等比较（结构/值完全一致）
> - `lt` / `le` / `gt` / `ge`：仅对 `integer` / `float` 有效
> - `in` / `not_in`：右侧必须为同质列表字面量
> - `exists` / `not_exists`：用于判断 `@StateSlot` 是否非空（或结构字段是否存在），**仅在 `@Predicate` 的原子查询中允许使用，且 `value` 字段必须省略**

---

#### 3. 逻辑操作符（LogicalOp）

```clj
@Enum[enum::cl::core::LogicalOp] {
  variants: [ and, or, xor, nand, nor ]
}
```

> **说明**：
>
> - `and` / `or`：标准短路逻辑（引擎可优化）
> - `xor`：异或（仅当奇数个子谓词为真时为真）
> - `nand` / `nor`：通用逻辑门，用于形式化验证场景
> - 所有逻辑操作符作用于 `subqueries` 列表，列表长度 ≥ 2
>
---

#### 4. 状态操作（StateOperation）

```clj
@Enum[enum::cl::core::StateOperation] {
  variants: [
    set,        ;; 覆盖赋值
    add,        ;; 数值加法 / 列表拼接（若目标为 list）
    subtract,   ;; 数值减法
    multiply,   ;; 数值乘法
    divide,     ;; 数值除法（结果为 float）
    append,     ;; 仅用于 list 类型，追加单个元素
    prepend,    ;; 仅用于 list 类型，前置单个元素
    remove,     ;; 从 list 或 set 中移除指定元素
    merge,      ;; 递归合并结构（仅对 structured 类型）
    clear       ;; 清空容器（list/set/map）或重置为 init
  ]
}
```

> **类型安全规则（引擎强制）**：
>
> - `add`/`subtract`/`multiply`/`divide` 仅作用于 `integer`/`float`
> - `append`/`prepend`/`remove` 仅作用于 `list` 类型 `@StateSlot`
> - `merge` 要求 operand 与目标 slot schema 结构兼容
> - `clear` 将 slot 重置为其 `init` 值（非 `null`）

---

### 6. 状态槽元数据

```clj
@Enum[enum::cl::slot::SlotType] {
  variants: [ discrete, continuous, structured, categorical, set, list, map ]
}
@Enum[enum::cl::slot::SlotAccessMode] {
  variants: [ read_only, mutable, transient, ephemeral ]
}
@Enum[enum::cl::slot::SlotDecayMode] {
  variants: [ none, linear_per_tick, exponential, event_triggered ]
}
```

### 7. 时间线与视界

```clj
@Enum[enum::cl::timeline::TimelineType] {
  variants: [ causal_manifold, event_graph ]
}
@Enum[enum::cl::timeline::TimelineFeature] {
  variants: [ branching, converging, local_loop, time_jump, causal_island, retrocausality ]
}
@Enum[enum::cl::horizon::HorizonType] {
  variants: [ causal_past, era_limited, omniscient, blind, prophetic ]
}
@Enum[enum::cl::horizon::PropheticScope] {
  variants: [ vision, dream, oracle, echo, whisper, omen ]
}
@Enum[enum::cl::horizon::AmbiguityLevel] {
  variants: [ low, medium, high, chaotic, unknowable ]
}
```

### 8. 集合类型前缀

```clj
@Enum[enum::cl::collection::KindPrefix] {
  variants: [ entity, action, relation, predicate, slot, collection ]
}
```

### 9. 值表达式节点类型

```clj
@Enum[enum::cl::value_expr::ExprNodeType] {
  variants: [ literal, slot_ref, aggregate, binary_op, exists, time_window ]
}
@Enum[enum::cl::value_expr::AggregateFunc] {
  variants: [ sum, count, avg, min, max, distinct_count ]
}
@Enum[enum::cl::value_expr::ArithmeticOp] {
  variants: [ add, subtract, multiply, divide, modulo ]
}
@Enum[enum::cl::value_expr::WindowAggregate] {
  variants: [ count, latest, earliest, exists ]
}
```

### 10. 纪元标签

```clj
@Enum[enum::cl::era::EraTag] {
  variants: [ technological, mythological, post_apocalyptic, golden_age ]
}
```

---

## 📐 二、系统级 Schema（`schema::cl::...`）

### 1. 时间间隔

```clj
@Schema[schema::cl::time::TimeInterval] {
  unit: ref(@Enum[enum::cl::time::Unit]),
  value: integer  ;; ≥1
}
```

### 2. 调度器子结构

```clj
@Schema[schema::cl::scheduler::TickSpec] {
  interval: ref(@Schema[schema::cl::time::TimeInterval]),
  phase_offset?: integer,
  max_jitter?: ref(@Schema[schema::cl::time::TimeInterval])
}

@Schema[schema::cl::scheduler::TickBehavior] {
  effects: [ ref(@Effect[effect::<universe_id>::<domain>::<effect_name>]) ],
  execution_order: ref(@Enum[enum::cl::scheduler::ExecutionOrder]),
  failure_policy: ref(@Enum[enum::cl::scheduler::FailurePolicy])
}

@Schema[schema::cl::scheduler::ConsistencyContract] {
  enforce_timeline: boolean,
  enforce_anchors: boolean,
  allow_retcon?: boolean
}
```

### 3. 行为发射器基础 Schema

```clj
@Schema[schema::cl::behavior::ImplementationSpec] {
  model_class: ref(@Enum[enum::cl::behavior::ModelClass]),
  model_name: string,
  temperature: float
}

@Schema[schema::cl::behavior::instruction::InlineText] {
  content: string
}

@Schema[schema::cl::behavior::output_format::NaturalLanguage] {
  style_hint: string
}
```

> ⚠️ 注意：`Structured` 指令/输出格式的 `payload_schema` 和 `payload` 由用户定义，**cl 不提供具体结构**，仅提供模式框架。

---

## 🧩 三、系统级 Action 模板（`action::cl::system::...`）

> 这些是**模板说明**，实际使用时用户必须创建具名实例，但引擎需识别其语义。

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

> ✅ 引擎必须支持这三种原子动作的执行语义。

---

## 🧱 四、系统级原语（非模板，但属于 cl 宇宙）

### `@Era`、`@CoreEvent` 等无系统级实例
>
> CLD 规定：**系统级原语仅提供枚举、Schema、Action 模板**，具体 `@Era`、`@CoreEvent`、`@Niche` 等均由用户宇宙定义。

---

## 🔒 五、禁止用户定义的路径空间

- 所有 `xxx::cl::...` 路径均为 **CLD 引擎保留**
- 用户 `universe_id` **不得为 `"cl"`**
- 用户不得定义任何 `enum::cl::...`、`schema::cl::...` 等

---

## ✅ 最终结论：完整 `cl` 官方宇宙内容清单

| 类型 | 路径 | 是否必须 |
|------|------|--------|
| `@Enum` | `enum::cl::world::CausalTension` | ✅ |
| `@Enum` | `enum::cl::world::TemporalTopology` | ✅ |
| `@Enum` | `enum::cl::time::Unit` | ✅ |
| `@Enum` | `enum::cl::scheduler::Mode` | ✅ |
| `@Enum` | `enum::cl::scheduler::ExecutionOrder` | ✅ |
| `@Enum` | `enum::cl::scheduler::FailurePolicy` | ✅ |
| `@Enum` | `enum::cl::behavior::Role` | ✅ |
| `@Enum` | `enum::cl::behavior::StructuredFormat` | ✅ |
| `@Enum` | `enum::cl::behavior::ModelClass` | ✅ |
| `@Enum` | `enum::cl::behavior::KnowledgeInjectionMode` | ✅ |
| `@Enum` | `enum::cl::constraint::ConstraintViolationPolicy` | ✅ |
| `@Enum` | `enum::cl::core::ValueType` | ✅ |
| `@Enum` | `enum::cl::core::ComparisonOp` | ✅ |
| `@Enum` | `enum::cl::core::LogicalOp` | ✅ |
| `@Enum` | `enum::cl::core::StateOperation` | ✅ |
| `@Enum` | `enum::cl::slot::SlotType` | ✅ |
| `@Enum` | `enum::cl::slot::SlotAccessMode` | ✅ |
| `@Enum` | `enum::cl::slot::SlotDecayMode` | ✅ |
| `@Enum` | `enum::cl::timeline::TimelineType` | ✅ |
| `@Enum` | `enum::cl::timeline::TimelineFeature` | ✅ |
| `@Enum` | `enum::cl::horizon::HorizonType` | ✅ |
| `@Enum` | `enum::cl::horizon::PropheticScope` | ✅ |
| `@Enum` | `enum::cl::horizon::AmbiguityLevel` | ✅ |
| `@Enum` | `enum::cl::era::EraTag` | ✅ |
| `@Enum` | `enum::cl::collection::KindPrefix` | ✅ |
| `@Enum` | `enum::cl::value_expr::ExprNodeType` | ✅ |
| `@Enum` | `enum::cl::value_expr::AggregateFunc` | ✅ |
| `@Enum` | `enum::cl::value_expr::ArithmeticOp` | ✅ |
| `@Enum` | `enum::cl::value_expr::WindowAggregate` | ✅ |
| `@Schema` | `schema::cl::time::TimeInterval` | ✅ |
| `@Schema` | `schema::cl::scheduler::TickSpec` | ✅ |
| `@Schema` | `schema::cl::scheduler::TickBehavior` | ✅ |
| `@Schema` | `schema::cl::scheduler::ConsistencyContract` | ✅ |
| `@Schema` | `schema::cl::behavior::ImplementationSpec` | ✅ |
| `@Schema` | `schema::cl::behavior::instruction::InlineText` | ✅ |
| `@Schema` | `schema::cl::behavior::output_format::NaturalLanguage` | ✅ |
| `@Action`（模板） | `action::cl::system::EmitEvent` | ✅ |
| `@Action`（模板） | `action::cl::system::UpdateNicheSlot` | ✅ |
| `@Action`（模板） | `action::cl::system::CallEffect` | ✅ |

---
