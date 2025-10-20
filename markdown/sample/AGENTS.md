# Repository Guidelines

## Project Structure & Module Organization
- `src/main/`: Electron main process (app lifecycle, IPC, DB).
- `src/renderer/`: React UI and features; shared UI under `components/`.
- `pages/`: Next.js routes for the renderer during dev/SSG.
- `styles/`: Global CSS + component styles (BEM and CSS Modules).
- `public/`: Static assets served by Next/Electron.
- `test/e2e/`: Playwright specs; see `playwright.config.ts`.
- `config/`: App settings and environment-specific config.
- `dist/`, `out/`, `release/`: Build outputs (gitignored).

## Build, Test, and Development Commands
- `npm run dev`: Run Next.js (port 13333) and Electron together.
- `npm run dev:next`: Start only Next dev server.
- `npm run dev:electron`: Start only Electron (waits for 13333).
- `npm run build`: Production build for Next + Electron.
- `npm run start`: Launch the built desktop app.
- `npm run clean:dev`: Free port 13333 if stuck processes exist.
- `npm run type-check`: TypeScript type checks (no emit).
- `npm run lint`: ESLint for TS/React (Next rules).
- `npm test`: Run Playwright E2E (Chromium only); `test:ui`, `test:report`, `test:headed` available.

## Coding Style & Naming Conventions
- Language: TypeScript across main and renderer.
- Indentation: 2 spaces; avoid trailing whitespace.
- React: Function components; PascalCase for components, camelCase for vars.
- Files: `PascalCase.tsx` for components, `kebab-case.css` or `*.module.css` for styles.
- Linting: ESLint (`.eslintrc.json`); fix warnings where feasible before PR.
- Prefer explicit types at module boundaries; keep IPC contracts in `src/shared/`.

## UI 공통 컴포넌트 사용 정책
- 모달: 반드시 공통 모달을 사용합니다.
    - DOM 구조: `.common-modal-overlay` + `.common-modal[data-type]`
    - 헤더: `.common-modal-header > .common-modal-title` 내부에 `.common-modal-icon`(material-symbols-outlined) + `.common-modal-title-text`
    - 바디: `.common-modal-body > .common-modal-message`
    - 푸터 버튼: `.common-modal-button` + `common-modal-button-primary|secondary`
- 에디터 플러그인에서는 `EditorModal` 유틸을 통해 공통 모달을 호출합니다. (내부 구현이 공통 모달 DOM을 생성)
- 기존 `lite-editor-modal` 스타일/구현은 더 이상 사용하지 않습니다.

## Testing Guidelines
- Framework: Playwright E2E in `test/e2e/*.spec.ts` (Chromium only; Firefox/WebKit 미사용).
- Local run: `npm run dev` then `npm test` (server on 13333 must be up).
- Debug: `npm run test:ui` for interactive, `test:report` to open HTML report.
- Selectors: Prefer stable attributes (e.g., `data-testid`), avoid brittle CSS.

## Commit & Pull Request Guidelines
- Messages: Use bracketed type + short scope, imperative summary.
    - Examples: `[feature] editor: add bold shortcut`, `[fix] db: prevent null titles`.
- PRs must include: purpose, linked issue (`Fixes #123`), test plan, and screenshots/GIFs for UI changes.
- Pre-push checklist: `npm run type-check && npm run lint && npm test` (or targeted specs).

## Security & Configuration Tips
- Environment: copy `.env.example` to `.env` (do not commit secrets).
- Node: use version `22.15.0` per `package.json` engines.
- Artifacts and local DB/backups should remain untracked (see `.gitignore`).

## Windows Compatibility Guide (필수 적용)
Windows 환경(파일 업로드/다운로드, DB 백업/복구, Markdown/HTML Import·Export 등)에서의 경로 처리와 파일 시스템 동작을 안전하게 보장하기 위한 가이드입니다. 코드 수정이 필요한 사안은 반드시 사전 승인 후 진행하며, 아래 수칙을 준수합니다.

