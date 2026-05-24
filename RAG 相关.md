# 一：概览
## 为什么要用RAG？-- 大模型幻觉


<font style="color:rgb(31, 35, 40);">大模型有时⁢⁢⁢会 “自信满满‏地‏胡‏说八道”，‍这就‍是大‍模型的⁠经典问⁠题 ——‌⁠ 幻觉。</font>

<font style="color:rgb(31, 35, 40);"></font>

<font style="color:rgb(31, 35, 40);">这些幻觉主要有三种表现形式：</font>

1. <font style="color:rgb(31, 35, 40);">事实性幻觉：生成与事实不符的内容（如错误的日期、人物关系等）。比如 “张三发明了计算器”</font>
2. <font style="color:rgb(31, 35, 40);">逻辑性幻觉：推理过程存在逻辑错误，得出不合理的结论。比如 “1 + 1 = 3”</font>
3. <font style="color:rgb(31, 35, 40);">自洽性幻觉：生成内容自身存在矛盾。比如 “我很年轻，才 80 岁”</font>

<font style="color:rgb(31, 35, 40);"></font>

<font style="color:rgb(31, 35, 40);">为什么会出现幻觉呢？</font>

1. <font style="color:rgb(31, 35, 40);">知识限制：模型的训练数据中可能包含错误或过时的信息或者未公开信息；</font>
2. <font style="color:rgb(31, 35, 40);">大语言模型本质上是 预测下一个词的概率 模型，它们倾向于生成流畅而未必准确的内容。更重要的是，模型并不真正 “知道” 什么，它只是学会了文本的统计模式。</font>
3. <font style="color:rgb(31, 35, 40);">上下文长度限制：注意力稀释</font>

<font style="color:rgb(31, 35, 40);"></font>

<font style="color:rgb(31, 35, 40);">如何减少这种幻觉呢？</font>

1. <font style="color:rgb(31, 35, 40);">通过引入‏‏‏外部知识源，我们可以让模型‍‍‍不再完全依赖其参数中存储的⁠⁠⁠信息，而是基于检索到的最新‌‌‌、准确的信息来回答问题。这就用到</font>**<font style="color:rgb(31, 35, 40);">RAG技术</font>**<font style="color:rgb(31, 35, 40);">。</font>
2. <font style="color:rgb(31, 35, 40);">此外，还有其他减轻幻⁢⁢⁢觉的方法，比如</font>**<font style="color:rgb(31, 35, 40);">提示工程优化</font>**<font style="color:rgb(31, 35, 40);">，可以采用“‏‏‏思维链”提高推理透明度，通过引导模型一‍‍‍步步思考，我们能够更好地观察其推理过程⁠⁠⁠，及时发现可能的错误。很多 Agent‌‌‌ 超级智能体都会采用这种模式：</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/12814253/1779611137294-5f83924b-a533-414a-919d-87afe1baa69d.png)

3. <font style="color:rgb(31, 35, 40);">此外，我们还可以使用 </font>**<font style="color:rgb(31, 35, 40);">事实验证模型</font>**<font style="color:rgb(31, 35, 40);"> 检查生成内容的准确性，建立关键信息的自动核查机制，或实施</font>**<font style="color:rgb(31, 35, 40);">人机协作</font>**<font style="color:rgb(31, 35, 40);">的审核流程。评估幻觉程度的指标包括事实一致性、引用准确性和自洽性评分。通过上面的方法，我们能够大幅减轻大模型幻觉，提供更可靠的 AI 使用体验。</font>



## <font style="color:rgb(31, 35, 40);">RAG的定义</font>
RAG（检索增强生成），指的就是一种融合了检索（retrieval)和生成（generation)的自然语言处理方法，旨在提升LLM在特定任务上的表现。

RAG是基于LLM的扩展，用于提供更准确、更全面的输出。RAG结合了传统信息检索技术和最新的生成式模型，从大型知识库中检索与查询最相关的信息，基于重排序的信息生成回答。

RAG是一种方法，在将提示词（promot）发送给LLM之前，先从数据中找到并注入相关信息片段。



## RAG 模型架构
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/12814253/1779632245604-7439ed10-6ef3-4d21-b803-4795a63dd62e.png)

## 典型的RAG 五个阶段


<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/12814253/1779612034232-684ed5b2-e3e4-4843-96bd-b1cb8e74e0b3.png)



