# Подробнее о UX/UI

| Грейд | Роль |
|-------|------|
| 🔵 Middle | 🅱️🅰️ BA+SA |

> **Подробное руководство** по основным компонентам UI дизайна и UX практикам для аналитиков, которые работают с дизайнерами.

## UI Components Deep Dive

### Layout Systems

#### 1. Grid Layout

Элементы располагаются по сетке для консистентности:

```
┌──────────┬──────────┬──────────┐
│ Col 1    │ Col 2    │ Col 3    │
│ (4 unit) │ (4 unit) │ (4 unit) │
├──────────┼──────────┼──────────┤
│ Col 1    │ Col 2    │ Col 3    │
├──────────┴──────────┴──────────┤
│ Span All (12 unit)             │
└────────────────────────────────┘
```

**Для аналитика:** При описании экрана используйте сетку (12 column grid стандарт).

#### 2. Flexbox Layout

Гибкое распределение пространства:

```
┌───────────────────────────────┐
│ [Button] [Button] [Button]    │ ← Stretch равномерно
│ [Input field..................] │ ← Занимает все место
└───────────────────────────────┘
```

#### 3. Responsive Design

Один дизайн для разных экранов:

```
Desktop (1200px):          Tablet (768px):         Mobile (375px):
┌─────────────────┐        ┌──────────────┐        ┌─────────┐
│ Nav | Content   │        │ Content      │        │ Content │
│     | Sidebar   │        │              │        │         │
└─────────────────┘        └──────────────┘        └─────────┘
```

---

### Colors in Detail

#### Color Palette Structure

**Base Colors:**
```
Primary (Brand): #007AFF (Blue)
Secondary: #5AC8FA (Light Blue)
Accent: #FF3B30 (Red)
```

**Semantic Colors:**
```
Success: #34C759 (Green) — положительные действия
Error: #FF3B30 (Red) — ошибки и опасность
Warning: #FF9500 (Orange) — предупреждения
Info: #0A84FF (Blue) — информационные сообщения
```

**Neutral Colors:**
```
Background: #FFFFFF
Surface: #F2F2F7 (Light gray)
Border: #E5E5EA (Dividers)
Text Primary: #000000
Text Secondary: #666666
Disabled: #CCCCCC
```

#### Color Usage Rules

```
Button Primary → Primary color
Button Secondary → Secondary color
Error message → Error color
Disabled state → Disabled color
Link → Accent color
```

---

### Typography System

#### Type Scale

```
H1: 32px, Bold (Главный заголовок)
H2: 24px, Bold (Подзаголовок)
H3: 18px, Bold (Малый заголовок)
Body: 16px, Regular (Основной текст)
Small: 14px, Regular (Вспомогательный текст)
Tiny: 12px, Regular (Подписи)
```

#### Line Height

```
Заголовок: 1.2 × font-size (компактный)
Body text: 1.5 × font-size (удобный для чтения)
Small text: 1.4 × font-size
```

#### Font Pairings

```
Serif (书籍) + Sans-serif (современный):
Georgia + Open Sans

Modern (минимализм):
Montserrat + Roboto

Technical (code):
Monaco / Courier (monospace)
```

---

### Icons System

#### Icon Sizes

```
Tiny: 16px (inline with text)
Small: 24px (list items, buttons)
Medium: 32px (standalone, featured)
Large: 48px (hero section)
XL: 64px+ (main focus)
```

#### Icon States

```
Default: #333333 (normal color)
Hover: #007AFF (primary color)
Disabled: #CCCCCC (gray)
Active: #007AFF (highlighted)
```

#### Icon Categories

**Functional icons:**
- Search 🔍, Delete ❌, Edit ✏️, Save 💾
- More ⋯, Menu ☰, Close ✕, Back ◀

**Status icons:**
- Success ✓, Error ✕, Warning ⚠️, Loading ⟳

**Action icons:**
- Add ➕, Remove ➖, Download ⬇️, Upload ⬆️

---

### Spacing System

#### 8px Grid System

```
8px → 1 unit
16px → 2 units
24px → 3 units
32px → 4 units
48px → 6 units
64px → 8 units

Используй только эти значения!
```

#### Spacing Rules

```
Внутри компонента (padding):
- Small: 8px (плотно)
- Medium: 16px (нормально)
- Large: 24px (просторно)

Между компонентами (margin):
- Small: 8px
- Medium: 16px
- Large: 24px
- XL: 32-48px
```

#### Practical Example

```
┌─────────────────────┐
│  ↓ 16px padding     │
│  [Input field] 8px  │ ← padding 8px
│  ↓ 24px gap         │
│  [Button]           │
│  ↓ 16px padding     │
└─────────────────────┘
```

---

### Components & States

#### Button Component

**States:**

```
Default:   [Click me] (Blue)
Hover:     [Click me] (Darker blue)
Active:    [Click me] (Even darker)
Disabled:  [Click me] (Gray)
Loading:   [Click me ⟳] (Gray with spinner)
```

**Sizes:**

```
Small:    12px text, 24px height, 8px padding
Medium:   14px text, 32px height, 12px padding
Large:    16px text, 40px height, 16px padding
```

