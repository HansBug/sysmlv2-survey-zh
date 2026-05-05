# 01 标准精读：OMG SysML v2 与 KerML

## 本章简介

本章对 OMG **KerML 1.0**、**SysML 2.0 Language**、**SysML 2.0 Transformation**、**Systems Modeling API & Services 1.0** 四份规范做技术深读。读者读完应能回答：

- 四份规范的关系如何？分别覆盖什么？
- KerML 与 SysML v2 在元模型、文法、库三个层面如何耦合？
- SysML v2 与 SysML v1 是否存在稳定的相互转换？官方与开源工具到什么程度？（含本地实测）
- 14 个核心概念（Element / Type / Feature / Specialization / Behavior / Definition-Usage / Connection / Requirement / Calculation / Variation / View / Metadata / Library / KEBNF）的精确语义与示范代码（取自官方 Pilot 实现的训练库或标准库）。
- API 的版本控制语义模型，以及为何 KerML 标准库 Element 必须用 UUID v5。

读者画像：具备形式化方法或编译器背景的资深工程师，熟悉「元模型 / 抽象语法 / 特化 / 类型系统」等概念。

## 1 四份规范文档

OMG 在 **2025-06-30 完成 Final Adoption**[^omg-final-2025]，经 FTF（Finalization Task Force）整理后于 **2026-03 以 `formal/2026-03-0x` 文档号正式出版**[^omg-formal-2026]：

