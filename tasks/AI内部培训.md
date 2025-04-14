# AI 内部培训任务

## 第一期 AI 的基本原理 【已完成】

## 第二期 AI Agent

一、理论讲解（25分钟）‌
‌1. AI Agent核心原理（15分钟）‌
‌定义与核心能力‌（5分钟）
• ‌一句话定义‌：
“感知环境→处理信息→自主决策→执行动作的智能体”
• ‌能力拆解‌（结合生活场景）：
▸ 客服Agent：语音识别（耳）→ 意图理解（脑）→ 调取知识库（记忆）→ 回复话术（嘴）
▸ 物流调度Agent：GPS定位（眼）→ 路况分析（脑）→ 路径规划（策略）→ 发送指令（手）

‌技术架构‌（7分钟）
① ‌输入层‌：多模态感知（语音/图像/文本）→ 示例：微信语音转文字API
② ‌推理层‌：大模型决策（GPT-4/Claude）→ 示例：用Chain-of-Thought提示词实现分步推理
③ ‌执行层‌：API工具调用 → 示例：钉钉消息自动发送接口
‌※ 可视化架构图‌：
[用户输入] → [意图识别模块] → [数据库查询] → [决策引擎] → [API执行] → [反馈结果]

‌应用场景边界‌（3分钟）
• ‌擅长领域‌：规则明确、数据可获取的重复性任务（合同审查/数据报表）
• ‌慎用场景‌：需人类价值观判断的决策（裁员名单生成/医疗诊断）

‌2. 企业级Agent价值（10分钟）‌
‌ROI测算案例‌
• 某电商公司客服Agent：
▸ 人力成本：原10人团队 → 缩减至3人（处理复杂问题）
▸ 效率提升：工单处理时间从15分钟→2分钟（自动化知识库检索）
• ‌价值公式‌：
年节省成本 = (原人力成本 - 现人力成本) + (工单量 × 单次节省时间 × 时薪)  

‌失败案例警示‌
• 某银行风控Agent误判贷款申请：因训练数据未覆盖小微企业特征，导致拒绝率异常升高
• ‌避坑指南‌：上线前必须进行小样本AB测试

‌二、实操演练（35分钟）‌
案例：会议纪要Agent
业务流程
结合Agent架构说明会议纪要Agent
实操1：通过Coze平台搭建会议纪要Agent
‌工具准备‌：Coze平台（预置企业账号）、会议录音文件样例
‌步骤演示‌：
‌创建Bot‌（2分钟）
• 登录Coze → 新建Bot → 命名「智能会议助手」
‌配置技能‌（5分钟）
• 上传录音文件 → 调用语音转文字API（演示参数设置）
• 编写Prompt：
"角色：资深秘书  
任务：  

1. 提取会议中的【关键决策】和【待办事项】  
2. 按部门分类（技术/市场/财务）  
3. 过滤闲聊内容（如'中午吃什么'）  
4. 输出Markdown表格，包含负责人、截止时间、交付物"  

如果信息不明确：

- 时间不明确时标注「需确认」
- 负责人未提及时标注「待指定」

‌部署测试‌（5分钟）
• 上传测试录音 → 查看输出结果 → 修正Prompt
• 部署至钉钉群：设置触发关键词「@会议总结」

实操2：代码实现的会议纪要Agent开发方案（Python版）‌
工具准备：python开发环境、通义千问API
‌一、技术架构设计‌
mermaid
Copy Code
graph TD
    A[原始录音文件] --> B(语音转文本模块)
    B --> C{文本预处理}
    C --> D[会议内容结构化]
    D --> E(Markdown报告生成)
    E --> F[钉钉/飞书通知]
‌二、核心代码实现‌
‌1. 语音转文本模块（使用OpenAI Whisper API）‌
python
Copy Code
import openai

def audio_to_text(audio_path):
    """将会议录音转换为文字（支持mp3/wav格式）"""
    try:
        with open(audio_path, "rb") as audio_file:
            transcript = openai.Audio.transcribe(
                file=audio_file,
                model="whisper-1",
                response_format="verbose_json",
                timestamp_granularities=["segment"]
            )
        return {
            "text": transcript.text,
            "segments": [{"start": s["start"], "end": s["end"], "text": s["text"]}
                       for s in transcript.segments]
        }
    except Exception as e:
        print(f"语音识别失败: {str(e)}")
        return None
‌2. 会议内容结构化处理（GPT-4-Turbo增强版）‌
python
Copy Code
def analyze_meeting_content(full_text):
    """使用大模型提取关键信息"""
    prompt = """
    你是一位专业的企业秘书，请从以下会议录音文本中：
    1. 识别出所有【决策事项】和【待办任务】
    2. 按部门分类（技术部/市场部/财务部）
    3. 提取每个任务的负责人（用@标记）、截止时间、交付物
    4. 过滤与工作无关的闲聊内容

    输出要求：
    - 使用JSON格式
    - 时间统一转换为YYYY-MM-DD格式
    - 如果未明确时间，默认设置为3个工作日后
    """
    
    response = openai.ChatCompletion.create(
        model="gpt-4-turbo",
        messages=[{"role": "user", "content": f"{prompt}\n\n{full_text}"}],
        response_format={"type": "json_object"}
    )
    
    return json.loads(response.choices.message.content)
‌3. 自动化通知（钉钉机器人集成）‌
python
Copy Code
import requests
import json

def send_to_dingtalk(content, webhook_url):
    """发送Markdown格式通知到钉钉群"""
    headers = {"Content-Type": "application/json"}
    data = {
        "msgtype": "markdown",
        "markdown": {
            "title": "会议纪要生成通知",
            "text": f"&zwnj;**自动生成的会议纪要**&zwnj;\n\n{content}"
        }
    }

    response = requests.post(webhook_url, 
                           data=json.dumps(data), 
                           headers=headers)
    return response.status_code == 200
‌三、完整执行流程‌
python
Copy Code

# 配置参数

OPENAI_API_KEY = "sk-xxx"
DINGTALK_WEBHOOK = "<https://oapi.dingtalk.com/robot/send?access_token=xxx>"

# 主程序

def main(audio_path):
    # 步骤1：语音转文字
    transcript = audio_to_text(audio_path)
    if not transcript:
        return False

    # 步骤2：结构化处理
    structured_data = analyze_meeting_content(transcript["text"])
    
    # 步骤3：生成Markdown
    md_content = """
    ## 会议纪要自动化报告
    
    ### 决策事项
    {decisions}
    
    ### 待办任务
    {tasks}
    """.format(
        decisions="\n".join([f"- {d['content']}" for d in structured_data["decisions"]]),
        tasks="\n".join([f"- {t['department']} @{t['owner']} | {t['deadline']} | {t['deliverable']}" 
                       for t in structured_data["tasks"]])
    )
    
    # 步骤4：发送通知
    return send_to_dingtalk(md_content, DINGTALK_WEBHOOK)

> 25.04.07 PPT 完成创作
> 25.04.08 实践案例熟悉
> 25.04.09 晚上试讲
> 25.04.10 优化

## 第三期 AI 工作流

## 第四期 AI 知识库 RAG

## 补充子任务 - Transformer 算法深入浅出讲解

## 补充子任务 - 大模型如何训练，数据如何采集，数据为何要规范化，以及如何规范化

## 推进公司内部 AI 学习氛围

## AI 助手环境搭建

- [ ] OmniParser 搭建
- [ ] 搭建编码助手 Cursor 或者 Github
