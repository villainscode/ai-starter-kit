# Design System - Core Principles

> 디자인 시스템 핵심 원칙 분석 문서  
> 분석 대상: toss.im, toss.tech, tossinvest.com

---

## 📐 Design Philosophy

### 1. Simplicity First (단순함 우선)
- **복잡함을 제거**: 금융 서비스의 복잡성을 최소화하고 본질에 집중
- **직관적 인터페이스**: 설명 없이도 이해 가능한 UI 구성
- **기능 중심 디자인**: 불필요한 장식 요소 배제, 기능에 집중

### 2. Accessibility & Readability (접근성과 가독성)
- **고대비 컬러**: WCAG 2.1 AA 이상의 명도 대비 준수
- **명확한 타이포그래피**: 충분한 크기와 여백으로 가독성 확보
- **일관된 시각적 위계**: 정보의 중요도에 따른 명확한 구분

### 3. Trust & Reliability (신뢰와 안정성)
- **일관된 브랜드 아이덴티티**: 모든 터치포인트에서 동일한 경험 제공
- **안정적인 색상 시스템**: 블루 계열의 신뢰감 있는 Primary 컬러
- **명확한 상태 표현**: 성공/오류/경고 상태의 직관적 시각화

---

## 🎨 Color System

> **📄 전체 컬러 시스템 정의**: [`colors.json`](./colors.json) 파일 참조  
> CSS Variables, Tailwind Config, 사용 가이드라인 포함

### Primary Colors (주요 색상)

```css
/* Brand Blue - 토스 시그니처 블루 */
--toss-blue-primary: #0064FF;      /* 메인 CTA, 링크 */
--toss-blue-secondary: #3182F6;    /* 인터랙티브 요소 */
--toss-blue-light: #E8F3FF;        /* 배경, 뱃지 */

/* Grayscale - 중립 색상 */
--toss-gray-900: #191919;          /* 주요 텍스트 */
--toss-gray-800: #191F28;          /* 헤딩 */
--toss-gray-700: #333D4B;          /* 서브 헤딩 */
--toss-gray-600: #4E5968;          /* 본문 텍스트 */
--toss-gray-500: #6B7684;          /* 보조 텍스트 */
--toss-gray-400: #8B95A1;          /* Placeholder */
--toss-gray-300: #B0B8C1;          /* 비활성화 텍스트 */
--toss-gray-200: #E5E8EB;          /* 구분선 */
--toss-gray-100: #F2F4F6;          /* 배경 */
--toss-gray-50: #F9FAFB;           /* 라이트 배경 */

/* Background */
--toss-white: #FFFFFF;
--toss-background-light: #F8F9FA;
--toss-background-gray: #F2F4F6;
```

### Semantic Colors (의미론적 색상)

```css
/* Success - 성공/상승/긍정 */
--toss-success: #16C784;
--toss-success-light: #E6F9F3;
--toss-success-dark: #00B56A;

/* Error/Danger - 오류/하락/부정 */
--toss-error: #EA3943;
--toss-error-light: #FFF0F1;
--toss-error-dark: #D32F2F;

/* Warning - 경고 */
--toss-warning: #FFA726;
--toss-warning-light: #FFF4E5;
--toss-warning-dark: #F57C00;

/* Info - 정보 */
--toss-info: #3182F6;
--toss-info-light: #E8F3FF;
--toss-info-dark: #1E6FDB;
```

### Interactive States (인터랙션 상태)

```css
/* Hover States */
--toss-hover-blue: #0055DD;
--toss-hover-gray: #E5E8EB;

/* Active/Pressed States */
--toss-active-blue: #0047BB;
--toss-active-gray: #D1D6DB;

/* Focus States */
--toss-focus-ring: rgba(49, 130, 246, 0.4);

/* Disabled States */
--toss-disabled-bg: #F2F4F6;
--toss-disabled-text: #B0B8C1;
```

---

## 📝 Typography

### Font Family
```css
/* 한글 폰트 */
font-family: 'Pretendard', 'Spoqa Han Sans Neo', -apple-system, BlinkMacSystemFont, system-ui, sans-serif;

/* 영문/숫자 폰트 */
font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Helvetica Neue', Arial, sans-serif;
```

### Type Scale

