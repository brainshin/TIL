### Context
개발자가 API와 통신할 때 서버의 과도한 요청을 방지하기 위해 사용되는 '속도 제한(Rate Limiting)' 정책을 파이썬 `requests` 라이브러리로 처리하는 방법이 필요합니다. 대량의 데이터를 수집하거나 외부 서비스를 반복 호출할 때 429 Too Many Requests 에러를 방지하고 안정적인 통신을 유지하는 전략을 학습합니다.

### Core
`requests`와 `time` 모듈을 사용하여 응답 코드 429를 감지하고 대기 시간(Backoff)을 도입하는 패턴입니다.

```python
import requests
import time

def fetch_with_rate_limiting(url, retries=3, backoff_factor=1):
    for i in range(retries):
        response = requests.get(url)
        
        # 429 상태 코드가 발생하면 대기 후 재시도
        if response.status_code == 429:
            sleep_time = backoff_factor * (2 ** i)
            print(f"Rate limited. Waiting for {sleep_time} seconds...")
            time.sleep(sleep_time)
            continue
            
        return response
    
    raise Exception("Max retries exceeded due to rate limiting.")
```

### Insight
*   지수 백오프(Exponential Backoff) 전략을 사용하면 서버 부하를 줄이면서도 재시도 효율을 극대화할 수 있습니다.
*   최신 API들은 `Retry-After` 헤더를 통해 정확한 대기 시간을 알려주는 경우가 많으므로, 이를 파싱하여 활용하는 것이 더욱 권장됩니다.
*   본인의 에이전트 개발 시, 무분별한 요청은 계정 차단의 원인이 되므로 반드시 요청 간 지연(Delay)을 적용하는 것이 좋습니다.

**출처:** [Python requests: Handling 429 Too Many Requests](https://requests.readthedocs.io/en/latest/user/advanced/)