1. **<font style="color:rgb(34, 34, 34);">加载：</font>**<font style="color:rgb(34, 34, 34);"> 从多源异构数据源中执行ETL操作，将原始非结构化文档（如PDF、HTML）提取并转化为统一的文档对象格式。</font>
2. **<font style="color:rgb(34, 34, 34);">索引：</font>**<font style="color:rgb(34, 34, 34);"> 利用嵌入模型将文档经过清洗、分块处理后转化为高维向量嵌入，建立语义检索所需的向量索引结构。</font>
3. **<font style="color:rgb(34, 34, 34);">存储：</font>**<font style="color:rgb(34, 34, 34);"> 将处理后的文本块及其对应的向量表示持久化写入向量数据库，以支持高维空间内的相似度计算与高效检索。</font>
4. **<font style="color:rgb(34, 34, 34);">查询：</font>**<font style="color:rgb(34, 34, 34);"> 执行语义检索以召回与用户提示相关的上下文片段，并将其注入提示词模板，驱动大语言模型进行条件文本生成。</font>
5. **<font style="color:rgb(34, 34, 34);">评估：</font>**<font style="color:rgb(34, 34, 34);"> 利用自动化基准测试框架（如RAGAS）或人工反馈，对检索内容的相关性及生成结果的忠实度与准确性进行量化分析</font>



<font style="color:rgb(34, 34, 34);">好比你在图书馆查资料写论文：</font>

1. **<font style="color:rgb(34, 34, 34);">加载：</font>**<font style="color:rgb(34, 34, 34);"> </font><font style="color:rgb(34, 34, 34);">图书馆买进了一堆新书。</font>
2. **<font style="color:rgb(34, 34, 34);">索引：</font>**<font style="color:rgb(34, 34, 34);"> </font><font style="color:rgb(34, 34, 34);">图书管理员把书拆开，给每一页贴上关键词标签，并记录在卡片柜里。</font>
3. **<font style="color:rgb(34, 34, 34);">存储：</font>**<font style="color:rgb(34, 34, 34);"> </font><font style="color:rgb(34, 34, 34);">把这些书和卡片柜整齐地摆放在书架上。</font>
4. **<font style="color:rgb(34, 34, 34);">查询：</font>**<font style="color:rgb(34, 34, 34);"> </font><font style="color:rgb(34, 34, 34);">你（用户）问图书管理员一个问题，管理员去卡片柜迅速找到相关的书页，读完后总结给你听。</font>
5. **<font style="color:rgb(34, 34, 34);">评估：</font>**<font style="color:rgb(34, 34, 34);"> 你听完回答后，判断管理员说得对不对，或者馆长定期检查管理员的工作质量。</font>

<font style="color:rgb(31, 35, 40);"></font>

# <font style="color:rgb(31, 35, 40);">二：技术实现</font>


## QAnything
QAnything 项目基于 PYthon 实现了完整的RAG。

### 源码阅读建议
源码：[https://qanything.ai/](https://qanything.ai/)



找到基于RAG的QA功能入口：

1. 找到入口文件`QAnything-qanything-v2\docker-compose-win.yaml`
2. 找到执行脚本
    1. `command: /bin/bash-c "cd /workspace/QAnything && bash scripts/entrypoint.sh"`
3. 找到启动的服务
    1. `qanything_Kernel/qanything_server/sanic_api.py`
4. 找到配置文件
    1. `QAnything-qanything-v2\qanything_kernel\qanything_server\sanic_api.py`
    2. <!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/12814253/1779626568579-612be46a-82ed-4130-8d71-1a628b74445a.png)
5. 找到实现方法
    1. API处理层，处理HTTP请求并调用核心业务逻辑:  
`QAnything-qanything-v2\qanything_kernel\qanything_server\handler.py`
    2. 核心业务逻辑层，包含 LocalDocQA 类:  
`QAnything-qanything-v2\qanything_kernel\core\local_doc_qa.py`
    3. 核心函数  
`get_knowledge_based_answer()`



### RAG 流程图
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/12814253/1779192215271-c368aafc-0066-41e3-9a1f-388b57141c41.png)



---

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/12814253/1779626895569-9f052ea3-9e11-45c5-9d90-1ea72d7418e3.png)



### 优化策略