```css
/* Heading */
--text-h1: 72px / 1.2 / 700;      /* 메인 히어로 */
--text-h2: 56px / 1.25 / 700;     /* 섹션 헤딩 */
--text-h3: 40px / 1.3 / 700;      /* 서브 헤딩 */
--text-h4: 32px / 1.4 / 600;      /* 카드 타이틀 */
--text-h5: 24px / 1.4 / 600;      /* 서브 타이틀 */
--text-h6: 20px / 1.5 / 600;      /* 작은 타이틀 */

/* Body */
--text-body-lg: 18px / 1.6 / 400;  /* 큰 본문 */
--text-body: 16px / 1.6 / 400;     /* 일반 본문 */
--text-body-sm: 14px / 1.5 / 400;  /* 작은 본문 */
--text-caption: 12px / 1.4 / 400;  /* 캡션 */

/* Button/Label */
--text-button-lg: 16px / 1.5 / 600;
--text-button: 14px / 1.5 / 600;
--text-button-sm: 12px / 1.4 / 600;
```

### Font Weight
```css
--font-regular: 400;
--font-medium: 500;
--font-semibold: 600;
--font-bold: 700;
```

---

## 📏 Spacing System

### 8px Base Grid
```css
--space-1: 4px;    /* 0.25rem */
--space-2: 8px;    /* 0.5rem */
--space-3: 12px;   /* 0.75rem */
--space-4: 16px;   /* 1rem */
--space-5: 20px;   /* 1.25rem */
--space-6: 24px;   /* 1.5rem */
--space-8: 32px;   /* 2rem */
--space-10: 40px;  /* 2.5rem */
--space-12: 48px;  /* 3rem */
--space-16: 64px;  /* 4rem */
--space-20: 80px;  /* 5rem */
--space-24: 96px;  /* 6rem */
--space-32: 128px; /* 8rem */
```

### Layout Spacing
```css
/* Container Padding */
--container-padding-mobile: 20px;
--container-padding-tablet: 40px;
--container-padding-desktop: 60px;

/* Section Spacing */
--section-spacing-sm: 60px;
--section-spacing-md: 100px;
--section-spacing-lg: 140px;

/* Component Spacing */
--card-padding: 24px;
--button-padding: 12px 24px;
--input-padding: 12px 16px;
```

---

## 🔲 Layout & Grid

### Breakpoints
```css
--breakpoint-mobile: 375px;
--breakpoint-tablet: 768px;
--breakpoint-desktop: 1024px;
--breakpoint-wide: 1440px;
--breakpoint-ultrawide: 1920px;
```

### Container
```css
--container-max-width: 1200px;
--container-wide-max-width: 1440px;
```

### Grid System
```css
/* 12 Column Grid */
--grid-columns: 12;
--grid-gutter: 24px;
--grid-gutter-mobile: 16px;
```

---

## 🎭 Component Patterns

### Border Radius
```css
--radius-sm: 4px;     /* 작은 요소 */
--radius-md: 8px;     /* 버튼, 인풋 */
--radius-lg: 12px;    /* 카드 */
--radius-xl: 16px;    /* 큰 카드 */
--radius-2xl: 24px;   /* 모달 */
--radius-full: 9999px; /* 원형 버튼 */
```

### Shadows
```css
--shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
--shadow-md: 0 4px 6px rgba(0, 0, 0, 0.07);
--shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
--shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.15);
--shadow-card: 0 2px 8px rgba(0, 0, 0, 0.08);
```

### Transitions
```css
--transition-fast: 150ms ease-in-out;
--transition-base: 200ms ease-in-out;
--transition-slow: 300ms ease-in-out;
--transition-slower: 500ms ease-in-out;
```

---

## 🎨 Design Patterns

### 1. Cards
- **Border**: None (그림자로 구분)
- **Border Radius**: 12px~16px
- **Padding**: 24px
- **Shadow**: 미묘한 그림자 (0 2px 8px rgba(0,0,0,0.08))
- **Hover**: 그림자 강화 + 약간의 상승 효과

### 2. Buttons

#### Primary Button
```css
background: var(--toss-blue-primary);
color: white;
border-radius: 8px;
padding: 12px 24px;
font-weight: 600;
transition: all 0.2s ease;

/* Hover */
background: var(--toss-hover-blue);
transform: translateY(-1px);
```

#### Secondary Button
```css
background: var(--toss-gray-100);
color: var(--toss-gray-900);
border-radius: 8px;
padding: 12px 24px;
font-weight: 600;
```

