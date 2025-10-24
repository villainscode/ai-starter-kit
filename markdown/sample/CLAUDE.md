# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

# Using Gemini CLI for Large Codebase Analysis

When analyzing large codebases or multiple files that might exceed context limits, use the Gemini CLI with its massive
context window. Use `gemini -p` to leverage Google Gemini's large context capacity.

## File and Directory Inclusion Syntax

Use the `@` syntax to include files and directories in your Gemini prompts. The paths should be relative to WHERE you run the
gemini command:

### Examples:

**Single file analysis:**
gemini -p "@src/main.py Explain this file's purpose and structure"

Multiple files:
gemini -p "@package.json @src/index.js Analyze the dependencies used in the code"

Entire directory:
gemini -p "@src/ Summarize the architecture of this codebase"

Multiple directories:
gemini -p "@src/ @tests/ Analyze test coverage for the source code"

Current directory and subdirectories:
gemini -p "@./ Give me an overview of this entire project"

# Or use --all_files flag:
gemini --all_files -p "Analyze the project structure and dependencies"

Implementation Verification Examples

Check if a feature is implemented:
gemini -p "@src/ @lib/ Has dark mode been implemented in this codebase? Show me the relevant files and functions"

Verify authentication implementation:
gemini -p "@src/ @middleware/ Is JWT authentication implemented? List all auth-related endpoints and middleware"

Check for specific patterns:
gemini -p "@src/ Are there any React hooks that handle WebSocket connections? List them with file paths"

Verify error handling:
gemini -p "@src/ @api/ Is proper error handling implemented for all API endpoints? Show examples of try-catch blocks"

Check for rate limiting:
gemini -p "@backend/ @middleware/ Is rate limiting implemented for the API? Show the implementation details"

Verify caching strategy:
gemini -p "@src/ @lib/ @services/ Is Redis caching implemented? List all cache-related functions and their usage"

Check for specific security measures:
gemini -p "@src/ @api/ Are SQL injection protections implemented? Show how user inputs are sanitized"

Verify test coverage for features:
gemini -p "@src/payment/ @tests/ Is the payment processing module fully tested? List all test cases"

When to Use Gemini CLI

Use gemini -p when:
- Analyzing entire codebases or large directories
- Comparing multiple large files
- Need to understand project-wide patterns or architecture
- Current context window is insufficient for the task
- Working with files totaling more than 100KB
- Verifying if specific features, patterns, or security measures are implemented
- Checking for the presence of certain coding patterns across the entire codebase

Important Notes

- Paths in @ syntax are relative to your current working directory when invoking gemini
- The CLI will include file contents directly in the context
- No need for --yolo flag for read-only analysis
- Gemini's context window can handle entire codebases that would overflow Claude's context
- When checking implementations, be specific about what you're looking for to get accurate results


## 🎯 QNote 프로젝트 개요
QNote는 Electron 기반의 크로스 플랫폼 노트 애플리케이션입니다. 리치 텍스트 편집 기능과 플러그인 기반 아키텍처를 제공하는 데스크톱 애플리케이션입니다.

### 기술 스택
- **프론트엔드**: React 18.3.1, Next.js 14.2.16
- **백엔드**: Electron 33.4.11
- **데이터베이스**: better-sqlite3 11.10.0
- **언어**: TypeScript 5.7.2
- **스타일**: CSS Modules, BEM 방법론

## 🚀 개발 명령어

### 필수 개발 명령어
```bash
# 의존성 설치 (반드시 Node.js 22.15.0 사용)
npm install

# 개발 모드 실행 (Next.js 포트 13333 + Electron)
npm run dev

# 웹 브라우저에서만 테스트
npm run dev:next  # http://localhost:13333

# 프로덕션 빌드
npm run build

# 타입 체크
npm run type-check

# 린트 검사  
npm run lint

# 데이터베이스 초기화
npm run setup-db

# 데이터베이스 리셋
npm run reset-db
```

### 테스트 명령어
```bash
# 단일 컴포넌트 테스트 (예시)
node test/components/[component-name].test.js

# 플러그인 테스트
node test/plugins/[plugin-name].test.js
```

## 🏗️ 코드 아키텍처

### 계층 구조 (Clean Architecture)
```
Presentation Layer (React Components)
    ↓
Domain Layer (Services, Business Logic)  
    ↓
Data Layer (Repositories, SQLite)
```

### Electron 프로세스 분리
- **메인 프로세스** (`src/main/`): 애플리케이션 생명주기, 데이터베이스, IPC 핸들러
- **렌더러 프로세스** (`src/renderer/`): React UI, 사용자 상호작용
- **IPC 통신**: 타입 안전한 프로세스 간 통신 (`src/shared/types/ipc.ts`)

### 주요 디렉토리 구조
```
src/
├── main/                 # Electron 메인 프로세스
│   ├── controllers/      # IPC 핸들러 컨트롤러
│   ├── services/         # 비즈니스 로직
│   ├── repositories/     # 데이터 접근 계층
│   └── database/         # SQLite 관리
├── renderer/             # React 렌더러 프로세스
│   └── components/       # UI 컴포넌트
└── shared/               # 공유 코드
    └── types/            # 타입 정의


styles/
├── common/               # 테마 무관 구조 스타일
└── themes/               # 테마별 색상 스타일
    ├── light/
    └── dark/
```