+ 混合检索
+ 两段式rerank





## Spring 生态


SpringAI  和  SpringAI Alibaba 提供了框架实现，简化了Java开发

官方文档：[https://docs.spring.io/spring-ai/reference/1.0/api/retrieval-augmented-generation.html](https://docs.spring.io/spring-ai/reference/1.0/api/retrieval-augmented-generation.html)

[https://java2ai.com/integration/rag/retrieval-augmented-generation](https://java2ai.com/integration/rag/retrieval-augmented-generation)





##  LlamaIndex 


PYthon 实现 RAG 可以用 LlamaIndex 框架



# 三：RAG 优化策略


## 数据清洗
### 借助大模型实现数据清洗
提示词示例：

```python
PROMPT_TEMPLATES = {
    "optimize": """
你是一名专业的文档预处理专家，专注于为 RAG 知识库构建提供高质量、结构化的文本输入。请对以下文档内容进行系统性清洗与标准化处理，确保其语义完整、格式统一、结构清晰。

### 处理准则

1. **去噪净化**
   - 移除广告、页眉页脚、水印、版权说明、免责声明等非核心信息
   - 清理装饰性符号、冗余空行及特殊控制字符
   - 过滤重复或无关的页面元素（如导航栏、页码）

2. **格式标准化**
   - 统一使用 UTF-8 编码
   - 中文文档：采用标准中文标点（如“。”、“，”），避免中英混用
   - 英文部分：统一大小写规则（专有名词、缩写保留原格式）
   - 规范空格使用：去除多余空白，段落间保留单空行
   - 全角/半角符号统一（依主语言风格调整）

3. **内容修复**
   - 修正 OCR 常见错误（如 `0`/`O`、`1`/`l`/`I`、`rn`/`m`）
   - 修复因换行导致的词语断裂或句子切分错误
   - 合并逻辑上连续但被分割的段落
   - 纠正明显拼写和语法错误（不改变原意前提下）
   - 对无法修复的残缺内容标注 `[内容损坏]` 并保留上下文

4. **结构重构**
   - 恢复并优化文档层级结构：合理使用 `#` 至 `###` 标题级别
   - 一级标题（`#`）用于主章节，二级（`##`）为子节，三级（`###`）为细分内容
   - 列表统一格式（有序/无序），保持缩进一致
   - 表格保持完整性，确保行列对齐、语义可读
   - 图表引用需与上下文连贯，缺失图注可补充 `[图注缺失]`

5. **语义保全**
   - 不增删、不改写核心信息
   - 保留专业术语、领域关键词及技术表达
   - 维护原文逻辑关系与上下文连贯性

### 输出要求

- **仅输出清洗后的文档内容**
- 使用 **Markdown 格式**，禁止添加解释、说明或元评论
- 不包含前缀、后缀、提示语或总结性语句
- 为无标题的关键段落补充简洁小标题，提升结构可读性
- 确保标题层级递进清晰，避免跳级（如 `#` 直接到 `###`）

请处理以下内容：

{content}
"""
}
```



### 手动清洗
1. 文本规范化：
    1. 分词
    2. 去除停用词
    3. 大小写统一
    4. 拼写纠错
2. 编码转换与乱码处理：
    1. 自动检测编码
    2. 统一转为 UTF-8





## 混合检索


### 不同检索方法对比
<font style="color:rgb(31, 35, 40);">不同的检索方法各有优⁢⁢⁢缺点：</font>

+ <font style="color:rgb(31, 35, 40);">向量检索虽然能理解语义，捕捉文本间的‏‏‏概念关联，但对关键词敏感度不够。比如，当你‍‍‍搜索 “2025 年怎么学编程” 时，向量⁠⁠⁠检索可能会返回与编程相关的术语解释，而不是‌‌‌准确锁定 2025 年编程学习路线。</font>
+ <font style="color:rgb(31, 35, 40);">相反，基于倒排索引的全⁢⁢⁢文检索在精确匹配关键词方面表现出色，但它不理‏‏‏解语义，难以处理同义词或概念性查询。就像你问‍‍‍ “编程导航的创始人是谁”，全文检索可能不会⁠⁠⁠返回只提到 “程序员鱼皮创办了很多网站” 而‌‌‌没有明确提到 “编程导航” 的文档。</font>
+ <font style="color:rgb(31, 35, 40);">结构化检索支⁢⁢⁢持精确过滤和复杂条件组‏‏‏合，但依赖良好的元‍‍‍数据。</font>
+ <font style="color:rgb(31, 35, 40);">而知识图谱检索能发现⁠⁠⁠实体间隐含关系，适合回‌‌‌答复杂问题，但构建成本高。</font>



<font style="color:rgb(31, 35, 40);">主要检索方法比较：</font>

| **<font style="color:rgb(31, 35, 40);">检索方法</font>** | **<font style="color:rgb(31, 35, 40);">原理</font>** | **<font style="color:rgb(31, 35, 40);">优势</font>****<font style="color:rgba(255, 255, 255, 0.01);">tArj6Cx2bR50Pmg+1NPqprpNL9Z6CoXwlg+daXqyRXg=</font>** | **<font style="color:rgb(31, 35, 40);">劣势</font>** |
| --- | --- | --- | --- |
| <font style="color:rgb(31, 35, 40);">向量检索</font> | <font style="color:rgb(31, 35, 40);">基于嵌入向量相似度搜索</font> | <font style="color:rgb(31, 35, 40);">理解语义关联，适合概念性查询</font><font style="color:rgba(255, 255, 255, 0.01);">F43EPUo38x/bguL8+2uRblf8nVLzGxKBrr6zMqBPxuU=</font> | <font style="color:rgb(31, 35, 40);">对关键词不敏感，召回可能不准确</font> |
| <font style="color:rgb(31, 35, 40);">全文检索</font> | <font style="color:rgb(31, 35, 40);">基于倒排索引，匹配⁢⁢⁢关键词</font> | <font style="color:rgb(31, 35, 40);">精确匹配关键词，高召回率</font><font style="color:rgba(255, 255, 255, 0.01);">xaRUrbiWO6LMyTQDFoeoSpefUuDk/xYc18yW+u4b4/4=</font> | <font style="color:rgb(31, 35, 40);">不理解语义，同义词难‏‏‏以匹配</font> |
| <font style="color:rgb(31, 35, 40);">结构化检索</font> | <font style="color:rgb(31, 35, 40);">基于元数据或结构化字段查询</font> | <font style="color:rgb(31, 35, 40);">精确过‍‍‍滤，支持复杂条件组合</font><font style="color:rgba(255, 255, 255, 0.01);">tArj6Cx2bR50Pmg+1NPqprpNL9Z6CoXwlg+daXqyRXg=</font> | <font style="color:rgb(31, 35, 40);">依赖良好的元数据，灵活性有限</font> |
| <font style="color:rgb(31, 35, 40);">知识图⁠⁠⁠谱检索</font> | <font style="color:rgb(31, 35, 40);">利用实体间关系进行图遍历</font> | <font style="color:rgb(31, 35, 40);">发现隐含关系，回答复‌‌‌杂问题</font><font style="color:rgba(255, 255, 255, 0.01);">6OqX2Gsi3ZLCDXxRMQdJjxM9KdnRbUU2mMuvIZ78awo=</font> | <font style="color:rgb(31, 35, 40);">构建成本高，需要专业知识</font> |




### 混合检索策略
#### 并行混合检索
同时使用多⁢⁢⁢种检索方法获取‏结‏果‏，然后使用‍重排‍模型‍融合多来⁠⁠源结果。

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/webp/12814253/1779628524905-4838c144-1b18-4202-9ab8-243f1036f2aa.webp)



#### 级联混合检索
<font style="color:rgb(31, 35, 40);">层层筛选，⁢⁢⁢先使用一种方法‏进‏行‏广泛召回，‍再用‍另一‍种方法精⁠⁠确过滤。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/webp/12814253/1779628570019-f5d3a4c8-e235-48ec-a451-a17a0ce3e56f.webp)





#### 动态混合检索
<font style="color:rgb(31, 35, 40);">通过一个 ⁢⁢⁢“路由器”，根‏据‏查‏询类型自动‍选择‍最合‍适的检⁠索方法⁠，更⁠加智‌能。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/webp/12814253/1779628602277-fb770c84-258d-4817-a019-b30d63a9d8fc.webp)  
 





## RAG 架构变体


### 自纠错 RAG（C-RAG）
<font style="color:rgb(31, 35, 40);">解决了模型⁢⁢⁢可能误解或错误‏使‏用‏检索信息的‍问题‍，提‍高回答的⁠⁠准确性。</font><font style="color:rgba(255, 255, 255, 0.01);">MFmvtc/b9DTHP4JyVltdO6H42AFSHJjXwV3A3vWWoig=</font>

<font style="color:rgb(31, 35, 40);">想象一下，你给朋友⁢⁢⁢讲述一个你刚读过的新闻，但不小心添加‏‏‏了一些自己的理解或记错了细节，C-R‍‍‍AG 就是为了解决这个问题而设计的。⁠⁠⁠ ‌‌‌ </font>

<font style="color:rgb(31, 35, 40);">C-RAG 采用 “检⁢⁢索-⁢生成-验证-纠正” 的闭环流程：先检索‏‏文档，生成初步回答‏，然后验证回答中的每个事‍‍实陈述，发现错误就立即纠正并重新‍生成。这种⁠⁠循环确保了最终回答的高度准确性，特别适合医‌‌⁠疗、法律等对事实准确性要求极高的领域 。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/webp/12814253/1779628915791-ca06704e-3992-4548-9f23-47018f52d025.webp)



### 自省式 RAG（Self-RAG）
<font style="color:rgb(31, 35, 40);">解决了 “⁢⁢⁢并非所有问题都‏需‏要‏检索” 的‍问题‍，让‍回答更⁠自然并⁠提高系‌⁠统效率。</font>

<font style="color:rgb(31, 35, 40);">想象你问 “1+1等于几” 这样的基础问题，模型完全可以直接回答，无需额外检索。Self-RAG 让模型学会了判断：什么时候需要查资料、什么时候可以直接回答。</font><font style="color:rgba(255, 255, 255, 0.01);">F43EPUo38x/bguL8+2uRblf8nVLzGxKBrr6zMqBPxuU=</font>

<font style="color:rgb(31, 35, 40);">收到提问时，Sel⁢⁢⁢f-RAG 模型会在内心思考：“这个‏‏‏问题我知道答案吗？需要查询更多信息吗‍‍‍？我的回答包含任何不确定的内容吗？”⁠⁠⁠ 这种自我反思机制使回答更加自然，也‌‌‌可以在一定程度上提高系统效率。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/webp/12814253/1779628945386-360bf4cc-f527-4b44-a36e-9223c77cbc31.webp)

<font style="color:rgb(31, 35, 40);"></font>

<font style="color:rgb(31, 35, 40);"></font>

### <font style="color:rgb(31, 35, 40);">检索树 RAG（RAPTOR）</font>
<font style="color:rgb(31, 35, 40);">提供了一种结构⁢⁢⁢化的解决方案，特别适合可拆‏‏‏分的复杂问题。它就像解决一‍‍‍个复杂数学题：先把大问题分⁠⁠⁠解成小问题，分别解决每个小‌‌‌问题，然后将答案整合起来。</font>

<font style="color:rgb(31, 35, 40);">举个例子，对于 “介绍编程⁢⁢⁢导航的交流板块、学习板块和教程板块” 这样的多方面问‏‏‏题，RAPTOR 会分别检索关于 3 个板块的信息，‍‍‍然后综合这些信息形成最终回答。这种方法特别适合需要整⁠⁠⁠合多方面知识的复杂问题，能够提高长篇叙述的连贯性和准‌‌‌确性，克服单次检索的上下文长度限制。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/webp/12814253/1779628972427-89b96904-938b-4aef-9e67-c631de0ef1e4.webp)



### 多智能体 RAG 系统
<font style="color:rgb(31, 35, 40);">组合拥有各⁢⁢⁢类特长的智能体，通过‏‏‏明确的通信协议交换信‍‍‍息，实现复杂任务的协⁠⁠⁠同处理。也就是让专业‌‌‌的大模型做专业的事情。</font>

<font style="color:rgb(31, 35, 40);">还是类比到现实生活，假⁢⁢⁢设某个团队要解决问题。团队中有专门负责理解用‏‏‏户意图的接待员，有擅长搜索文档的资料管理员，‍‍‍有精通特定领域知识的专家，还有负责事实核查的⁠⁠⁠审核员和润色最终回答的编辑。比起一个人做事，‌‌‌各司其职相互配合效果可能会更好。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/webp/12814253/1779629001667-016cb5cf-58bd-41ae-97b6-7a88b7517e46.webp)



# 四：RAG评估
<font style="color:rgb(31, 35, 40);">RAG 应用评估本质上回答了 3 个关键问题：</font><font style="color:rgba(255, 255, 255, 0.01);">tArj6Cx2bR50Pmg+1NPqprpNL9Z6CoXwlg+daXqyRXg=</font>

+ <font style="color:rgb(31, 35, 40);">系统检索的信息是否相关？</font>
+ <font style="color:rgb(31, 35, 40);">生成的回答是否准确？</font>
+ <font style="color:rgb(31, 35, 40);">整体用户体验如何？</font>

<font style="color:rgb(31, 35, 40);">评估的目的⁢⁢⁢是确保回答质量‏、‏识‏别性能瓶颈‍，从‍而给‍出持续⁠优化⁠的思路。</font>

<font style="color:rgb(31, 35, 40);">我们可以简单了解下 RAG 应用的评估指标：</font><font style="color:rgba(255, 255, 255, 0.01);">MFmvtc/b9DTHP4JyVltdO6H42AFSHJjXwV3A3vWWoig=</font>

<font style="color:rgb(31, 35, 40);">1）检索质量评估指标</font>

+ <font style="color:rgb(31, 35, 40);">召回率：能否检索到所有相关文档</font>
+ <font style="color:rgb(31, 35, 40);">精确率：检索结果中相关文档的比例</font>
+ <font style="color:rgb(31, 35, 40);">平均精度均值（MAP）：考虑排序质量的综合指标</font>
+ <font style="color:rgb(31, 35, 40);">规范化折扣累积增益（NDCG）：考虑到文档的相关性和它们在排名中的位置，是一个衡量排名质量的指标</font>

<font style="color:rgb(31, 35, 40);">2）生成回答质量评估指标</font><font style="color:rgba(255, 255, 255, 0.01);">F43EPUo38x/bguL8+2uRblf8nVLzGxKBrr6zMqBPxuU=</font>

+ <font style="color:rgb(31, 35, 40);">事实准确性：回答中事实性陈述的准确程度</font>
+ <font style="color:rgb(31, 35, 40);">答案完整性：回答是否涵盖问题的所有方面</font>
+ <font style="color:rgb(31, 35, 40);">上下文相关性：回答与问题的相关程度</font>
+ <font style="color:rgb(31, 35, 40);">引用准确性：引用内容是否确实来自检索上下文</font>

<font style="color:rgb(31, 35, 40);">当然，我们还可以根据具⁢⁢⁢体应用场景，定制专门的评估标准。比如系统性能‏‏‏评估、领域适应性评估、多语言评估、时效性评估‍‍‍和用户满意度评估。其中，用户满意度评估在我们⁠⁠⁠开发 AI 产品时尤为常见，经常需要引导用户‌‌‌针对 AI 大模型的回复进行打分。</font>

  
 <!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/webp/12814253/1779629119292-c74c992f-2a77-4403-8d74-c45301f4cf61.webp)



<font style="color:rgb(31, 35, 40);">RAG 评估流程：</font>

1. <font style="color:rgb(31, 35, 40);">生成评估数据集：创建覆盖不同问题类型的测试集，为每个问题准备标准答案和相关文档。这些测试问题应包括事实性问题、观点性问题、多步骤推理问题等各种类型。</font>
2. <font style="color:rgb(31, 35, 40);">运行评估检索过程的程序：对每个测试问题执行检索，与人工标注的相关文档比较，计算检索性能指标。</font>
3. <font style="color:rgb(31, 35, 40);">评估回答质量：实际操作中，评估通常分为自动评估和人工评估两种方式。自动评估使用像 ROUGE（召回率取向摘要评估）或 BLEU（双语评估替补）这样的指标来衡量生成内容与参考答案的相似度，或者使用更强大的模型来判断回答质量。但自动评估有其局限性，某些方面如创造性、实用性等仍然需要人工评估。</font>
4. <font style="color:rgb(31, 35, 40);">综合分析与优化：识别失败模式和常见错误，比如区分检索失败和生成失败，针对性改进系统组件。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/webp/12814253/1779629198662-7a6e8ca4-83bf-4d56-850b-d222c8f8e0cd.webp)





