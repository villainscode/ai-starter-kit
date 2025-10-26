# CLAUDE.md - 일렉트론 노트앱 개발 가이드

## 🎯 프로젝트 개요
일렉트론 기반 노트 애플리케이션 개발을 위한 종합 가이드입니다. TypeScript, JavaScript, HTML, CSS를 사용하여 크로스 플랫폼 데스크톱 애플리케이션을 구축합니다.

## 📁 프로젝트 구조

```
note-app/
├── src/                          # 소스 코드
│   ├── main/                     # 메인 프로세스
│   │   ├── main.ts              # 일렉트론 메인 진입점
│   │   ├── window/              # 윈도우 관리
│   │   ├── menu/                # 애플리케이션 메뉴
│   │   ├── storage/             # 파일 시스템 관리
│   │   └── ipc/                 # IPC 핸들러
│   ├── renderer/                # 렌더러 프로세스
│   │   ├── components/          # UI 컴포넌트
│   │   │   ├── common/          # 공통 컴포넌트
│   │   │   ├── editor/          # 에디터 관련
│   │   │   ├── sidebar/         # 사이드바
│   │   │   └── toolbar/         # 툴바
│   │   ├── pages/               # 페이지 컴포넌트
│   │   ├── styles/              # CSS/SCSS 파일
│   │   │   ├── base/            # 기본 스타일
│   │   │   ├── components/      # 컴포넌트별 스타일
│   │   │   ├── themes/          # 테마 관리
│   │   │   └── utils/           # 유틸리티 스타일
│   │   ├── utils/               # 유틸리티 함수
│   │   ├── services/            # 비즈니스 로직
│   │   ├── types/               # TypeScript 타입 정의
│   │   └── locales/             # 다국어 리소스
│   ├── shared/                  # 공유 모듈
│   │   ├── types/               # 공통 타입
│   │   ├── constants/           # 상수 정의
│   │   └── utils/               # 공통 유틸리티
│   └── assets/                  # 정적 리소스
│       ├── icons/               # 아이콘
│       ├── images/              # 이미지
│       └── fonts/               # 폰트
├── dist/                        # 빌드 결과물
├── tests/                       # 테스트 파일
│   ├── unit/                    # 단위 테스트
│   ├── integration/             # 통합 테스트
│   └── e2e/                     # E2E 테스트
├── docs/                        # 문서
├── config/                      # 설정 파일
└── scripts/                     # 빌드 스크립트
```

## 🏗️ 아키텍처 원칙

### 1. 계층 분리 (Clean Architecture)
```
Presentation Layer (UI Components)
    ↓
Domain Layer (Business Logic)
    ↓
Data Layer (Storage, IPC)
```

### 2. 일렉트론 프로세스 분리
- **메인 프로세스**: 애플리케이션 생명주기, 윈도우 관리, 파일 시스템
- **렌더러 프로세스**: UI 렌더링, 사용자 상호작용
- **IPC 통신**: 프로세스 간 안전한 데이터 전송

### 3. 모듈화 및 재사용성
- 컴포넌트 기반 설계
- 공통 로직은 유틸리티로 분리
- 두 번 이상 중복되는 코드는 공통 모듈화

## 📝 코딩 가이드라인

### 1. 네이밍 컨벤션
```typescript
// 변수, 함수: camelCase
const noteContent = '';
function saveNote() {}

// 컴포넌트: PascalCase
class NoteEditor {}
const NoteList = () => {};

// 파일명: kebab-case
// note-editor.ts, sidebar-component.tsx

// CSS 클래스: BEM 방법론
.note-editor__content--active {}
```

### 2. TypeScript 사용 규칙
```typescript
// 모든 함수와 변수에 타입 지정
interface Note {
  id: string;
  title: string;
  content: string;
  createdAt: Date;
  updatedAt: Date;
}

// 엄격한 타입 체크
function createNote(title: string, content: string): Note {
  return {
    id: generateId(),
    title,
    content,
    createdAt: new Date(),
    updatedAt: new Date()
  };
}
```

### 3. 컴포넌트 설계 원칙
```typescript
// 단일 책임 원칙
// ❌ 잘못된 예
class NoteApp {
  renderUI() {}
  saveNote() {}
  deleteNote() {}
  searchNotes() {}
  exportToPDF() {}
}

// ✅ 올바른 예
class NoteEditor {
  renderEditor() {}
}
class NoteStorage {
  saveNote() {}
  deleteNote() {}
}
class NoteSearch {
  searchNotes() {}
}
```