### 에디터 플러그인 시스템
- **주요 플러그인**: table, image, link, format, list 등을 독립적인 TS로 만들고 각각의 독립 플러그인을 툴바에 추가, 삭제함으로서 기존 기능에 영향을 전혀 미칠수 없게 한다.


## 📝 코딩 규칙
- 표준 TS 및 Next.js 폴더 구조를 따른다.
- OOP 및 기능단위 컴포넌트 베이스로 개발
- SOLID 원칙 및 Clean Architecture 적용
- DRY 원칙 - 중복 코드 제거 및 공통 함수화

### 🔔 모달 사용 규칙 (중요)
- **절대 window.alert(), window.confirm() 사용 금지**
- **절대 window.electronAPI.dialog.showAlert() 사용 금지**
- **반드시 useModal() 훅 사용**
```javascript
// ✅ 올바른 사용
import { useModal } from '../../contexts/ModalContext';
const modal = useModal();
await modal.alert('메시지', { title: '제목', theme });

// ❌ 절대 사용 금지
window.alert('메시지');
window.electronAPI.dialog.showAlert('메시지');
```

### 한국어 우선
- 모든 설명과 주석은 한국어로 작성
- Git 커밋 메시지 한국어 가능
- 기술 용어나 라이브러리명은 영어 유지

### TypeScript 엄격 모드
- 모든 함수와 변수에 타입 명시
- any 타입 사용 금지
- 엄격한 null 체크

### 파일 수정 원칙
- 새 파일 생성보다 기존 파일 수정 우선
- 20줄 이하 수정 시 변경 부분만 표시
- 수정 전 반드시 파일 읽기로 현재 상태 확인


## 🚨 절대 금지 사항 (CRITICAL - 반드시 준수)

### 🔴 데이터베이스 관련 절대 금지 사항
```bash
# 데이터베이스 파괴적 명령어 - 절대 사용 금지 (사용자 명시적 요청 없이)
npm run reset-db           # ❌ 절대 금지 - 모든 데이터 삭제
npm run setup-db           # ⚠️ 사용자 허가 필요
node scripts/reset-database.js  # ❌ 절대 금지

# SQL 파괴적 명령어 - 절대 금지
DROP TABLE                 # ❌ 절대 금지
DROP DATABASE             # ❌ 절대 금지  
DELETE FROM               # ⚠️ WHERE 절 없이 사용 금지
TRUNCATE                  # ❌ 절대 금지
ALTER TABLE DROP          # ⚠️ 사용자 허가 필요
```

### 🔴 데이터베이스 작업 필수 규칙
1. **데이터 삭제/리셋 전 반드시 사용자에게 명시적 허가 요청**
2. **백업 없이 데이터 삭제 절대 금지**
3. **테스트 데이터가 있는 상태에서 리셋 금지**
4. **SQL 수정으로 해결 가능한 문제는 데이터베이스 리셋 금지**
5. **프로덕션 데이터베이스는 어떤 경우에도 자동 수정 금지**

### 🔴 Git 위험 명령어 - 절대 사용 금지
```bash
git push --force
git reset --hard
git commit --no-verify
```

### 🔴 npm 위험 명령어
```bash
npm audit fix --force
```

### 라이브러리 버전 고정 (변경 금지)
```json
{
  "react": "^18.3.1",
  "next": "^14.2.16",
  "electron": "33.4.11",
  "better-sqlite3": "11.10.0",
  "typescript": "^5.7.2"
}
```
### 기본 기술 스택 이외의 라이브러리, 프레임워크, 언어 도입은 지양
- 어쩔수 없이 해야 할 경우 해야 하는 이유와 검토 의견을 낸 뒤 명시적으로 허가 요청할것

### 보안 규칙
- eval() 사용 금지
- innerHTML 직접 사용 금지 (DOMPurify 사용)
- 사용자 입력 반드시 검증
- API 키나 시크릿 절대 커밋 금지

## 🔄 개발 워크플로우

### 기능 개발 프로세스
1. 요구사항 분석
2. 기존 코드 검토 (유사 기능 찾기)
3. 타입 정의 작성
4. 기능 구현
5. 타입 체크 실행 (`npm run type-check`)
6. 린트 검사 (`npm run lint`)
7. 테스트 실행
8. 문서 업데이트

### Git 커밋 컨벤션
```
[feat] : 새로운 기능 추가
[fix] : 버그 수정
[debug] : 디버깅 관련
[refactor] : 코드 리팩토링
[docs] : 문서 변경
[style] : 코드 포맷팅
[test] : 테스트 코드
[chore] : 빌드 설정 변경
```

### 브랜치 전략
- `main`: 안정 버전
- `feature/날짜`: 기능 개발 (예: feature/20250803)
- `hotfix/이슈명`: 긴급 버그 수정