**Variants:**

```
Primary:   Blue background (main action)
Secondary: Gray background (less important)
Outline:   Border only (neutral)
Ghost:     Text only (minimal)
Danger:    Red background (destructive)
```

#### Input Field Component

**States:**

```
Default:   [Type here...] (Empty field)
Focused:   [Type here...] (Blue border, cursor)
Filled:    [Hello world] (With content)
Error:     [Bad input] (Red border, error message)
Disabled:  [Cannot type] (Gray, non-interactive)
```

#### Card Component

```
┌──────────────────────┐
│                      │ ← Padding 16px
│  Title               │
│                      │
│  Description text    │
│  Multi-line content  │
│                      │
│  [Button]            │
│                      │ ← Padding 16px
└──────────────────────┘

Border: 1px #E5E5EA
Shadow: 0 2px 4px rgba(0,0,0,0.1)
```

---

## UX Patterns

### Navigation Patterns

#### Top Navigation (Header)

```
┌─────────────────────────────────────┐
│ [Logo] [Home] [Products] [Help] [👤] │
└─────────────────────────────────────┘
```

Когда использовать: Desktop, когда список пунктов небольшой

#### Sidebar Navigation

```
┌──────────┬──────────────────────┐
│ [Home]   │                      │
│ [About]  │  Main Content        │
│ [Shop]   │                      │
│ [Help]   │                      │
└──────────┴──────────────────────┘
```

Когда использовать: Desktop, много пунктов, нужна видимость

#### Tab Navigation

```
[Home] [Products] [Help]
   ↓
┌──────────────────────┐
│  Home content here   │
└──────────────────────┘
```

Когда использовать: Переключение между альтернативными представлениями

#### Bottom Navigation (Mobile)

```
┌────────────────────────┐
│   Main content         │
└────────────────────────┘
[Home] [Shop] [Cart] [👤]
```

Когда использовать: Mobile, 3-5 основных экранов

---

### Form Patterns

#### Single Column Form (Mobile-first)

```
┌────────────────────┐
│ Email*             │
│ [..................]│
│                    │
│ Password*          │
│ [..................]│
│                    │
│ [Remember me] □    │
│                    │
│ [Sign In]          │
└────────────────────┘
```

#### Two Column Form (Desktop)

```
┌─────────────────────────────────────┐
│ First Name*        │ Last Name*     │
│ [...............] │ [...............] │
│                                     │
│ Email*                              │
│ [...................................] │
│                                     │
│ [Sign Up]                           │
└─────────────────────────────────────┘
```

#### Form Validation

```
Good input: [Valid email] ✓
Bad input:  [Bad email] ✕
           "Please enter valid email"

Helpful: Show error BELOW field
Timing: Show error when leave field (onBlur)
```

---

### Feedback Patterns

#### Success Message

```
┌────────────────────────────┐
│ ✓ Account created success │
└────────────────────────────┘

Duration: 3-5 seconds, then auto-dismiss
Color: Green (#34C759)
```

#### Error Message

```
┌────────────────────────────┐
│ ✕ Password too short       │
│ Must be at least 8 chars   │
└────────────────────────────┘

Duration: Stays until fixed
Color: Red (#FF3B30)
Action: Show where error is
```

#### Loading State

```
Loading... ⟳

For short waits (< 1s): Spinner only
For longer waits: Spinner + text
For very long: Progress bar + percentage
```

---

## Design System Documentation

### Component Card Example

```
## Button Component

### Description
Primary action button used for main CTAs.

### States
- Default (Blue)
- Hover (Darker blue)
- Active (Even darker)
- Disabled (Gray)
- Loading (Spinner)

### Sizes
- Small (24px height)
- Medium (32px height)
- Large (40px height)

### Variants
- Primary
- Secondary
- Outline
- Ghost
- Danger

### Code Example
<Button variant="primary" size="medium">
  Click me
</Button>

### Accessibility
- Keyboard accessible
- Color contrast: AA compliant
- Focus indicator: 2px blue border
```

---

## Accessibility (A11y)

### Color Contrast

```
Text on background must have ratio ≥ 4.5:1 (WCAG AA)

✓ Black (#000) on White (#FFF): 21:1 (excellent)
✓ Gray (#666) on White: 7.5:1 (good)
❌ Gray (#999) on White: 2.3:1 (bad, too light)
```

### Interactive Elements

```
Click targets: Min 44×44px (finger-friendly)
Keyboard navigation: Tab between elements
Focus indicator: Clearly visible (not invisible)
Screen readers: Proper labels and descriptions
```

### Color Not Only

```
❌ Error = Red color only
✓ Error = Red color + ✕ icon + text message

❌ Important = Bold text
✓ Important = Bold + larger + color change
```

---

## 📚 Ресурсы для изучения

- **Material Design (Google)** — полная design system
- **Human Interface Guidelines (Apple)** — iOS design
- **Web Content Accessibility Guidelines (WCAG)** — доступность
- **Laws of UX** — 10 принципов UX дизайна
- **Figma Design System Resources** — примеры реальных систем