### 4. IPC 통신 패턴
```typescript
// 메인 프로세스
ipcMain.handle('notes:save', async (event, note: Note) => {
  try {
    return await storageService.saveNote(note);
  } catch (error) {
    console.error('노트 저장 실패:', error);
    throw error;
  }
});

// 렌더러 프로세스
const saveNote = async (note: Note): Promise<boolean> => {
  try {
    return await window.electronAPI.saveNote(note);
  } catch (error) {
    notificationService.showError('노트 저장에 실패했습니다.');
    return false;
  }
};
```

## 🎨 스타일 가이드

### 1. CSS 구조 및 BEM 방법론
```scss
// 기본 구조
.note-editor {
  // Block 스타일
  
  &__toolbar {
    // Element 스타일
  }
  
  &__content {
    // Element 스타일
    
    &--readonly {
      // Modifier 스타일
    }
  }
}
```

### 2. 테마 시스템
```scss
// themes/_variables.scss
$themes: (
  light: (
    background: #ffffff,
    text: #333333,
    accent: #007acc
  ),
  dark: (
    background: #1e1e1e,
    text: #ffffff,
    accent: #4fc3f7
  )
);

// 테마 믹신 사용
@mixin themed() {
  @each $theme, $map in $themes {
    .theme-#{$theme} & {
      $theme-map: () !global;
      @each $key, $submap in $map {
        $value: map-get(map-get($themes, $theme), '#{$key}');
        $theme-map: map-merge($theme-map, ($key: $value)) !global;
      }
      @content;
      $theme-map: null !global;
    }
  }
}
```

### 3. 반응형 디자인
```scss
// 브레이크포인트 정의
$breakpoints: (
  mobile: 768px,
  tablet: 1024px,
  desktop: 1200px
);

@mixin responsive($breakpoint) {
  @media (max-width: map-get($breakpoints, $breakpoint)) {
    @content;
  }
}
```

## 🌍 국제화 (i18n)

### 1. 리소스 파일 구조
```typescript
// locales/ko.json
{
  "common": {
    "save": "저장",
    "cancel": "취소",
    "delete": "삭제"
  },
  "note": {
    "create": "새 노트",
    "title_placeholder": "제목을 입력하세요",
    "content_placeholder": "내용을 입력하세요"
  }
}

// 사용법
const t = useTranslation();
<button>{t('common.save')}</button>
```

## 🧪 테스트 전략

### 1. 테스트 구조
```typescript
// 단위 테스트 예시
describe('NoteService', () => {
  let noteService: NoteService;
  
  beforeEach(() => {
    noteService = new NoteService();
  });
  
  it('새 노트를 생성할 수 있어야 한다', () => {
    const note = noteService.createNote('제목', '내용');
    expect(note.title).toBe('제목');
    expect(note.content).toBe('내용');
    expect(note.id).toBeDefined();
  });
});
```

### 2. E2E 테스트
```typescript
// 일렉트론 앱 E2E 테스트
describe('노트 앱 E2E', () => {
  let app: Application;
  
  beforeEach(async () => {
    app = new Application({
      path: electronPath,
      args: [path.join(__dirname, '../dist/main.js')]
    });
    await app.start();
  });
  
  afterEach(async () => {
    if (app && app.isRunning()) {
      await app.stop();
    }
  });
});
```

## ⚠️ 중요 제약사항 및 주의사항

### 🚫 절대 금지 사항

#### 1. Git 관련 위험 명령어
```bash
# ❌ 절대 사용 금지
git push --force
git push -f
git reset --hard HEAD~n  # 커밋 완전 삭제
git branch -D branch-name  # 브랜치 강제 삭제
git clean -fd  # 추적되지 않는 파일 강제 삭제

# ❌ 위험한 커밋 옵션
git commit --no-verify  # 훅 우회 금지
```


#### 2. 파일 시스템 위험 작업
```typescript
// ❌ 절대 금지
fs.rmSync(path, { recursive: true, force: true });  // 디렉토리 강제 삭제
fs.unlinkSync(file);  // 동기적 파일 삭제
process.exit(0);  // 강제 프로세스 종료

// ✅ 안전한 대안
try {
  await fs.promises.unlink(file);
} catch (error) {
  console.error('파일 삭제 실패:', error);
}
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


```typescript
// ❌ 보안 위험
eval(userInput);  // 사용자 입력 실행 금지
new Function(userCode)();  // 동적 함수 생성 금지
innerHTML = userContent;  // XSS 위험

// ✅ 안전한 대안
textContent = userContent;
DOMPurify.sanitize(userContent);
```


// ❌ 절대 사용 금지
window.alert('메시지');
window.electronAPI.dialog.showAlert('메시지');
```