## 🐛 디버깅

### Electron 개발자 도구
- 앱 실행 후 `Cmd+Option+I` (macOS)
- 메인 프로세스 로그: `electron-log` 사용
- 렌더러 프로세스: 브라우저 개발자 도구

### 디버그 유틸


## 📊 데이터 모델

### 주요 엔티티
- **Document**: 노트 문서
- **Folder**: 폴더 구조
- **Tag**: 태그 시스템
- **Attachment**: 첨부파일
- **Category**: 카테고리 분류

### SQLite 스키마
- 위치: `src/main/database/`
- 마이그레이션: `src/main/database/migrations/`

## 🎨 스타일 가이드

### CSS 구조
- BEM 방법론 사용
- 테마 시스템: light/dark 테마 지원
- 구조(common)와 색상(themes) 분리

### 테마 전환
- localStorage에 사용자 설정 저장
- 헤더의 테마 버튼으로 실시간 전환

## 📋 AI Assistant 핵심 규칙

### 🚨🚨🚨 최우선 규칙 - 데이터베이스 보호 🚨🚨🚨
**절대 금지 (NEVER DO WITHOUT EXPLICIT USER PERMISSION):**
- `npm run reset-db` 실행 금지
- `DROP TABLE`, `DROP DATABASE` 실행 금지
- `DELETE FROM` (WHERE 절 없이) 실행 금지
- 데이터베이스 파일 직접 삭제 금지
- 테스트 데이터가 있는 상태에서 리셋 금지

**데이터베이스 문제 발생 시:**
1. 먼저 코드 수정으로 해결 시도
2. 데이터베이스 구조 변경이 필요하면 사용자에게 상황 설명
3. 데이터 손실 가능성이 있는 작업은 반드시 사용자 허가 요청
4. "데이터베이스를 리셋하시겠습니까? 모든 데이터가 삭제됩니다" 명시적 확인

### 🔄 기능 변경 시 필수 체크리스트
**기존 기능 보존 원칙:**
1. **변경 전 현재 상태 확인**
    - 해당 컴포넌트의 현재 기능 목록 작성
    - 관련 UI 요소들의 표시 상태 확인
    - CSS 충돌 가능성 체크

2. **영향 범위 분석**
    - 변경할 컴포넌트를 사용하는 다른 컴포넌트 확인
    - CSS 클래스 중복 정의 확인 (common, themes, modules)
    - 조건부 렌더링 로직 검토

3. **변경 후 검증**
    - 기존 기능들이 모두 정상 작동하는지 확인
    - 특히 UI 요소 표시 여부 확인 (버튼, 메뉴 등)
    - collapsed/expanded 등 다양한 상태에서 테스트

4. **문제 발생 시 즉시 롤백**
    - 기존 기능이 사라지거나 동작하지 않으면 즉시 원복
    - 원인 분석 후 다시 시도

### 🎯 핵심 UI 요소 보호 목록
다음 요소들은 항상 표시되어야 함:
- **사이드바**: 폴더 생성 버튼, 노트 검색
- **에디터**: 툴바 모든 버튼, 저장/취소 버튼
- **헤더**: 테마 전환, 창 컨트롤 버튼

### 언어 및 커뮤니케이션
- 모든 설명은 한국어로 제공
- 사용자를 전문가로 대우하고 불필요한 설명 최소화
- 간결하고 명확한 답변 제공
- 기술 용어는 원문 유지 가능

### 코드 작성 원칙
- 완전하고 버그 없는 코드 작성 (TODO, placeholder 없음)
- 가독성 최우선 고려
- TypeScript 엄격 모드 준수
- SOLID 원칙 및 Clean Architecture 적용
- DRY 원칙 - 중복 코드 제거 및 공통 함수화

### 수정 작업 가이드라인
- 파일 수정 전 반드시 현재 상태 확인
- 기존 기능 영향 여부 명시
- 아이콘, 폰트 등 변경 시 사전 허가 필요

## 🔍 리팩토링 가이드
- 목표: 기능 변경 없이 코드 구조 개선
- 모든 테스트 통과 확인 필수
- Dead code 자동 검출 및 제거
- 500라인이 넘어가는 파일은 반드시 명시적으로 허가 요청 후 리팩토링 제안
- 리팩토링 하기전에 GEMINI, SERENA MCP 등을 통해 Plan모드로 상세하게 분석하여 사이드이펙트가 없는지 검토 후 명시적 허가 요청 후 진행
- 대규모 리팩토링의 경우 반드시 원본을 백업한 뒤 진행

## 🐛 디버깅 접근법
- 원인과 해결책 설명 후 진행
- 상세 로그 추가로 원인 분석
- 로그는 develop 모드에서 출력하고 production 모드는 제외 되어야 함

## 📖 문서화 규칙
- 주요 컴포넌트: `/docs/[component].mdc` 작성
- 코드와 함께 문서 업데이트
- 복잡한 로직은 주석으로 설명
- 모든 문서와 주석은 한국어 작성
