# QR Studio — Generador de Códigos QR

## Concepto & Visión

**QR Studio** es un generador de códigos QR que se siente como una herramienta de diseño profesional, no un utilitario genérico. La experiencia transmite precisión, minimalismo sofisticado y atención al detalle. Cada QR generado es una pieza visual en sí misma — el usuario no solo "crea un QR", está "componiendo" algo.

El tono es: **"estudio de artista digital meets herramienta de precisión japonesa"** — limpio, considerado, satisfactorio de usar.

## Design Language

### Aesthetic Direction
Minimalismo oscuro con acentos cromáticos vibrantes. Inspirado en interfaces de software de diseño profesional (Figma, Linear) con toques de brutalismo tipográfico.

### Color Palette
- **Background**: `#0C0C0E` (negro profundo, casi tinta)
- **Surface**: `#161619` (elevated cards)
- **Surface Hover**: `#1E1E22`
- **Border**: `#2A2A2F` (sutil, casi imperceptible)
- **Text Primary**: `#FAFAFA`
- **Text Secondary**: `#8A8A8E`
- **Accent**: `#FF6B35` (naranja vibrante — energía sin ser agresivo)
- **Accent Glow**: `rgba(255, 107, 53, 0.15)`
- **Success**: `#4ADE80`

### Typography
- **Display**: "Outfit" (700) — títulos, el logo
- **Body**: "Outfit" (400, 500) — inputs, labels
- **Mono**: "JetBrains Mono" — el link mostrado, el QR output como SVG

### Spatial System
- Base unit: 8px
- Card padding: 32px (24px mobile)
- Border radius: 12px (cards), 8px (inputs/buttons)
- Max content width: 480px (centrado)

### Motion Philosophy
- **Entrada**: Fade + slide up suave (300ms ease-out)
- **Generación de QR**: El QR aparece con un scale sutil desde 0.95 → 1 + fade
- **Hover states**: 150ms transitions, nunca instantáneo
- **Focus**: Ring de accent con glow sutil

### Visual Assets
- Iconos: Lucide Icons (outline, 1.5px stroke)
- QR generador: qrcode-generator (CDN)
- Sin imágenes de stock — todo es geometría pura

## Layout & Structure

### Página Única, Tres Estados

```
┌─────────────────────────────────────┐
│           [Logo: QR Studio]          │  ← Minimal header
├─────────────────────────────────────┤
│                                      │
│    ┌─────────────────────────────┐   │
│    │      Link Input Area        │   │  ← Input card
│    │   [URL input + Generate btn] │   │
│    └─────────────────────────────┘   │
│                                      │
│    ┌─────────────────────────────┐   │
│    │                             │   │
│    │         QR Preview          │   │  ← Output card (aparece cuando hay QR)
│    │        [SVG del QR]         │   │
│    │                             │   │
│    │   [Download PNG] [Download]  │   │  ← Action buttons
│    └─────────────────────────────┘   │
│                                      │
│           [Footer: hint text]         │
└─────────────────────────────────────┘
```

### Responsive
- Mobile-first
- En móvil: padding 16px, card padding 24px
- Input y output cards tienen el mismo estilo para consistencia

## Features & Interactions

### 1. Input de URL
- Placeholder: "Pegá tu link acá..."
- Validación: URL válida (http/https) o se normaliza automáticamente
- On change: Validación en tiempo real, border cambia a rojo si inválido
- On focus: Ring de accent con glow sutil
- Auto-focus al cargar la página

### 2. Botón Generar
- Estado default: Background accent, texto oscuro
- Estado hover: Brillo aumentado, cursor pointer
- Estado disabled: Opacity 0.5, cursor not-allowed (cuando input vacío)
- Estado loading: Spinner inline (en caso de generación lenta)
- Click: Genera QR y hace scroll suave al preview

### 3. Preview del QR
- Aparece con animación (scale + fade) cuando hay un QR válido
- QR renderizado como SVG inline (calidad máxima)
- Fondo del QR: blanco puro (#FFFFFF) para escaneo óptimo
- Tamaño: 280x280px
- Error correction level: M (medium) — balance entre densidad y tolerancia

### 4. Descargar
- **Download PNG**: Exporta como imagen PNG 1024x1024px
- **Download SVG**: Exporta el SVG raw
- Botones con iconos de Lucide
- Hover: Background lighten + translateY(-2px)

### 5. Estados de Error
- URL vacía al presionar generar: Shake animation en el input + placeholder cambia a "Tenés que poner un link"
- URL claramente inválida: Border rojo + mensaje "No parece una URL válida"

### 6. Estados Vacíos
- Preview card visible pero con placeholder: "Tu QR va a aparecer acá" con ícono de QR placeholder (grid de puntos)

## Component Inventory

### Logo
- Texto "QR" en accent + "Studio" en blanco
- Font: Outfit 700, 24px
- Letter-spacing: -0.5px

### Input Field
- Full width, height: 52px
- Background: surface
- Border: 1px solid border
- Placeholder: text secondary
- Focus: border → accent, box-shadow: 0 0 0 3px accent-glow
- Error: border → #EF4444, box-shadow: 0 0 0 3px rgba(239, 68, 68, 0.15)

### Primary Button (Generar)
- Height: 52px, padding: 0 28px
- Background: accent, color: #0C0C0E
- Font: Outfit 600, 15px
- Hover: filter brightness(1.1), translateY(-1px)
- Disabled: opacity 0.5, pointer-events none
- Active: translateY(0), filter brightness(0.95)

### Secondary Buttons (Download)
- Height: 44px, padding: 0 20px
- Background: transparent, border: 1px solid border
- Color: text primary
- Hover: background surface-hover, border → accent
- Gap entre botones: 12px

### QR Preview Card
- Background: surface
- Border: 1px solid border
- Padding: 32px
- QR centrado con padding interno (el SVG tiene margin auto)

### Hint Text
- Color: text secondary
- Font: 13px
- Centrado, margin-top: 24px

## Technical Approach

### Stack
- HTML5 + CSS3 + Vanilla JavaScript
- Single file (index.html) para máxima portabilidad
- QR generation: qrcode-generator (CDN: https://cdn.jsdelivr.net/npm/qrcode-generator@1.4.4/qrcode.min.js)
- Canvas para exportar PNG
- Google Fonts: Outfit + JetBrains Mono

### QR Generation Logic
```javascript
// Usar qrcode-generator con:
// - Error correction: M (15% recovery)
// - Output: SVG para display inline
// - Canvas para PNG export (1024x1024)
```

### Key Implementation Details
- Validación de URL con regex + fallback de normalización
- Debounce en input para auto-generate (opcional, no crítico)
- Download PNG: crear canvas offscreen, dibujar QR, usar toDataURL
- Download SVG: serializar el SVG generado, crear blob, trigger download
- Smooth scroll al preview con `scrollIntoView({ behavior: 'smooth' })`