### ⚡ 성능 및 메모리 관리

#### 1. 메모리 누수 방지
```typescript
// ✅ 이벤트 리스너 정리
class NoteEditor {
  private listeners: (() => void)[] = [];

  addListener(element: Element, event: string, handler: () => void) {
    element.addEventListener(event, handler);
    this.listeners.push(() => element.removeEventListener(event, handler));
  }

  dispose() {
    this.listeners.forEach(cleanup => cleanup());
    this.listeners = [];
  }
}
```

#### 2. 대용량 파일 처리
```typescript
// ✅ 스트림 사용으로 메모리 효율성
const saveNoteStream = (note: Note) => {
  const stream = fs.createWriteStream(notePath);
  stream.write(JSON.stringify(note));
  stream.end();
};
```

### 🔍 코드베이스 분석 - Gemini CLI 활용

#### 대량 토큰 소모 작업은 Gemini CLI 사용
```bash
# 전체 프로젝트 구조 분석
gemini --all_files -p "이 노트앱 프로젝트의 아키텍처를 분석하고 개선점을 제안해주세요"

# 특정 기능 구현 확인
gemini -p "@src/renderer/ @src/main/ 다크모드가 제대로 구현되어 있나요? 관련 파일들을 보여주세요"

# 보안 취약점 검사
gemini -p "@src/ 이 코드베이스에서 보안 취약점이 있는지 확인해주세요. XSS, CSRF, 파일 경로 조작 등을 중점적으로 봐주세요"

# 성능 최적화 분석
gemini -p "@src/renderer/ 렌더러 프로세스에서 성능 병목이 될 수 있는 부분을 찾아주세요"

# 테스트 커버리지 확인
gemini -p "@src/ @tests/ 테스트가 부족한 모듈들을 찾아주세요"
```

#### Claude vs Gemini 사용 구분
- **Claude 사용**: 단일 파일 수정, 작은 기능 구현, 코드 리뷰
- **Gemini 사용**: 전체 프로젝트 분석, 대규모 리팩토링 계획, 아키텍처 검토

## 📋 개발 프로세스

### 1. 기능 개발 워크플로우
```
1. 요구사항 분석
2. 아키텍처 설계
3. 타입 정의 작성
4. 테스트 케이스 작성 (TDD)
5. 기능 구현
6. 코드 리뷰
7. 테스트 실행
8. 문서 업데이트
```

### 2. 커밋 메시지 컨벤션
```
feat: 새로운 기능 추가
fix: 버그 수정
docs: 문서 변경
style: 코드 포맷팅
refactor: 코드 리팩토링
test: 테스트 코드 추가/수정
chore: 빌드 설정 변경
```

### 3. 브랜치 전략
```
main: 배포 가능한 안정 버전
develop: 개발 중인 기능들 통합
feature/기능명: 새로운 기능 개발
hotfix/버그명: 긴급 버그 수정
```

## 🛠️ 도구 및 라이브러리 관리

### 1. 의존성 관리
```json
{
  "dependencies": {
    "electron": "^latest",
    "typescript": "^latest"
  },
  "devDependencies": {
    "jest": "^latest",
    "eslint": "^latest",
    "prettier": "^latest"
  }
}
```

### 2. 코드 품질 도구
```json
// .eslintrc.json
{
  "extends": [
    "@typescript-eslint/recommended",
    "prettier"
  ],
  "rules": {
    "no-console": "warn",
    "no-eval": "error",
    "@typescript-eslint/no-any": "error"
  }
}
```

## 📚 문서화 규칙

### 1. 코드 문서화
```typescript
/**
 * 노트를 저장하는 함수
 * @param note - 저장할 노트 객체
 * @returns 저장 성공 여부
 * @throws {Error} 파일 시스템 오류 시
 */
async function saveNote(note: Note): Promise<boolean> {
  // 구현
}
```

### 2. README 구조
```markdown
# 프로젝트명
## 설치 방법
## 사용법
## 개발 환경 설정
## 기여 방법
## 라이센스
```

## 🎯 마무리 체크리스트

개발 완료 전 반드시 확인:
- [ ] TypeScript 컴파일 오류 없음
- [ ] 모든 테스트 통과
- [ ] ESLint/Prettier 검사 통과
- [ ] 보안 취약점 검사 완료
- [ ] 메모리 누수 검사 완료
- [ ] 크로스 플랫폼 동작 확인
- [ ] 접근성 검사 완료
- [ ] 다국어 지원 확인
- [ ] 문서 업데이트 완료

---

**⚠️ 이 가이드를 준수하여 안전하고 효율적인 노트앱을 개발하시기 바랍니다.**