- 경로/URL 표준화
    - 디스크 경로는 언제나 `path.join()`을 사용합니다. HTML/저장용 URL은 반드시 POSIX 슬래시(`/`)로 정규화합니다.
    - 서비스 계층에서 생성하는 상대경로는 저장 직전에 `relativePath.replace(/\\/g, '/')`로 한 번 더 정규화합니다.
    - 렌더러에서 URL을 만들 때도 백슬래시 대비 가드를 추가합니다. 예) `const src = '/' + relativePath.replace(/\\/g,'/').replace(/^public\//,'');`

- 파일 리소스 접근 프로토콜
    - 프로덕션에서 `/file/...` → `<userData>/public/file/...` 매핑은 Electron `session.webRequest.onBeforeRequest`로 선제 등록(메인 윈도우 `loadURL` 이전)합니다.
    - 매칭 시 Windows 백슬래시를 슬래시로 치환 후 비교합니다. 또한 `/public/file/...`도 동일하게 리다이렉트 허용합니다.
    - 앱 내부 로딩은 `app://` 또는 전용 `qnote-file://`(custom) 프로토콜을 사용하며, HTML에는 상대 `/file/...`만 기록합니다(절대 `file:///C:/...` 금지).

- 이미지 업로드/삽입
    - 메인: 저장 상대경로 생성 시 `path.join('public','file','image', ...)` → 반환하기 전 `replace(/\\/g,'/')` 필수.
    - 렌더러: 업로드 응답의 `relativePath`는 항상 슬래시 정규화 후 `/file/...`로 변환하여 `img.src`에 사용합니다.
    - Sanitize 정책에 `data:`(dataURL)·내부 경로(`/file/…`)만 허용합니다. 외부 `http(s)`는 사용자 액션에 한정합니다.

- 첨부파일(업/다운로드, 열기)
    - 파일명 추출은 분리자 양쪽 모두 대응: `filePath.split(/[\\/]/).pop()`.
    - 열기/다운로드 시 원본이 상대경로면 `<userData>/public/file` 우선, 없으면 `process.cwd()/public/file` 후보 순으로 접근합니다.
    - 저장 다이얼로그 기본 파일명은 Windows 금지문자 제거: `name.replace(/[<>:"/\\|?*]/g,'_')`.

- DB 백업/복구(zip)
    - 백업 파일 경로는 `.zip` 확장자 보정, 스트림 오류와 권한 오류 분기 로깅 유지.
    - 백업에는 DB 3종(`.db`, `-wal`, `-shm`) + `public/`(파일 리소스)을 포함합니다. 복구 시 임시 디렉토리를 사용하고 롤백 파일을 사전에 생성합니다.
    - 프로덕션 빌드시 `archiver-utils`가 App.asar에 누락되면 백업 모듈 로딩이 실패합니다. `package.json`의 정식 dependencies에 `archiver-utils`를 포함하고, afterPack 단계에서 prune 스크립트가 해당 모듈을 삭제하지 않는지 확인합니다.
    - 경로 연산은 모두 `path.join()`과 `fs` 동기/비동기 API만 사용(셸 호출 금지).

- Markdown/HTML Import·Export
    - Export: HTML 내 `<img src>`는 `/file/...`만 남기도록 재작성(쿼리/fragment, 절대 URL 정리). Windows에서도 슬래시 기준으로만 매칭합니다.
    - Export 파일명은 금지문자 제거. Import 시 텍스트·체크리스트 변환은 브라우저 중립적으로 처리합니다.

- 테스트/검증 체크리스트(Windows)
    - 이미지 업로드 후 에디터 DOM의 `<img src>`가 `/file/...` 형식인지 확인(역슬래시 불가).
    - 첨부파일 다운로드/열기: 사용자 다운로드 경로, 탐색기 열기 동작 확인.
    - DB 백업/복구: zip 생성/해제·무결성·롤백 로그 확인.
    - Markdown/HTML Export: 압축 결과에 이미지가 포함되고 경로 재작성 정상.
    - Dev/Prod 모두에서 `session.webRequest.onBeforeRequest` 리다이렉션이 동작하는지 확인.

