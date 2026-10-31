# 냉털

냉장고에 있는 재료를 적으면, 지금 만들 수 있는 요리와 레시피를 추천하는 웹앱입니다. (냉털 = 냉장고 털이)

## 파일 구성

| 파일 | 역할 |
| --- | --- |
| `index.html` | 화면 전체 (재료 입력 → 추천 → 레시피). 서버에 연결하지 못하면 내장 레시피 106개로 동작합니다. |
| `api/recipes.js` | Vercel 서버리스 함수. Gemini API를 호출합니다. API 키는 서버 환경변수에만 있습니다. |
| `.env.example` | 환경변수 예시입니다. 진짜 키는 여기에 적지 않습니다. |

## Vercel 배포 (약 10분)

1. **API 키 만들기**: aistudio.google.com/apikey → Create API key → `AIza…` 키를 복사해 둡니다.
2. **GitHub에 올리기**: 새 저장소(예: `naengteol`, Public)를 만들고 **uploading an existing file**로 `index.html`, `api/recipes.js`, `README.md`, `.env.example`을 올립니다. `api` 폴더 구조가 유지되어야 합니다. 진짜 키는 올리지 않습니다.
3. **Vercel에서 가져오기**: vercel.com → Add New → Project → 저장소 Import.
   - Framework Preset: **Other**, Root Directory: `./`, Build/Output: 비워 둠
4. **환경변수 추가**: Name `GEMINI_API_KEY`, Value 1번의 키 → Add.
5. **Deploy**. 끝나면 나오는 `https://….vercel.app` 주소가 제출할 배포 링크입니다.
6. 환경변수를 배포 뒤에 추가했다면 **Deployments → Redeploy**를 한 번 해야 적용됩니다.

## 확인 방법

- 재료를 넣고 추천을 받았을 때 결과 위에 초록색 **AI 추천** 배지가 보이면 성공입니다.
- 노란색 **기본 레시피 모드**가 보이면 AI 호출이 실패해 내장 레시피로 대체된 것입니다.
  - 환경변수 이름(`GEMINI_API_KEY`), 재배포 여부, 모델 이름을 확인하세요.
  - 모델 이름이 바뀌었으면 AI Studio에서 현재 이름을 확인해 `GEMINI_MODEL` 환경변수로 넣으면 코드를 고치지 않아도 됩니다.
  - 원인은 Vercel의 **Logs** 탭에서 `gemini upstream status …` 줄로 확인합니다. (404: 모델 이름, 400/403: 키)
- 주소 뒤에 `/api/recipes`를 붙여 열었을 때 `{"error":"method_not_allowed"}`가 보이면 서버 함수는 정상 배포된 것입니다.

## 알아둘 점

- 요리 사진은 브라우저가 위키백과에서 불러옵니다. 해당 요리 문서가 없거나 연결이 안 되면 이모지 칸이 대신 보입니다. 사진을 누르면 출처 문서로 이동합니다.
- AI가 준 결과는 서버에서 한 번 더 검증합니다. 조리 단계에 나오지 않는 재료는 빼고, 입력하지 않았는데 단계에 나오는 재료는 "필요한 재료"로 표시합니다.

- `file://`로 열거나 GitHub Pages에 올리면 `/api/recipes`가 없으므로 항상 기본 레시피 모드로 동작합니다. AI 추천은 Vercel에서만 켜집니다.
- 무료 Hobby 플랜은 함수 실행이 10초로 제한되어, 코드는 8.5초가 지나면 중단하고 내장 레시피로 넘어갑니다.
- 호출 제한은 인스턴스별 최선 노력 방식이라 완벽한 방어는 아닙니다. 공개 링크를 널리 퍼뜨리기 전에 AI Studio에서 키의 사용량 한도를 확인하세요.
- AI가 만든 레시피는 틀릴 수 있습니다. 상했거나 확실하지 않은 재료는 쓰지 마세요.
