# 全局文件存放约定 (File Location Conventions)

本文档是 OLYD 系统的「最高地图」，定义所有文件夹用途及对应的 Agent。

---

## `meta/` — 系统宪法

- **用途**: 存放描述本系统运作机制的全局规约文件。
- **核心文件**: `my-core-principles.md`, `task-abc.md`, `file-location.md`
- **管理**: 人工维护

---

## `inbox/` — 快速捕捉区

- **用途**: 存放所有未经处理的临时想法、链接和碎片信息。
- **核心原则**: **定期清空**，不要当成待办列表。
- **对应 Agent**: `olyd.inbox` (写入) → `olyd.task` (转化为任务卡片)
- **文件模板**: `[[.foam/templates/0_inbox]]`

---

## `specs/` — 战略规约

- **用途**: 存放 OLYD 系统的 Source of Truth — 全维度战略规约。
- **核心文件**: `entity_definition.md`
- **对应 Agent**: `olyd.init` (唯一写入者)
- **读取者**: `olyd.year`, `olyd.week`, `olyd.review`

---

## `journal/` — 时间驾驶舱

- **用途**: 存放所有与时间相关的计划与复盘文件。
- **结构**: `journal/YYYY/year-plan.md`, `journal/YYYY/MM-month-plan.md`, `journal/YYYY/Week-XX/plan.md`, `journal/YYYY/Week-XX/YYYY-MM-DD.md`
- **对应 Agent**: `olyd.year`, `olyd.month`, `olyd.week`, `olyd.review`

---

## `tasks/` — 任务卡片看板

- **用途**: 以看板形式管理所有可执行任务卡片的生命周期。
- **结构**: `tasks/0_todo/`, `tasks/1_doing/`, `tasks/2_done/`
- **对应 Agent**: `olyd.task` (唯一写入者)
- **文件模板**: `[[.foam/templates/1_task]]`

---

## `projects/` — 项目管理

- **用途**: 存放有明确终点和交付成果的项目记录，含阶段、里程碑、进度。
- **对应 Agent**: `olyd.project`
- **文件模板**: `project-record`

---

## `areas/` — 责任领域

- **用途**: 存放没有终点的个人责任领域 (健康、家庭、职业)，作为 SOP 中心。
- **管理**: 人工维护 + `olyd.init` 访谈时参考

---

## `habits/` — 习惯引擎

- **用途**: 循环/定时习惯的打卡、streak、积分账本。
- **对应 Agent**: `olyd.habit`
- **文件模板**: `habit-card`

---

## `notes/` — 知识卡片系统

- **对应 Agent**: `olyd.note`
- **文件模板**: `note-card`, `deck-index`

---

## `study/` — 学习计划

- **用途**: 考试/课程学习计划、阶段拆解、学习日志。
- **对应 Agent**: `olyd.study`
- **文件模板**: `study-plan`

---

## `archive/` — 归档区

- **用途**: 存放已完成/终止/搁置的项目与笔记。
