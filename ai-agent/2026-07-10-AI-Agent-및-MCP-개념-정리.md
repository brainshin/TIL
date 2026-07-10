### Context
AI 에이전트(AI Agent)는 단순히 질문에 답변만 하는 기존의 AI 어시스턴트와 달리, 사용자가 설정한 목표를 달성하기 위해 자율적으로 계획을 세우고 도구를 사용하여 환경과 상호작용하는 지능형 시스템입니다. 최근 LLM(대규모 언어 모델)의 발전에 힘입어 복잡한 업무를 스스로 처리하는 '실무 담당자'로서 그 중요성이 커지고 있습니다.

### Core
AI 에이전트의 핵심 구성요소는 다음의 구조로 이해할 수 있습니다.

* **LLM (Brain)**: 추론, 계획 수립, 의사결정의 핵심 역할.
* **Planning**: 작업을 작은 단위로 분해하고 순서를 결정하는 모듈.
* **Tools/Skills (MCP 등)**: 외부 데이터베이스나 업무 도구(Slack, Notion, GitHub 등)와 통신하여 실무를 수행하는 인터페이스.
* **Memory**: 작업 흐름을 유지하기 위한 단기/장기 기억 장치.

**MCP(Model Context Protocol)**는 AI 에이전트가 외부 데이터 및 도구와 상호작용하는 방식을 표준화한 오픈 프로토콜입니다. 

```json
// MCP Server 예시 구조 (개념적)
{
  "protocol": "mcp",
  "capabilities": {
    "resources": ["read_document", "list_files"],
    "tools": ["execute_query", "send_message"]
  },
  "endpoint": "ws://mcp-server-url:port"
}
```

실무 적용 방법:
* **워크플로우 자동화**: n8n과 같은 도구를 활용해 LLM 에이전트가 이메일을 읽고 특정 조건에 맞춰 슬랙 알림을 보내는 등의 프로세스를 자동화합니다.
* **데이터 통합**: MCP를 활용하여 서로 다른 SaaS 도구들의 데이터를 표준화된 인터페이스로 에이전트에게 공급합니다.

### Insight
AI 에이전트는 '생각하는 기계'에서 '행동하는 기계'로 진화하고 있습니다. 과거에는 각 도구마다 API를 개별적으로 개발해야 했으나, MCP와 같은 표준 프로토콜의 등장은 AI 에이전트의 확장성을 획기적으로 높여줄 것으로 보입니다. MCP를 통해 모델은 도구의 사용법을 직접 학습하는 것이 아니라, 제공된 인터페이스(context)를 통해 즉각적으로 능력을 확장할 수 있게 됩니다. 이는 실무 생산성 도구의 파편화 문제를 해결하는 핵심 기술이 될 것입니다.

**출처:** [AI 에이전트란 무엇일까요? - AWS](https://aws.amazon.com/ko/what-is/ai-agents/), [MCP(Model Context Protocol)란? - 마켓핏랩](https://www.mfitlab.com/solutions/blog/mcp)