### 3. Input Fields
```css
border: 1px solid var(--toss-gray-200);
border-radius: 8px;
padding: 12px 16px;
font-size: 16px;

/* Focus */
border-color: var(--toss-blue-primary);
box-shadow: 0 0 0 3px var(--toss-focus-ring);
```

### 4. Navigation
- **Fixed Header**: 고정 헤더 (모바일/데스크톱)
- **Height**: 64px (Desktop), 56px (Mobile)
- **Background**: 화이트 + 미묘한 그림자
- **Logo**: 왼쪽 정렬
- **CTA**: 오른쪽 정렬 (블루 버튼)

---

## ✨ Animation Principles

### 1. Micro-interactions
- **Hover 효과**: 0.2s ease-in-out
- **버튼 클릭**: Scale down (0.97) + 색상 변화
- **Focus 상태**: Ring animation (0.15s)

### 2. Page Transitions
- **Fade In**: Opacity 0 → 1 (0.3s)
- **Slide Up**: translateY(20px) → 0 (0.4s)
- **Stagger**: 요소별 순차 애니메이션 (0.1s delay)

### 3. Scroll Animations
- **Parallax**: 배경 이미지 느린 스크롤
- **Reveal on Scroll**: Intersection Observer 활용
- **Progress Indicators**: 스크롤 진행도 시각화

---

## 📱 Responsive Design

### Mobile First Approach
1. **Mobile (375px~)**: 기본 디자인
2. **Tablet (768px~)**: 2컬럼 레이아웃
3. **Desktop (1024px~)**: 3~4컬럼 레이아웃
4. **Wide (1440px~)**: 최대 너비 제한

### Key Responsive Patterns
- **Stack to Grid**: 모바일 세로 → 데스크톱 그리드
- **Hide/Show**: 뷰포트별 요소 표시/숨김
- **Typography Scale**: 모바일 축소 → 데스크톱 확대
- **Spacing**: 모바일 좁게 → 데스크톱 넓게

---

## 🎯 Best Practices

### 1. Color Usage
✅ **DO**
- Primary 색상은 주요 CTA에만 사용
- 충분한 명도 대비 (4.5:1 이상)
- 그레이스케일로 계층 구조 표현

❌ **DON'T**
- 과도한 컬러 사용 지양
- 낮은 대비의 텍스트 사용 금지
- Primary 색상 남용 금지

### 2. Typography
✅ **DO**
- 명확한 타입 위계 유지
- 충분한 line-height (1.5 이상)
- 적절한 font-weight 사용

❌ **DON'T**
- 3가지 이상 폰트 사용 금지
- 과도하게 작은 폰트 (12px 미만)
- 긴 텍스트에 짧은 line-height

### 3. Layout
✅ **DO**
- 8px 기반 스페이싱 시스템
- 넉넉한 화이트스페이스
- 일관된 컴포넌트 패딩

❌ **DON'T**
- 불규칙한 간격 사용
- 과밀한 레이아웃
- 일관성 없는 정렬

### 4. Interaction
✅ **DO**
- 명확한 호버/포커스 상태
- 부드러운 트랜지션 (0.2s)
- 즉각적인 피드백

❌ **DON'T**
- 느린 애니메이션 (1s 이상)
- 불필요한 모션 효과
- 접근성 무시한 인터랙션

---

## 🚀 Implementation Guidelines

### CSS Variables Setup
```css
:root {
  /* Include colors.json values */
  
  /* Typography */
  --font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, sans-serif;
  
  /* Spacing */
  --spacing-unit: 8px;
  
  /* Transitions */
  --transition-base: 200ms ease-in-out;
}
```

### Component Structure
```tsx
// 예시: Button Component
interface ButtonProps {
  variant: 'primary' | 'secondary' | 'ghost';
  size: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
}

const Button: React.FC<ButtonProps> = ({ variant, size, children }) => {
  return (
    <button 
      className={`btn btn-${variant} btn-${size}`}
      style={{
        borderRadius: 'var(--radius-md)',
        transition: 'var(--transition-base)',
      }}
    >
      {children}
    </button>
  );
};
```

---

## 📚 References

- **Toss Main**: https://toss.im/
- **Toss Tech Blog**: https://toss.tech/
- **Toss Invest**: https://www.tossinvest.com/

---

## 🔗 Related Files

- **colors.json**: 전체 컬러 시스템 변수 정의

---

**Last Updated**: 2025-10-24  
**Version**: 1.0.0