- 로깅/진단(개발 모드만)
    - 메인: 파일 경로 매핑(onBeforeRequest) 성공/에러 로깅, 4xx 응답 onCompleted 로깅.
    - 렌더러: 업로드 응답·경로 정규화 결과를 debug 레벨로 1회 로깅(노이즈 최소화).

위 지침은 기능 추가/수정 시 반드시 준수합니다. 경로/프로토콜/보안 정책은 중앙 정책을 우선하며, OS별 분기 대신 “디스크=네이티브 경로, HTML=POSIX 슬래시” 원칙을 고정 규약으로 삼습니다.

# 🚨 Absolute Prohibitions (CRITICAL - Must Follow)

## 🔴 Database Absolute Prohibitions
```bash
# Destructive database commands - NEVER USE (without explicit user request)
npm run reset-db           # ❌ STRICTLY FORBIDDEN - deletes all data
npm run setup-db           # ⚠️ Requires explicit user approval
node scripts/reset-database.js  # ❌ STRICTLY FORBIDDEN

# Destructive SQL commands - NEVER USE
DROP TABLE                 # ❌ STRICTLY FORBIDDEN
DROP DATABASE              # ❌ STRICTLY FORBIDDEN  
DELETE FROM                # ⚠️ Forbidden without WHERE clause
TRUNCATE                   # ❌ STRICTLY FORBIDDEN
ALTER TABLE DROP           # ⚠️ Requires explicit user approval
```

## 🔴 Mandatory Database Rules
1. **Always request explicit user approval before deleting/resetting data**
2. **Never delete data without a backup**
3. **Do not reset while test data exists**
4. **Do not reset database if issue can be solved with SQL modification**
5. **Never auto-modify production database under any circumstances**

## 🔴 Dangerous Git Commands - STRICTLY FORBIDDEN
```bash
git push --force
git reset --hard
git commit --no-verify
```

## 🔴 Dangerous npm Commands
```bash
npm audit fix --force
```

## Library Versions (DO NOT CHANGE)
```json
{
  "react": "^18.3.1",
  "next": "^14.2.16",
  "electron": "33.4.11",
  "better-sqlite3": "11.10.0",
  "typescript": "^5.7.2"
}
```

## Do not introduce libraries, frameworks, or languages outside the base tech stack
- If unavoidable, submit justification and request explicit approval.

## Security Rules
- Do not use `eval()`
- Do not use `innerHTML` directly (use DOMPurify)
- Always validate user input
- Never commit API keys or secrets


# 📋 AI Assistant Core Rules

## 🚨🚨🚨 Top Priority Rule - Database Protection 🚨🚨🚨
**NEVER DO (without explicit user permission):**
- Run `npm run reset-db`
- Execute `DROP TABLE`, `DROP DATABASE`
- Run `DELETE FROM` without WHERE clause
- Delete database files directly
- Reset when test data exists

**If a database issue occurs:**
1. First attempt to solve with code modifications
2. If structural DB change is needed, explain to user
3. Any operation with risk of data loss requires explicit user approval
4. Explicit confirmation message:  
   *"Do you want to reset the database? All data will be deleted."*

## 🔄 Feature Change Checklist
**Principle: Preserve existing features**
1. **Pre-change check**
    - Document current feature list of the component
    - Verify state of related UI elements
    - Check for possible CSS conflicts

2. **Impact analysis**
    - Identify other components using the modified one
    - Check for duplicate CSS class definitions (common, themes, modules)
    - Review conditional rendering logic

3. **Post-change validation**
    - Ensure all existing features still work
    - Verify visibility of UI elements (buttons, menus, etc.)
    - Test various states (collapsed/expanded, etc.)

4. **Immediate rollback if issues arise**
    - Restore immediately if a feature disappears or breaks
    - Analyze cause and retry after fix

## 🎯 Protected Core UI Elements
These elements must always be visible:
- **Sidebar**: Folder creation button, note search
- **Editor**: Toolbar buttons, Save/Cancel buttons
- **Header**: Theme toggle, window control buttons

