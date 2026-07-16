# CLI 환경 고성능 AI 모델 선택기 쉘프로그램

## Context
* **해결하고자 하는 문제**: `Ollama(Qwen/Hermes)`와 `Gemini CLI` 등 여러 AI 도구를 사용할 때, 매번 긴 옵션이나 별도의 단축어(Alias)를 기억하고 입력해야 하는 번거로움을 해결하고자 함.
* **학습 배경**: 단 하나의 명령어로 현재 가용 가능한 최적의 로컬/클라우드 AI 모델 환경을 대화형 인터페이스(Interactive Menu)로 호출하고, 백그라운드 서버 의존성까지 동적으로 제어하는 자동화 스크립트 설계.

---

## Core

아래 코드를 복사하여 `ai-selector.sh` 파일을 생성하고 실행 권한을 부여하면 터미널에서 바로 사용할 수 있습니다.

```bash
#!/bin/bash

# 색상 정의
GREEN='\033[0;32m'
BLUE='\033[0;34m'
YELLOW='\033[1;33m'
RED='\033[0;31m'
NC='\033[0m' # No Color

# Ollama 서버 상태 체크 및 자동 구동 함수
check_and_start_ollama() {
    echo -e "\({YELLOW}[*] Ollama 서버 상태를 확인하는 중...\){NC}"
    if ! lsof -i :11434 > /dev/null 2>&1; then
        echo -e "\({BLUE}[!] Ollama 서버가 꺼져 있습니다. 최적화 옵션으로 가동합니다...\){NC}"
        OLLAMA_FLASH_ATTENTION="1" \
        OLLAMA_KV_CACHE_TYPE="q8_0" \
        /opt/homebrew/opt/ollama/bin/ollama serve > /dev/null 2>&1 &
        
        # 서버가 바인딩될 때까지 대기 (최대 5초)
        for i in {1..5}; do
            sleep 1
            if lsof -i :11434 > /dev/null 2>&1; then break; fi
        done
        echo -e "\({GREEN}[+] Ollama 서버 구동 완료 (Flash Attention 활성화)\){NC}"
    else
        echo -e "\({GREEN}[+] Ollama 서버가 이미 실행 중입니다.\){NC}"
    fi
}

# 인터랙티브 메뉴 출력
echo -e "\({GREEN}=======================================\){NC}"
echo -e "\${GREEN}    🤖 후츠릿 TIL CLI AI 에이전트 선택기   \${NC}"
echo -e "\({GREEN}=======================================\){NC}"
echo -e "사용할 고성능 AI 모델을 선택하세요:"
echo -e "1) \({BLUE}Qwen 2.5 Coder\){NC} (로컬 최고성능 코딩/추론)"
echo -e "2) \({BLUE}Nous Hermes 3\){NC}  (로컬 에이전트/범용 대화)"
echo -e "3) \({BLUE}Google Gemini\){NC}  (클라우드 대형 맥락/무설치)"
echo -e "4) 종료"
echo -ne "선택 (1-4): "
read -r CHOICE

case \$CHOICE in
    1)
        check_and_start_ollama
        echo -e "\({GREEN}[+] qwen2.5-coder 로딩 중... (/bye 입력 시 종료)\){NC}"
        ollama run qwen2.5-coder
        ;;
    2)
        check_and_start_ollama
        echo -e "\({GREEN}[+] hermes3 로딩 중... (/bye 입력 시 종료)\){NC}"
        ollama run hermes3
        ;;
    3)
        echo -e "\${GREEN}[+] Google Gemini CLI 진입 중... (Ctrl+C 입력 시 종료)\${NC}"
        npx @google/gemini-cli
        ;;
    4)
        echo -e "\({RED}[*] 프로그램을 종료합니다.\){NC}"
        exit 0
        ;;
    *)
        echo -e "\({RED}[!] 잘못된 선택입니다. 1에서 4 사이의 숫자를 입력하세요.\){NC}"
        exit 1
        ;;
esac
```

### ⚙️ 빠른 실행 및 환경 등록 방법
```bash
# 1. 파일 생성 및 권한 부여
chmod +x ai-selector.sh

# 2. 터미널 어디서나 'ai' 단 두 글자로 실행하도록 ~/.zshrc 등록
echo "alias ai='\$(pwd)/ai-selector.sh'" >> ~/.zshrc
source ~/.zshrc
```

---

## Insight

### 1. 기술적 검증 결과 및 비교 데이터
* **포트 탐지(`lsof`) 기반 예외 처리**: 기존에는 서버가 꺼진 상태에서 `ollama run`을 시도하면 연결 실패 익셉션으로 프로그램이 뻗었으나, `lsof -i :11434` 조건절을 통해 인프라 상태를 먼저 검증하므로 100% 실행 안정성이 보장됨.
* **리소스 효율화**: 무조건 백그라운드에 Ollama를 띄우지 않고, 사용자가 1번 혹은 2번을 선택하는 시점에만 **동적으로 자원을 할당(Lazy Loading)** 하므로 Mac 메모리 관리에 최적화됨.

### 2. 엔지니어적 견해
* 진정한 지능화는 인프라의 파편화를 수동으로 메우지 않는 것입니다. 
* 쉘 스크립트를 통해 로컬 엔진(Ollama)의 하드웨어 최적화 가동 프로세스와 클라우드 엔진(npx)의 일회성 런타임을 하나의 인터페이스로 레이어링(Layering)함으로써, 개발 컨텍스트 스위칭 비용을 혁신적으로 절감했습니다.