| OMG 文件号 | 简称 | 范围 | 来源 |
|---|---|---|---|
| `formal/26-03-01` | **KerML 1.0** | 内核建模语言：抽象语法 + 文本/图形语法 + 标准库 | [omg.org/spec/KerML/1.0](https://www.omg.org/spec/KerML/1.0)[^omg-kerml] |
| `formal/26-03-02` | **SysML 2.0 — Part 1: Language** | 系统建模语言（基于 KerML） | [omg.org/spec/SysML/2.0/Language](https://www.omg.org/spec/SysML/2.0/Language)[^omg-sysml-language] |
| `formal/26-03-03` | **SysML 2.0 — Part 2: Transformation** | SysML v1 → v2 元模型与实例转换规则 | [omg.org/spec/SysML/2.0/Transformation](https://www.omg.org/spec/SysML/2.0/Transformation)[^omg-sysml-transform] |
| `formal/26-03-04` | **Systems Modeling API & Services 1.0** | API 元模型 + REST/HTTP + OSLC PSM | [omg.org/spec/SystemsModelingAPI/1.0](https://www.omg.org/spec/SystemsModelingAPI/1.0)[^omg-api] |

四份文档同时镜像在 [Systems-Modeling/SysML-v2-Release/doc/](https://github.com/Systems-Modeling/SysML-v2-Release/tree/master/doc)[^repo-release]：`1-Kernel_Modeling_Language.pdf` / `2a-OMG_Systems_Modeling_Language.pdf` / `2b-SysML_v1_to_v2_Transformation.pdf` / `3-Systems_Modeling_API_and_Services.pdf`。

> **术语澄清**：v1 时代常见的 "Part 4 = API" 写法已不再使用；v2 的 API 是平级独立规范，不再编号到 SysML 之内。本仓库统一称这四份规范为 **KerML 1.0、SysML 2.0 Language、SysML 2.0 Transformation、API & Services 1.0**。

历史归档（Beta1 2024-09、Beta2 2025-03）仍可访问[^omg-beta1][^omg-beta2]，对追溯演进轨迹有用。Final 与 Beta2 在文本语法与 KerML 元模型层面已无破坏性变更——主要差异是术语统一与 Annex 整理。RTF（Revision Task Force）周期性发布 minor 修订，工具链通常滞后 3–12 个月。

## 2 SysML v2 与 KerML 的关系

理解 SysML v2 的关键起点是：**SysML v2 不再是 UML 概要文件（profile），而是 KerML 的语义特化**。三个层面的耦合关系如下。

### 2.1 元模型层（Ecore 继承）

SysML v2 抽象语法直接以 KerML 元模型为父类。一个典型继承链（从 Pilot 的 `SysML.ecore` / `KerML.ecore` 可读出）[^pilot-ecore]：

```
SysML::PartDefinition  →  SysML::OccurrenceDefinition  →  SysML::Definition
                       →  KerML::Structure  →  KerML::Class  →  KerML::Classifier
                       →  KerML::Type  →  KerML::Namespace  →  KerML::Element
```

`PartDefinition` 自动继承 KerML 的所有特征（multiplicity、specialization、ownedFeature、isAbstract 等），不需要 SysML 重新定义。

### 2.2 文法层（Xtext `Grammar.with`）

Pilot 实现把语法分成两个 OSGi bundle，通过 Xtext 的语法继承机制串起来[^pilot-grammar]：

```xtext
grammar org.omg.sysml.xtext.SysML
    with org.omg.kerml.expressions.xtext.KerMLExpressions
```

在 `SysML.xtext` 中，多数规则通过 `import` 复用 `KerML.xtext` 的对应规则，再在 SysML 层加领域语法糖（`part def` / `attribute def` / `port def`）。daltskin 的 ANTLR4 grammar 也复制了这一双层结构（但对应文件名为 `KerMLLexer.g4` / `KerMLParser.g4` 与 `SysMLv2Lexer.g4` / `SysMLv2Parser.g4`），见 [04-解析 IDE](04-parsing-ide-infrastructure.md)。

### 2.3 库层（kernel libraries vs systems libraries）

Pilot 把库分两层：

- **`sysml.library/Kernel Libraries/`** —— KerML 自身的标准库（`Base.kerml` / `Links.kerml` / `Occurrences.kerml` / `Performances.kerml` / `Triggers.kerml` 等）。设计意图是给**语言设计者与高级工具开发者**使用，不是日常建模面向终端工程师的。
- **`sysml.library/Systems Library/`** —— SysML v2 域语法的真身（`Parts.sysml` / `Connections.sysml` / `Requirements.sysml` 等），所有 `part def` / `requirement def` 等关键字最终都展开到这里的库元素。
- **`sysml.library/Domain Libraries/`** —— 量纲（ISQ / SI）、几何、分析、因果链等可选领域库。

KerML 单独使用是合法的，但绝大多数日常工程模型走 SysML 层。

### 2.4 一个例子：`part def` 如何降到 KerML

把 `part def Vehicle { part wheels[4] : Wheel; }` 展开到底层语义：

1. SysML `part def Vehicle` ⇒ 生成 `PartDefinition`，并自动 `:> Parts::Part`（Pilot 的 `Systems Library/Parts.sysml` 定义 `Part :> Item`）。
2. `Item` ⇒ KerML `Object`（`Items.sysml` 中 `Item :> Objects::Object`）。
3. `Object` ⇒ KerML `Occurrence`（`Objects.kerml` 中定义）。
4. `Occurrence` ⇒ KerML `Anything`（`Base.kerml` 中根类型）。
5. `part wheels[4] : Wheel` 内部展开为 KerML 的 composite feature，`subsets parts`（继承自 `Part::parts`），多重度 `[4]`。

这条链可以用 Pilot 的 `%show` magic 在 Jupyter 中观察[^pilot-jupyter]。

## 3 KerML 根元模型

> 本节内容摘自 KerML 1.0 §7.2、§7.3、§7.4。所有代码示例来自 Pilot 的 `sysml.library/` 或 `Systems-Modeling/SysML-v2-Release/sysml/src/training/`，并附 raw URL 便于读者本地核对。

### 3.1 Element 与 Relationship（KerML §7.2.2）

**Element** 是元模型的根：

> *"Every element has a unique identifier known as its element ID. The properties of an element can change over its lifetime, but its element ID does not change after the element is created."* —— KerML §7.2.2.2[^omg-kerml]

`Element` 拥有：

- 不可变的 `elementId`（UUID）。
- 可变的 `aliasIds[*]`、`name[0..1]`、`shortName[0..1]`。
- 与外部世界关联的 `documentation` 与 `metadataFeatures`。

`Relationship` 是 `Element` 的子类，带方向，区分 `source` 与 `target`，至少有 2 个 `relatedElement`。除少数 Association 外，KerML 多数关系是**有向二元关系**。Relationship 自身又是 Element，可参与高阶关系——这是相对 UML 的最大元层简化。

```sysml
classifier <c123> AClassifier;
feature aFeature;
namespace P {
    classifier A;
    classifier B {
        feature x;
        feature y;
    }
}
```

### 3.2 Membership 与 Namespace（KerML §7.2.5）

`Namespace` 是 `Element` 子类，通过若干 **Membership**（关系子类）持有命名子元素；可见性分 `public / protected / private`。两类核心 Membership：

- **OwningMembership**：拥有所有权（拥有的子元素物理嵌套在 namespace 中）。
- **ImportMembership**：仅别名（`import` 语句产生的引用）。

```sysml
package P {
    private import OtherPkg::*;       // ImportMembership, private
    public  classifier A;             // OwningMembership, public
    protected feature internal_temp;  // 仅 P 的特化可见
}
```

### 3.3 Type / Classifier / Feature（KerML §7.3.2 / §7.3.3 / §7.3.4）

**Type** 是有 *extent*（外延：满足该类型条件的全体实例集合）的 Element。Type 派生出两个并列子类：

- **Classifier** 分类那些"在它之外存在的事物"，extent 是 *things*。
- **Feature** 表示"关系槽位"，extent 是 *links*——它在每个所属 (featuring) 实例上有一组 *values*。

这是 KerML 与 UML 的**核心歧异**：UML 把"属性 (Property)"与"类 (Class)"视为两类不同元素；KerML 把二者**统一**为 Type 的两个分支，二者都可特化、都可继承、都可被重定义。Feature 既可以是结构属性、关联端，也可以是行为参数或表达式参数。

`MultiplicityRange` 用 `[lower..upper]` 表示，`*` 表示无穷大；`unique`/`nonunique`、`ordered`/`unordered` 可选。

```sysml
classifier Wheel;
classifier DriveWheel specializes Wheel;
classifier Automobile {
    composite feature wheels[4] : Wheel;
    composite feature driveWheels[2] : DriveWheel subsets wheels;
}
```

### 3.4 FeatureChaining（KerML §7.3.4.6）

特征链 `a.b.c` 把若干 Feature 串成一条新 Feature；语义上是值的"导航"，并把第一个特征的所属类型与最后一个特征的目标类型穿透为新特征的所属/目标类型。链可以出现在任何关系声明里：

```sysml
feature cousins chains parents.siblings.children;
feature uncles  subsets parents.siblings;
connector vehicle.wheelAssembly.wheels to vehicle.road;
```

## 4 四种关系：Specialization / Subsetting / Redefinition / Conjugation

这是 SysML v2 相对 UML profile 最重要的范式跃迁。

| 关系 | 关键字 | 符号 | 适用对象 | 语义 |
|---|---|---|---|---|
| Specialization | `specializes` | `:>` | Type → Type | extent 子集（≤） |
| Subsetting | `subsets` | `:>` | Feature → Feature | values 子集（每个 domain 实例上） |
| Redefinition | `redefines` | `:>>` | Feature → Feature | values 相等；并替换继承的同名 feature |
| Conjugation | `conjugates` | `~` | Type ↔ Type | 继承所有成员，但 `in/out` 方向反向 |

UML 用 stereotype + tagged value 在元层"打补丁"，**无法对 *property* 做类型上的子类化**；KerML 把 Specialization 直接施加在 Feature 上，于是"四轮的 wheels 子集是两轮 driveWheels"这种细化不再依赖工具约定，而是有抽象语法层面的关系实体。

### 4.1 Subsetting 示例

来自 `04. Subsetting/Subsetting Example.sysml`[^training-04]：

```sysml
package 'Subsetting Example' {
    part def Vehicle {
        part parts : VehiclePart[*];
        part eng    : Engine       subsets parts;
        part trans  : Transmission subsets parts;
        part wheels : Wheel[4]     :>    parts;     // 简写
    }
    abstract part def VehiclePart;
    part def Engine       :> VehiclePart;
    part def Transmission :> VehiclePart;
    part def Wheel        :> VehiclePart;
}
```

### 4.2 Redefinition 示例

来自 `05. Redefinition/Redefinition Example.sysml`[^training-05]：

```sysml
package 'Redefinition Example' {
    part def Vehicle      { part eng : Engine; }
    part def SmallVehicle :> Vehicle { part smallEng : SmallEngine redefines eng; }
    part def BigVehicle   :> Vehicle { part bigEng   : BigEngine   :>>      eng; }

    part def Engine       { part cyl : Cylinder[4..6]; }
    part def SmallEngine  :> Engine { part redefines cyl[4]; }
    part def BigEngine    :> Engine { part redefines cyl[6]; }
    part def Cylinder;
}
```

注意 `part redefines cyl[4]`：KerML §7.3.4.5 规定，**redefining feature 没显式名时隐式继承被重定义 feature 的名**——所以 `SmallEngine` 中只是把继承到的 `cyl` 多重度从 `[4..6]` 收紧到 `[4]`，仍然叫 `cyl`。

### 4.3 Conjugation 示例（KerML §7.3.2.4）

```sysml
type Original specializes Base::Anything {
    in feature Input;
}
conjugate Conjugate1 conjugates Original;        // Conjugate1.Input 变为 out
conjugate Conjugate2 ~ Original;                  // 等价写法
```

这是 **port 共轭**的元层基础——SysML 中 `port def` 的 `~` 共轭让"客户端口"自动继承"服务端口"的所有 feature 但 `in/out` 颠倒。

## 5 行为本体（KerML §7.4.7 + §9.2）

KerML 把"发生"统一成 **Occurrence**：在时间和空间上有身份、可分割的事物。`Object` 与 `Performance` 是 `Occurrence` 的两个并列分支。

| 概念 | 元类 | 来源 | 简述 |
|---|---|---|---|
| Occurrence | Class | KerML §9.2.4 / `Occurrences.kerml` | 时空中有身份的事物 |
| Object | Class | `Objects.kerml` | 不变身份的"东西" |
| Performance | Behavior | `Performances.kerml` | 时空中"发生"的过程 |
| Behavior | Behavior | KerML §7.4.7 / `Performances.kerml` | Performance 的类型 |
| Step | Feature (Behavior typing) | KerML §7.4.7.3 | Behavior 内的子 Performance |
| Function | Behavior | KerML §7.4.8 / `Performances.kerml::Evaluation` | 返回值的 Behavior |
| Predicate | Function (Boolean) | KerML §7.4.8.4 | 返回 Boolean 的 Function |
| Expression | Step (Function-typed) | KerML §7.4.9 | Function 的求值 |
| Calculation | SysML | `Calculations.sysml` | 命名的、可复用的 Function 用法 |
| Action | SysML | `Actions.sysml` | Performance 的 SysML 包装 |
| State | SysML | `States.sysml` | 一段持续的 Performance |
| Transition | SysML | `States.sysml` | State 间转换关系 |
| Trigger | KerML | `Triggers.kerml::TriggerWhen / TriggerAt / TriggerAfter` | 触发表达式 |
| Guard | KerML | "boolean expression" KerML §7.4.8.5 | 守卫的 Predicate |

`Occurrence` 的实际定义（来自 [`Occurrences.kerml`][^kerml-occurrences]）：

```kerml
abstract class Occurrence specializes Anything disjoint from DataValue {
    feature portionOfLife : Life[1] subsets portionOf default self;
    feature self : Occurrence[1] redefines Anything::self
        subsets timeSlices, spaceSlices, spaceTimeCoincidentOccurrences, sameLifeOccurrences;
    feature this : Occurrence[1] default self;
    connector :HappensDuring from [1] self to [1] this;
    composite feature suboccurrences : Occurrence[0..*] subsets occurrences;
    /* ... */
}
```

SysML 状态机示例（来自 `23. State Definitions/State Definition Example-1.sysml`[^training-23]）：

```sysml
package 'State Definition Example-1' {
    attribute def VehicleStartSignal;
    attribute def VehicleOnSignal;
    attribute def VehicleOffSignal;

    state def VehicleStates {
        first start then off;
        state off;
        transition off_to_starting
            first off  accept VehicleStartSignal then starting;
        state starting;
        transition starting_to_on
            first starting accept VehicleOnSignal then on;
        state on;
        transition on_to_off
            first on accept VehicleOffSignal then off;
    }
}
```

## 6 Definition / Usage 范式

KerML 只有 Type / Feature 二分；SysML 在它之上引入 **定义 vs 使用**孪生范式：

- `part def X { … }` 声明一个 Classifier（PartDefinition），元模型层是 Type。
- `part p : X` 声明一个 Feature（PartUsage），把 `X` 作为其 typing。

定义只是模板；使用才会被实例化。`part p : X` 从语义上等价于在 KerML 层声明一个 type 为 `X` 的 composite feature。`ItemDef`/`AttributeDef`/`EnumerationDef`/`PortDef`/`ConnectionDef` 完全同构。

```sysml
part def Vehicle {                    // Classifier
    attribute mass : ISQ::MassValue;  // AttributeUsage  (composition by default)
    part eng : Engine[1];             // PartUsage,    composition
    ref  part driver : Person;        // PartUsage,    reference (no ownership)
}
part def Engine { part cyl : Cylinder[4..6]; }
part def Person;
part def Cylinder;

part myVehicle : Vehicle {            // Top-level usage
    part redefines eng = sportsEngine;
}
part sportsEngine : Engine;
```

`composite` 缺省于结构组合（强组合，`Part`），`ref` 表示弱引用——这正是 v1 中 `aggregation = composite/shared/none` 的元层等价物。

## 7 Connection / Port / Interface / ItemFlow / Succession

`connection def C` 声明一个有 `end` 端点的关联，`connect ... to ...` 是它的使用。SysML 用 `flow of T from a to b` 表达 ItemFlow（携带类型 T 的物质 / 能量 / 数据流）；`succession` 是时间上的 happens-before。

```sysml
package 'Connections Example' {
    part def TireBead; part def TireMountingRim;
    connection def PressureSeat {
        end [1] part bead         : TireBead;
        end [1] part mountingRim  : TireMountingRim;
    }
    part wheelHubAssembly {
        part wheel {
            part t { part bead : TireBead[2]; }
            part w { part rim  : TireMountingRim[2]; }
            connection : PressureSeat
                connect bead         references t.bead
                to      mountingRim  references w.rim;
        }
    }
}
```

来源：`09. Connections/Connections Example.sysml`[^training-09]。

```sysml
package 'Flow Usage Example' {
    private import 'Port Example'::*;
    part vehicle : Vehicle {
        part tankAssy : FuelTankAssembly;
        part eng      : Engine;
        flow of Fuel from tankAssy.fuelTankPort.fuelSupply to eng.engineFuelPort.fuelSupply;
        flow of Fuel from eng.engineFuelPort.fuelReturn    to tankAssy.fuelTankPort.fuelReturn;
    }
}
```

来源：`13. Flows/Flow Usage Example.sysml`[^training-13]。

## 8 Requirement / Constraint / VerificationCase / Allocation

来自 `32. Requirements/Requirement Definitions.sysml`[^training-32]：

```sysml
package 'Requirement Definitions' {
    private import ISQ::*; private import SI::*;

    requirement def MassLimitationRequirement {
        doc /* The actual mass shall be less than or equal to the required mass. */
        attribute massActual : MassValue;
        attribute massReqd   : MassValue;
        require constraint { massActual <= massReqd }
    }
    part def Vehicle {
        attribute dryMass      : MassValue;
        attribute fuelMass     : MassValue;
        attribute fuelFullMass : MassValue;
    }
    requirement def <'1'> VehicleMassLimitationRequirement :> MassLimitationRequirement {
        subject vehicle : Vehicle;
        attribute redefines massActual = vehicle.dryMass + vehicle.fuelMass;
        assume constraint { vehicle.fuelMass > 0[kg] }
    }
}
```

注意三个语义动词：

- `require` —— 必须满足的约束（goal）。
- `assume` —— 前置条件假设。
- `subject` —— 这个 Requirement 检查的对象绑定。

在 Pilot 库 `Requirements.sysml` 中可看到 `RequirementCheck :> RequirementConstraintCheck`，`subj : Anything[1]` —— **Requirement 实质是个返回 Boolean 的 Function**[^pilot-requirements]。

`assert constraint { ... }` 在用户模型里强制断言；`verify` 声明 VerificationCase 校验某 Requirement；`allocate` 把逻辑模型的元素映射到物理模型：

```sysml
package 'Allocation Definition Example' {
    package LogicalModel {
        part def TorqueGenerator;
        part torqueGenerator : TorqueGenerator;
    }
    package PhysicalModel {
        private import LogicalModel::*;
        part def PowerTrain;
        part powerTrain : PowerTrain;
        allocation def LogicalToPhysical {
            end logical  : LogicalElement;
            end physical : PhysicalElement;
        }
        allocation torqueGenAlloc : LogicalToPhysical
            allocate torqueGenerator to powerTrain;
    }
}
```

来源：`38. Allocation/Allocation Definition Example.sysml`[^training-38]。

UseCase 与 VerificationCase 都是 Case 的特化，详见 Pilot `Cases.sysml` / `UseCases.sysml` / `VerificationCases.sysml`。

## 9 Calculation / Constraint / Parametric

来自 `30. Calculations/Calculation Definitions.sysml`[^training-30]，物理学 F = m·a 类的例：

```sysml
package 'Calculation Definitions' {
    private import ScalarValues::Real;
    private import ISQ::*;

    calc def Power { in whlpwr : PowerValue; in Cd : Real; in Cf : Real;
                     in tm : MassValue;     in v  : SpeedValue;
        attribute drag     = Cd * v;
        attribute friction = Cf * tm * v;
        return : PowerValue = whlpwr - drag - friction;
    }
    calc def Acceleration { in tp : PowerValue; in tm : MassValue; in v : SpeedValue;
        return : AccelerationValue = tp / (tm * v);
    }
    calc def Velocity { in dt : TimeValue; in v0 : SpeedValue; in a : AccelerationValue;
        return : SpeedValue = v0 + a * dt;
    }
    calc def Position { in dt : TimeValue; in x0 : LengthValue; in v : SpeedValue;
        return : LengthValue = x0 + v * dt;
    }
}
```

`calc def` 等价于 KerML `function`（KerML §7.4.8）；`return` 子句声明 Function 的 result feature。`constraint def` 则是 `predicate`（KerML §7.4.8.4），返回 Boolean。`assert constraint { … }` 在使用层把 Boolean 推向"必须为真"。

## 10 Variation / Variant

```sysml
package 'Variation Definitions' {
    private import SI::mm;
    attribute def Diameter :> ISQ::LengthValue;

    part def Cylinder { attribute diameter : Diameter[1]; }
    part def Engine   { part cylinder : Cylinder[2..*]; }

    part '4cylEngine' : Engine { part redefines cylinder[4]; }
    part '6cylEngine' : Engine { part redefines cylinder[6]; }

    variation attribute def DiameterChoices :> Diameter {
        variant attribute diameterSmall = 70[mm];
        variant attribute diameterLarge = 100[mm];
    }
    variation part def EngineChoices :> Engine {
        variant '4cylEngine';
        variant '6cylEngine';
    }
}
```

来源：`36. Variability/Variation Definitions.sysml`[^training-36]。

`variation` 把一个 Definition 变为"变体集合"，`variant` 列举可选项。"配置 (configuration)"是为变体集合每一处选定具体 variant 的产物——这是 v2 内建的 SPL（Software / Systems Product Line）支持。

## 11 View / Viewpoint / Rendering

`Views.sysml` 中 `View :> Part`，`viewpoint def :> RequirementCheck`，意为"视点是对视图必须满足的需求"，`rendering def :> Part` 描述如何渲染——视图本身仍是模型元素，可被需求与约束治理[^pilot-views]：

```sysml
abstract view def View :> Part {
    ref view :>> self : View;
    abstract ref view subviews : View[0..*] :> views;
    abstract ref rendering viewRendering : Rendering[0..1];
    viewpoint viewpointSatisfactions : ViewpointCheck[0..*]
        :> viewpointChecks, checkedConstraints;
    satisfy requirement viewpointConformance by that { /* ... */ }
}
abstract viewpoint def ViewpointCheck :> RequirementCheck {
    ref viewpoint :>> self : ViewpointCheck;
    subject subj : View[1] :>> RequirementCheck::subj;
}
abstract rendering def Rendering :> Part { /* ... */ }
```

## 12 Metadata

`metadata def X about A, B { … }` 把元数据特化（自身是 Type）"贴"到现有模型元素上，并可对元素做扩展约束（`annotatedElement`）：

```sysml
package 'Metadata Example-1' {
    metadata def SafetyFeature;
    metadata def SecurityFeature {
        :> annotatedElement : SysML::PartDefinition;
        :> annotatedElement : SysML::PartUsage;
    }
    metadata SafetyFeature about
        vehicle::interior::seatBelt,
        vehicle::interior::driverAirBag,
        vehicle::bodyAssy::bumper;
    metadata SecurityFeature about
        vehicle::interior::alarm,
        vehicle::bodyAssy::keylessEntry;
}
```

来源：`39. Metadata/Metadata Example-1.sysml`[^training-39]。

KerML §7.4.13 把 metadata 定义为"元层级 feature"——它和普通 feature 共享所有规则，但所属 type 是 `Metaobject`，由 `Metaobjects.kerml` 提供。

## 13 库结构

Pilot 仓库 `sysml.library/` 三层结构：

```
sysml.library/
├── Kernel Libraries/
│   ├── Kernel Semantic Library/   ← KerML §9.2
│   │   ├── Base.kerml             § 9.2.2  Anything / DataValue / things / multiplicity
│   │   ├── Links.kerml            § 9.2.3  Link / BinaryLink / SelfLink
│   │   ├── Occurrences.kerml      § 9.2.4  Occurrence / HappensBefore / HappensDuring
│   │   ├── Objects.kerml          § 9.2.5  Object / LinkObject
│   │   ├── Performances.kerml     § 9.2.6  Performance / Evaluation / BooleanEvaluation
│   │   ├── Transfers.kerml        § 9.2.7  Transfer / FlowTransfer
│   │   ├── ControlPerformances.kerml         § 9.2.9
│   │   ├── TransitionPerformances.kerml      § 9.2.10
│   │   ├── StatePerformances.kerml           § 9.2.11
│   │   ├── Triggers.kerml                    TimeSignal / TriggerWhen / TriggerAt / TriggerAfter
│   │   ├── Clocks.kerml / SpatialFrames.kerml / Observation.kerml
│   │   ├── Metaobjects.kerml      metadata 元基础
│   │   ├── FeatureReferencingPerformances.kerml
│   │   └── KerML.kerml            (汇总根包)
│   ├── Kernel Data Type Library/   ← KerML §9.3
│   │   ├── ScalarValues.kerml     Boolean / Natural / Integer / Real / String
│   │   ├── VectorValues.kerml
│   │   └── Collections.kerml
│   └── Kernel Function Library/    ← KerML §9.4
│       ├── BaseFunctions, BooleanFunctions, IntegerFunctions, RealFunctions
│       ├── ControlFunctions, CollectionFunctions, SequenceFunctions
│       ├── TrigFunctions, ComplexFunctions, RationalFunctions
│       └── DataFunctions, OccurrenceFunctions, ScalarFunctions, StringFunctions, VectorFunctions
├── Systems Library/                ← SysML v2 §6.x（领域语法层）
│   ├── SysML.sysml (root), Parts.sysml, Items.sysml, Attributes.sysml,
│   │   Connections.sysml, Ports.sysml, Interfaces.sysml, Flows.sysml,
│   │   Actions.sysml, States.sysml, Calculations.sysml, Constraints.sysml,
│   │   Requirements.sysml, Cases.sysml, UseCases.sysml, AnalysisCases.sysml,
│   │   VerificationCases.sysml, Allocations.sysml, Metadata.sysml,
│   │   Views.sysml, StandardViewDefinitions.sysml
└── Domain Libraries/               ← 可选领域库
    ├── Quantities and Units/  (ISQ, SI, USCustomaryUnits 等)
    ├── Geometry/
    ├── Analysis/
    ├── Cause and Effect/
    ├── Metadata/
    └── Requirement Derivation/
```

依赖层级：`Base ← Links ← Occurrences ← Objects ∥ Performances ← {Transfers, ControlPerformances, StatePerformances, TransitionPerformances, Triggers}`；Systems Library 中所有 `*.sysml` 都 `private import` Kernel Semantic Library。

`Base.kerml` 真实头部[^kerml-base]：

```kerml
standard library package Base {
    abstract classifier Anything {
        feature self : Anything[1] subsets things chains things.that;
    }
    abstract datatype DataValue specializes Anything {
        feature self : DataValue redefines Anything::self;
    }
    abstract feature things    : Anything[1..*]            nonunique { feature that : Anything[1]; }
    abstract feature dataValues: DataValue[0..*] nonunique subsets things;
    abstract feature naturals  : ScalarValues::Natural[0..*]         subsets dataValues;
    multiplicity exactlyOne [1..1];
    multiplicity zeroOrOne  [0..1];
    multiplicity oneToMany  [1..*];
    multiplicity zeroToMany [0..*];
}
```

## 14 文本表示法细节

- **文件结构**：每个 `.sysml` / `.kerml` 文件是一个 root namespace；按惯例顶层是 `package P { … }`，但语法允许直接列元素。
- **导入**：`import P::*`、`import P::Q;`、`alias Foo for OtherPackage::ReallyLongName;`。可见性前缀 `public/protected/private`。
- **注释**：`//` 单行；`//* … */` 多行 *Note*；`/* … */` 在 `doc` 关键字之后变成结构化文档（属于 `Documentation` 元素）；裸 `/* … */` 也是注释。
- **doc 块**：`doc /* ... */` 把文档绑定到拥有它的元素，可被 API 检索。
- **多行字符串**：通过 unrestricted name `'…'` 可包含转义；spec §8.2.2.3 表 4 列出 `\n \r \t \b \f \' \" \\` 与 `\u{...}`。
- **Unicode 标识符**：basic name 限定 ASCII 字母 + 下划线 + 数字；非 ASCII 标识符必须用 `'Ångström'` 这样的 unrestricted 形式。
- **保留字**（KerML §8.2.2.6）：`abstract, about, alias, all, and, as, assert, behavior, binding, by, calc, case, class, classifier, comment, conjugate, conjugates, connect, connection, connector, constraint, datatype, def, default, dependency, derived, disjoining, disjoint, do, doc, else, end, exhibit, expr, false, feature, filter, first, flow, for, from, function, hastype, if, implies, import, in, include, inout, interaction, inverse, inverting, istype, language, library, locale, member, message, metaclass, metadata, multiplicity, namespace, nonunique, not, null, of, or, ordered, out, package, perform, port, predicate, private, protected, public, redefines, redefinition, ref, references, render, require, rep, return, satisfy, send, specialization, specializes, standard, state, step, struct, subject, subset, subsets, subtype, succession, then, timeslice, to, true, type, until, use, variant, variation, verify, view, viewpoint, when, while, xor`。

## 15 KEBNF 元语法

KerML / SysML 的具体语法都用 **KEBNF**（Kernel Extended BNF）写在 [`Systems-Modeling/SysML-v2-Release/bnf/`][^repo-bnf] 下：`KerML-textual-bnf.kebnf`、`SysML-textual-bnf.kebnf`、`SysML-graphical-bnf.kgbnf`（KGBNF = Kernel Graphical BNF）。

KEBNF 与"普通"EBNF 的关键差异：

1. **每条产生式带元类**：形如 `RuleName : MetaclassName = production…`。冒号后的元类名表明该产生式实例化哪个 KerML 抽象语法元素。例：

   ```kebnf
   Identification : Element = ( '<' declaredShortName = NAME '>' )?
                              ( declaredName = NAME )?
   ```

   解析此规则即直接构造一个 `Element` 实例并赋值 `declaredShortName / declaredName`。

2. **属性赋值与累加**：`= expr` 单值赋值；`+= expr` 列表追加。例 `client += [QualifiedName]` 把每个解析到的 `QualifiedName` 追加到 `client` 集合。

3. **跨引用 `[QualifiedName]`**：方括号里的产生式不是字面文本，而是"先解析 QualifiedName，再在符号表里解引用为对应 Element 的引用"——这是普通 EBNF 没有的语义动作。

4. **产生式继承**：`OwnedSpecialization : Specialization = …` 表示这条规则产生的元素是 `Specialization` 元类的特化（即 `OwnedSpecialization` 是子元类的别名），子规则可重定义 / 收紧父规则。这与 KerML 自身的 Specialization 语义同构——元语法用 KerML 的概念描述自己。

5. **抽象 / 具体规则混合**：抽象规则（如 `BinaryRelationshipBody`）只能在父规则里被引用，不会单独驱动 parser；具体规则才有自身入口。

6. **分层关系**：`SysML-textual-bnf.kebnf` 的根产生式**继承**自 `KerML-textual-bnf.kebnf` 的对应根 —— SysML 文本语法在文法层就是 KerML 语法的特化，新加的 SysML 规则要么 *redefine* 已有 KerML 规则，要么 *subset* 它们。这正是把 KerML 的 Specialization / Subsetting / Redefinition 用到元元层（M3）的体现：**元模型、模型、文法之间使用同一套关系语义**。

KEBNF 直接转 ANTLR4 / Langium / Xtext 都需要一些 mechanical patch（左递归、SLL 预测、关键字消歧、删除"语义动作")——daltskin 的 ANTLR4 grammar[^repo-daltskin-grammar]给出了 56 处 patch 的完整清单。详见 [04-解析 IDE](04-parsing-ide-infrastructure.md) §1。

## 16 API 与服务的语义模型

API 规范 §7.1.1 把所有可被传输的实体抽象为 **Record**，字段：

```
Record { id : UUID readOnly ;
         resourceIdentifier : IRI [0..1] ;
         alias : String[*] ;
         name : String[0..1] subsets alias ;
         description : String }
```

子类（图 4）：`Project, Commit, CommitReference (≤ Tag, Branch), DataVersion, DataIdentity, Query`。Data 是接口，被 `Element, ExternalData, ExternalRelationship, ProjectUsage` 实现（§7.1.2 图 5）。

### 16.1 Project Data Versioning（§7.1.2）

- `Project` 拥有 `commits[*]`、`branches[1..*]`（其中 `defaultBranch[1] subsets branches`）、`tags[*]`。
- `Commit { created : ISO8601DateTime; previousCommits[*] }` —— 不可变快照；Commit 通过 `change : Change[1..*]` 引用一组 `DataVersion`。
- `Branch :> CommitReference`、`Tag :> CommitReference`，二者都子集 `commitReferences`，区别仅是 Branch 可移动，Tag 通过 `redefines referencedCommit` 固定。
- `DataIdentity` 表示元素跨版本身份，`createdAt/deletedAt` 引用其首末次出现的 `Commit`。
- `DataVersion` 表示某 `DataIdentity` 在某 `Commit` 处的具体载荷。

### 16.2 Commit 不可变性

API §7.1.2 明确：

> *"`Project.commits` 通过 `previousCommits` 形成 DAG；`Commit` 的 `change` 一旦写入即只读。"*

`getCommits / getCommitById / getCommitChange / diffCommits` 都标 `isQuery=true`（§2 ProjectDataVersioningService Read Conformance），不允许修改；唯一的 commit 写操作是 `createCommit`（§7.2.3 表）。

### 16.3 全局标识：UUID v5 强制要求

KerML §9.1 规定 **标准库 Element 的 UUID 必须用 name-based UUID v5**：

- 命名空间 = NameSpace_URL UUID `6ba7b811-9dad-11d1-80b4-00c04fd430c8`
- name = `https://www.omg.org/spec/KerML/<package path>` 用 UTF-8 编码
- 算法 = SHA-1 (RFC 4122 §4.3)

这意味着**标准库在所有实现中 UUID 必须一致**——这是跨工具互操作的硬约束。任何"自己实现 v2"的工具，对 `Base::Anything`、`Occurrences::Occurrence` 等元素必须复现完全相同的 UUID，否则跨实现 API 协议会失败。

### 16.4 服务清单（§2 / §7.2）

- `ProjectService`（getProjectById, getProjects, …）
- `ElementNavigationService`（getElementById, getRoots, …）
- `ProjectDataVersioningService`（createCommit, getCommits, getHeadCommit, getBranches, getTags, diffCommits, …）
- `QueryService`（executeQuery, …）
- `ExternalRelationshipService`
- `ProjectUsageService`（getProjectUsages）

PSM 绑定：REST/HTTP（OpenAPI）+ OSLC 3.0（§8）。Pilot 实现仓库为 [Systems-Modeling/SysML-v2-API-Services][^repo-api-services]，REST 端点路径形如 `/projects/{id}/commits/{cid}/elements/{eid}`。

**API 规范级缺陷**：没有 merge 端点 / 三方合并 / 冲突解决——所有商用与开源工具的 merge 实现都是私有不兼容的。详见 [06-可视化与协作](06-visualization-collaboration.md) §2.2。

## 17 SysML v1 ↔ v2 转换：现状与本地验证

> 本节回答用户最关心的问题：「SysML v1 与 v2 之间是否存在稳定的相互转换？具体怎么转？」

### 17.1 OMG Part 2 Transformation 规范的实际内容

OMG **SysML 2.0 Part 2: Transformation**（`formal/26-03-03`）[^omg-sysml-transform]给出从 SysML v1（含 UML profile + XMI）到 SysML v2 的元模型映射规则。规范明确：

1. **方向**：单向 v1 → v2。规范**没有**定义 v2 → v1 的反向转换。原因是 v2 的概念表达力严格大于 v1（例如 Specialization-on-Feature、Variation 是 v1 中没有的元层概念），反向转换需要丢信息。
2. **粒度**：元模型级 + 实例级映射规则均给出，例如：
   - UML `Class` 带 `«block»` 立体型 → SysML v2 `PartDefinition`
   - UML `Property` 带 `«property»` → SysML v2 `AttributeUsage`
   - UML `Connector` → SysML v2 `ConnectionUsage`
   - SysML v1 `Requirement` → SysML v2 `RequirementDefinition`
3. **明文承认 reference implementation 缺失**：规范在引言段直接写「reference implementation not currently available」。

### 17.2 DoD CTO 蓝图

美国国防部 OUSD(R&E) 在 2024-03 发布的 *SysML v1 to SysML v2 Model Conversion Approach* v1.3[^dod-cto] 提供大型武器系统迁移的实施蓝图，但同样仅是**方法学指南**，无配套代码。

### 17.3 Pilot 中 `org.omg.sysml.uml.ecore.importer` 的真实功能（本地核查）

Pilot Implementation 有一个 `org.omg.sysml.uml.ecore.importer` 模块（4 个 Java 文件），网络上常被误认为"v1→v2 转换器"。**本地核查（2026-05-05）确认这是误解**。

核查方法：

```bash
# 1. 浅克隆 Pilot
cd /tmp && git clone --depth 1 \
  https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation.git pilot

# 2. 查看模块文件
ls pilot/org.omg.sysml.uml.ecore.importer/src/org/omg/sysml/uml/ecore/importer/
# CustomUMLImporter.java
# CustomUML2EcoreConverter.java
# ui/CustomUMLImporterWizard.java
# ui/CustomUMLImporterDetailPage.java

# 3. 读 CustomUMLImporter.java 的关键注释
head -25 pilot/org.omg.sysml.uml.ecore.importer/src/org/omg/sysml/uml/ecore/importer/CustomUMLImporter.java
```

源码注释明文写着[^pilot-importer]：

```java
/*
 * MOSTLY FORKED FROM org.eclipse.uml2.uml.ecore.importer.UMLImporter except for
 * the UML2Ecore transformation
 * (org.omg.sysml.uml.ecore.importer.CustomUMLImporter.doComputeEPackages().new
 * UML2EcoreConverter() {...}.() switched with ours)
 */
```

且类继承结构是 `CustomUMLImporter extends org.eclipse.uml2.uml.ecore.importer.UMLImporter`。这是 **Eclipse UML2 项目的 UMLImporter 的 fork**——其原本目的是把 UML 元模型（`.uml` / `.xmi` / `.cmof`）**转成 Ecore 元模型**，供 EMF 代码生成器使用。

模块的 `plugin.xml` 显示它注册的是 `org.eclipse.emf.importer.modelImporterDescriptors`：

```xml
<modelImporterDescriptor
   id="org.omg.sysml.uml.ecore.importer"
   extensions="uml,UML,uml2,UML2,xmi,XMI,cmof,CMOF"
   description="%_UI_UMLModelImporter_description"
   wizard="org.omg.sysml.uml.ecore.importer.ui.CustomUMLImporterWizard"/>
```

——它注册成 EMF 的 *model importer*，目的是被 OMG / Pilot 开发者用来在 Pilot **首次构建过程中**把 SysML v2 抽象语法（在 OMG 规范中以 UML profile 形式给出）导入为 Ecore 文件，再用 EMF 生成 Java 代码。

源码中的关键常量：

```java
private static final String SYSML_URI    = "https://www.omg.org/spec/SysML/20230201";
private static final String BASE_PACKAGE = "org.omg.sysml.lang";
private static final String TYPES_URI    = "https://www.omg.org/spec/UML/20161101/PrimitiveTypes";
```

`SYSML_URI` 指向 SysML v2 spec 命名空间；`BASE_PACKAGE` 是 Pilot 生成 Ecore 类的 Java 包名；`TYPES_URI` 是 UML 2 PrimitiveTypes（Boolean / Integer / String 等）。这些都是元模型层面的常量。

**搜索整个 Pilot 仓库的 v1 / 迁移相关字符串均为空命中**：

```bash
$ grep -ri "sysml.*v1\|migration\|migrate\|sysmlv1\|legacy.*sysml" /tmp/pilot \
    --include='*.java' --include='*.md' 2>&1 | head
# (no output)
```

**结论**：`org.omg.sysml.uml.ecore.importer` **不是 v1 用户模型迁移工具**，而是 Pilot 自身构建过程中把 SysML v2 抽象语法（UML profile 形式）导入 Ecore 的元模型工具。**Pilot 仓库内不存在 v1 → v2 实例迁移代码**。

### 17.4 本地 Pilot 构建工具链验证

为确认本仓库的环境可在未来需要时支持 Pilot 编译运行，本调研在本地完成了 Java 21 + Maven 3.9.6 的安装与 Pilot Maven Wrapper 的验证：

```bash
# 安装 Oracle JDK 21
$ cd /tmp && curl -sL -o jdk21.tgz \
  "https://download.oracle.com/java/21/latest/jdk-21_linux-x64_bin.tar.gz" && \
  tar xzf jdk21.tgz

$ /tmp/jdk-21.0.11/bin/java -version
java version "21.0.11" 2026-04-21 LTS
Java(TM) SE Runtime Environment (build 21.0.11+9-LTS-211)

# 在 Pilot 目录启动 Maven Wrapper（自动下载 Maven 3.9.6）
$ cd /tmp/pilot && \
  JAVA_HOME=/tmp/jdk-21.0.11 PATH=/tmp/jdk-21.0.11/bin:$PATH ./mvnw --version
Apache Maven 3.9.6
Maven home: /home/zhangshaoang/.m2/wrapper/dists/apache-maven-3.9.6-bin/3311e1d4/apache-maven-3.9.6
Java version: 21.0.11, vendor: Oracle Corporation, runtime: /tmp/jdk-21.0.11
OS name: "linux", version: "6.17.0-22-generic", arch: "amd64"
```

完整 Pilot 编译需要 Eclipse Modeling Tools 2025-12 运行时（Tycho Maven 模式下还需 Eclipse target platform），不在本节验证范围内——但工具链基础已就绪。**本节核心结论的成立不依赖完整编译**，因为 Pilot 仓库中**没有 v1 → v2 实例迁移代码**这一点已通过源码 grep 排除。

### 17.5 商用工具

| 厂商 | 工具 | 状态 |
|---|---|---|
| Dassault Systèmes | **Cameo / CATIA No Magic 2026x SysMLv2 Plugin** 内置 *SysML v1 → v2 Migration* 功能 | **闭源**，商用 license[^vendor-cameo] |
| Sensmetry | *Syside Editor* + 咨询服务 | 闭源；DETECT 案例研究[^sensmetry-detect]披露其迁移过程**主要为人工**，自动化覆盖率偏低 |
| Visual Paradigm | *SysML v2 Studio* | 闭源[^vendor-vp] |
| IBM | *Rhapsody Systems Engineering* | 闭源；与 Cameo 配套迁移[^vendor-ibm] |

### 17.6 第三方开源尝试

GitHub 搜索 `sysml v1 v2 migration` / `sysml v1 to v2`：

- 未发现成熟的开源 v1 → v2 转换工具。
- 个别学位项目使用 daltskin 风格的 ANTLR4 grammar 做单方向解析，但**均不实现 v1 输入 → v2 输出**的迁移功能。
- ATL / QVT / Henshin / VIATRA 在 KerML 上的转换绑定全部空白。

### 17.7 结论

| 主张 | 状态 |
|---|---|
| **稳定双向 OSS 转换** | **不存在** |
| **稳定 v1 → v2 OSS 转换** | **不存在**——只有 OMG 规范与 DoD 蓝图，无 reference 实现 |
| **稳定 v2 → v1 转换**（任何形式） | **不存在**——OMG 规范本身只定义 v1 → v2 单向 |
| **商用 v1 → v2 工具** | 存在（Cameo 2026x 等），但闭源、自动化率有限 |
| **现实可行迁移路径** | 手工 + Sensmetry blog 经验 + Cameo 商业插件半自动；自动化率随 v1 模型复杂度反比例下降 |

如本仓库读者计划做 v1 → v2 迁移，建议路径：

1. 先用 Cameo 2026x 的 *SysML v1 → v2 Migration* 功能跑首轮（**有 license 的话**）。
2. 复核 Sensmetry [DETECT 案例][^sensmetry-detect]披露的人工修复模式（`requirement` / `analysis` / 一些 stereotype 没有干净的 v2 对等物）。
3. 用 OMG Part 2 Transformation 规范作为映射规则的 ground truth，对 Cameo 输出做检查。
4. **不要**寄望开源 reference implementation 短期内出现——这是 [09-缺口与机会](09-gaps-opportunities.md) §B.5 标记的中期机会窗口。

## 18 RTF 修订与版本管理

OMG 在 Final Adoption 之后进入 **Revision Task Force (RTF)** 周期：

- 业务模式：每 1–2 年发布一次 minor 修订（如 1.0.1、1.1）。
- 修订内容：Issue 库（OMG 公开 Jira）汇集的 spec bug、术语统一、向后兼容的语义澄清。
- 对工具链影响：Pilot Implementation 通常滞后 RTF 修订 3–6 个月；商用厂商滞后 6–12 个月。

本仓库引用规范条款时应以**截至维护日期最新的 spec 版本号**为准，见 [AGENTS.md §7](../AGENTS.md)。

## 参考文献

[^omg-final-2025]: OMG. *Final Adoption: SysML v2.0, KerML v1.0, Systems Modeling API & Services v1.0*. 2025-07-21 公告. <https://www.omg.org/news/releases/pr2025/07-21-25.htm>

[^omg-formal-2026]: OMG. *2026-03 Formal Publication of KerML 1.0, SysML 2.0 Language, Transformation, and API & Services 1.0*. 文档号 `formal/26-03-01..04`. <https://www.omg.org/sysml/sysmlv2/>

[^omg-kerml]: OMG. *Kernel Modeling Language (KerML) 1.0*. <https://www.omg.org/spec/KerML/1.0>

[^omg-sysml-language]: OMG. *SysML 2.0 Part 1: Language Specification*. <https://www.omg.org/spec/SysML/2.0/Language>

[^omg-sysml-transform]: OMG. *SysML 2.0 Part 2: Transformation Specification*. <https://www.omg.org/spec/SysML/2.0/Transformation>

[^omg-api]: OMG. *Systems Modeling API & Services 1.0*. <https://www.omg.org/spec/SystemsModelingAPI/1.0>

[^omg-beta1]: OMG. *SysML v2 Beta1 (Historical)*. <https://www.omg.org/spec/SysML/2.0/Beta1/About-SysML>

[^omg-beta2]: OMG. *SysML v2 Beta2 (Historical)*. <https://www.omg.org/spec/SysML/2.0/Beta2/About-SysML>

[^repo-release]: *Systems-Modeling/SysML-v2-Release*. <https://github.com/Systems-Modeling/SysML-v2-Release>（含 `doc/` PDF 镜像、`bnf/` KEBNF/KGBNF 源、`sysml/src/{examples,training,validation}` 范例）

[^pilot-ecore]: Pilot 元模型 Ecore：[`org.omg.sysml/model/SysML.ecore`](https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.sysml/model/SysML.ecore) 与 `KerML.ecore`。

[^pilot-grammar]: Pilot SysML grammar 头：[`org.omg.sysml.xtext/src/org/omg/sysml/xtext/SysML.xtext`](https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.sysml.xtext/src/org/omg/sysml/xtext/SysML.xtext)

[^pilot-jupyter]: Pilot Jupyter kernel 中 `%show` 与 `%viz` magic 用于探索元模型继承链与图形化。<https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/tree/master/org.omg.sysml.jupyter.kernel>

[^training-04]: `04. Subsetting/Subsetting Example.sysml`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/sysml/src/training/04.%20Subsetting/Subsetting%20Example.sysml>

[^training-05]: `05. Redefinition/Redefinition Example.sysml`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/sysml/src/training/05.%20Redefinition/Redefinition%20Example.sysml>

[^kerml-occurrences]: `Occurrences.kerml`. <https://raw.githubusercontent.com/Systems-Modeling/SysML-v2-Pilot-Implementation/master/sysml.library/Kernel%20Libraries/Kernel%20Semantic%20Library/Occurrences.kerml>

[^training-23]: `23. State Definitions/State Definition Example-1.sysml`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/sysml/src/training/23.%20State%20Definitions/State%20Definition%20Example-1.sysml>

[^training-09]: `09. Connections/Connections Example.sysml`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/sysml/src/training/09.%20Connections/Connections%20Example.sysml>

[^training-13]: `13. Flows/Flow Usage Example.sysml`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/sysml/src/training/13.%20Flows/Flow%20Usage%20Example.sysml>

[^training-32]: `32. Requirements/Requirement Definitions.sysml`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/sysml/src/training/32.%20Requirements/Requirement%20Definitions.sysml>

[^pilot-requirements]: `Systems Library/Requirements.sysml`. <https://raw.githubusercontent.com/Systems-Modeling/SysML-v2-Pilot-Implementation/master/sysml.library/Systems%20Library/Requirements.sysml>

[^training-38]: `38. Allocation/Allocation Definition Example.sysml`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/sysml/src/training/38.%20Allocation/Allocation%20Definition%20Example.sysml>

[^training-30]: `30. Calculations/Calculation Definitions.sysml`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/sysml/src/training/30.%20Calculations/Calculation%20Definitions.sysml>

[^training-36]: `36. Variability/Variation Definitions.sysml`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/sysml/src/training/36.%20Variability/Variation%20Definitions.sysml>

[^pilot-views]: `Systems Library/Views.sysml`. <https://raw.githubusercontent.com/Systems-Modeling/SysML-v2-Pilot-Implementation/master/sysml.library/Systems%20Library/Views.sysml>

[^training-39]: `39. Metadata/Metadata Example-1.sysml`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/sysml/src/training/39.%20Metadata/Metadata%20Example-1.sysml>

[^kerml-base]: `Base.kerml`. <https://raw.githubusercontent.com/Systems-Modeling/SysML-v2-Pilot-Implementation/master/sysml.library/Kernel%20Libraries/Kernel%20Semantic%20Library/Base.kerml>

[^repo-bnf]: `Systems-Modeling/SysML-v2-Release/bnf/`：[KerML-textual-bnf.kebnf](https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/bnf/KerML-textual-bnf.kebnf)、[SysML-textual-bnf.kebnf](https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/bnf/SysML-textual-bnf.kebnf)、SysML-graphical-bnf.kgbnf

[^repo-daltskin-grammar]: *daltskin/sysml-v2-grammar*. <https://github.com/daltskin/sysml-v2-grammar>

[^repo-api-services]: *Systems-Modeling/SysML-v2-API-Services*. <https://github.com/Systems-Modeling/SysML-v2-API-Services>

[^dod-cto]: U.S. OUSD(R&E). *SysML v1 to SysML v2 Model Conversion Approach v1.3*. 2024-03. <https://www.cto.mil/wp-content/uploads/2025/02/SysML-v2-TransitionApproach-1.3.pdf>

[^pilot-importer]: Pilot 中 `CustomUMLImporter.java` 源文件（直接从 fork 注释可见其继承自 Eclipse UML2 项目的 UMLImporter）。<https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.sysml.uml.ecore.importer/src/org/omg/sysml/uml/ecore/importer/CustomUMLImporter.java>

[^vendor-cameo]: Dassault. *CATIA SysML v2 Solution Docs*（含 v1 → v2 Migration 章节）. <https://docs.nomagic.com/spaces/CATIA/pages/261619716/CATIA+SysML+v2+Solution>

[^sensmetry-detect]: Sensmetry. *DETECT Case Study: SysML v1 → v2 Migration Lessons Learned*. <https://sensmetry.com/sysml-v1-to-sysml-v2-migration-of-detect-benefits-lessons-learned/>

[^vendor-vp]: Visual Paradigm. *SysML v2 Studio*. <https://updates.visual-paradigm.com/releases/sysml-v2-studio-competitive-advantages-launch/>

[^vendor-ibm]: IBM. *Rhapsody Systems Engineering*. <https://www.ibm.com/products/rhapsody-systems-engineering>