## 🧊 Code Freeze: Editor IME/Enter/Title (CRITICAL)
다음 영역은 한글 조합(IME) 깨짐과 제목 마지막 글자가 본문(sub-content)에 중복 삽입되던 치명적 버그를 해결한 핵심 경로입니다. 기능 보장을 위해 명시적 승인 없이 코드 변경을 금지합니다.

- Enter/Shift+Enter 처리 전역 플로우 (중앙화 유지)
    - `src/renderer/editor/core/ShortcutManager.ts`
        - Enter 키 전역 처리: 코드 블록 내 제외, `__enterKeyHandled` 플래그 설정
        - document-level/key capture 순서 보장 (중복 처리 금지)
    - `src/renderer/editor/plugins/actions/EnterKeyPlugin.ts`
        - `beforeinput(insertParagraph)`에서만 단락 생성 처리
        - IME 조합 중(`isComposing`) DOM 조작 금지, `compositionend` 후 래핑 수행
        - `normalizeContent()/wrapLooseTextInParagraph()`의 selection 보존 로직

- 제목 입력과 에디터 포커스 전환
    - `src/renderer/components/content/DocumentEditor.tsx`
        - 제목 입력에서 IME 조합 중(Tab/Focus 이동 금지)
        - 제목 변경과 에디터 초기화/포커스 순서 유지

- 공통 불변 조건 (변경 금지)
    - IME 조합 중에는 DOM 정규화/래핑/selection 이동을 실행하지 않는다
    - Enter키는 EnterKeyPlugin이 처리하고, ShortcutManager는 중복 처리 방지 플래그만 설정한다
    - 첫 블록 보정: 빈 `<p><br>` 초기화와 selection 앵커 삽입 순서 유지

상기 파일/로직은 “코드 수정 금지 항목”으로 관리합니다. 수정이 불가피하면 사전 승인 + 회귀 테스트(E2E) 추가가 필수입니다.

## Language & Communication
- All explanations provided in Korean
- Treat user as expert; minimize unnecessary explanations
- Provide concise, clear answers
- Keep technical terms in original form where needed

## Code Writing Principles
- Always complete and bug-free code (no TODOs, no placeholders)
- Prioritize readability
- Enforce strict TypeScript mode
- Follow SOLID principles & Clean Architecture
- Apply DRY principle – eliminate duplicate code, use shared functions

### Scope Discipline (STRICT)
- Do NOT implement any feature, UI, API, or behavior that is not explicitly requested or already specified in this document.
- When requirements are ambiguous, ask for clarification first. Default to preserving existing behavior.
- Experimental or “nice-to-have” changes are forbidden without explicit approval.

### Mandatory Performance & Memory Check (AFTER EVERY CHANGE)
- After each code modification, perform a quick performance/memory review and fix findings before finishing:
    - Events: no duplicate listeners; remove on dispose; avoid global leaks.
    - DOM: detach temporary nodes; avoid unbounded inner loops; prefer class toggles over inline styles.
    - Data: no growing arrays/Maps/Sets without cleanup; cap caches/buffers.
    - Rendering: avoid layout thrashing; batch style reads/writes; prefer CSS over JS where possible.
    - IPC/Async: debounce/throttle noisy calls; avoid hot-path synchronous work on the main thread.
    - Memory: prefer WeakMap/WeakRef for object-keyed state; clear references on teardown.
- Confirm in the final handoff that the perf/memory check was performed and issues addressed.

## Modification Guidelines
- Always check current file state before editing
- State whether existing features will be affected
- Require prior approval for changes to icons, fonts, etc.

# 🔍 Refactoring Guidelines
- Goal: Improve code structure without changing functionality
- Must pass all tests
- Automatically detect and remove dead code
- Files exceeding 500 lines → require explicit approval before refactor
- Before refactor, analyze with GEMINI, SERENA MCP in Plan mode to check for side effects → request explicit approval before proceeding
- For large-scale refactors, always back up original code first

# 🐛 Debugging Approach
- Explain root cause and solution before proceeding
- Add detailed logs for root cause analysis
- Logs must only appear in develop mode, excluded in production

# 📖 Documentation Rules
- Major components: write `/docs/[component].mdc`
- Update docs alongside code
- Complex logic must be explained with comments
- All docs and comments must be written in Korean
