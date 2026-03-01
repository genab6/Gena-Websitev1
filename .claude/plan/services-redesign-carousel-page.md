# Implementation Plan: Services Image Redesign + Highlights Carousel Page

## Task Type
- [x] Frontend (Gemini-designed)
- [x] Backend/Engineering (Claude-built)
- [x] Fullstack (Parallel)

---

## Technical Solution

**Synthesized from Gemini (design authority) + Claude (engineering authority)**

### Feature 1: Services Section — Remove Inline Images, Add Side Accents

**Design (Gemini):** Transform from 2-column image+text grid to single-column centered text with decorative images absolutely positioned in the side margins, alternating left/right.

**Engineering (Claude):** Minimal structural change — keep the `services.map()` pattern, remove the `.service-item` 2-column grid, make `.service-img` absolutely positioned. Reuse existing `FadeIn` component for animation.

### Feature 2: Highlights "See More" → Carousel Page

**Design (Gemini):** Ghost button with gold border below gallery grid. Carousel has dark charcoal fullscreen background, cross-fade transitions, minimalist SVG arrows, gold dot indicators.

**Engineering (Claude):** Use conditional rendering (`isGalleryOpen` state) — NO new dependencies needed. Custom carousel with React state + useEffect auto-play + touch events. This avoids adding react-router-dom or any carousel library to this simple portfolio site.

---

## Implementation Steps

### Step 1: Add New State Variables
**File:** `src/App.jsx` (~line 148-167, inside `GBeauty` component)

Add after existing state declarations:
```jsx
const [isGalleryOpen, setGalleryOpen] = useState(false);
const [carouselIdx, setCarouselIdx] = useState(0);
const [carouselPaused, setCarouselPaused] = useState(false);
const highlightsSectionRef = useRef(null);
const touchStartX = useRef(0);
const touchEndX = useRef(0);
```

**Expected deliverable:** New state variables ready for both features.

---

### Step 2: Refactor Services Section CSS
**File:** `src/App.jsx` (~lines 726-760, inside `<style>` tag)

**Remove:**
- `.service-item` 2-column grid layout (`grid-template-columns: 1fr 1fr`)
- `.service-item:nth-child(even)` RTL direction hack
- `.service-img` full-width styling

**Replace with:**
```css
.services-list {
  max-width: 1050px; margin: 0 auto;
  display: flex; flex-direction: column;
  align-items: center; gap: clamp(80px, 10vw, 120px);
}
.service-item {
  position: relative; width: 100%;
  max-width: 700px; text-align: center;
}
.service-img {
  position: absolute; top: 50%;
  transform: translateY(-50%);
  width: clamp(160px, 18vw, 250px);
  aspect-ratio: 4/5;
  opacity: 0.15; z-index: 0;
  transition: opacity 0.8s cubic-bezier(0.22,1,0.36,1);
  pointer-events: none;
}
.service-item:nth-child(odd) .service-img { left: -20%; }
.service-item:nth-child(even) .service-img { right: -20%; left: auto; }
.service-text { position: relative; z-index: 1; }
.service-text h3 {
  font-family: var(--serif); font-weight: 400;
  font-size: clamp(26px, 3vw, 38px);
  letter-spacing: 0.04em; margin-bottom: 16px;
}
.service-text p {
  font-family: var(--sans); font-size: 13.5px;
  line-height: 2; color: var(--text); margin-bottom: 24px;
  max-width: 55ch; margin-left: auto; margin-right: auto;
}
@media (max-width: 768px) {
  .service-img { display: none; }
  .service-item { max-width: 100%; }
}
```

**Expected deliverable:** Services section renders as centered text column with faint decorative images on sides.

---

### Step 3: Update Services Section JSX
**File:** `src/App.jsx` (~lines 1283-1297)

**Modify the `.service-item` rendering:**
```jsx
{services.map((svc, i) => (
  <FadeIn key={i}>
    <div className="service-item">
      <div className="service-img" style={{
        backgroundImage: svc.img,
        backgroundSize: "cover",
        backgroundPosition: "top center"
      }} />
      <div className="service-text">
        <h3>{svc.title}</h3>
        <p>{svc.desc}</p>
        <span className="inquire-link" onClick={() => scrollTo("contact")}>INQUIRE</span>
      </div>
    </div>
  </FadeIn>
))}
```

The JSX structure stays mostly the same — the CSS repositioning does the heavy lifting.

**Expected deliverable:** Service images appear as decorative side accents, not inline.

---

### Step 4: Add "See Full Gallery" Button Below Highlights Grid
**File:** `src/App.jsx` (~line 1314, after the highlights gallery-grid div)

**Add ref to highlights section:**
```jsx
<section id="highlights" className="section section-cream" ref={highlightsSectionRef}>
```

**Add button after gallery grid:**
```jsx
<FadeIn delay={0.15}>
  <div style={{ textAlign: 'center', marginTop: 'clamp(32px, 4vw, 48px)' }}>
    <button className="see-gallery-btn" onClick={() => setGalleryOpen(true)}>
      SEE FULL GALLERY
    </button>
  </div>
</FadeIn>
```

**Add CSS:**
```css
.see-gallery-btn {
  display: inline-block;
  font-family: var(--sans); font-size: 11px;
  letter-spacing: 0.25em; text-transform: uppercase;
  color: var(--gold); background: none;
  border: 1px solid var(--gold);
  padding: 15px 40px; cursor: pointer;
  transition: all 0.3s;
}
.see-gallery-btn:hover {
  background: var(--gold); color: var(--white);
}
```

**Expected deliverable:** "SEE FULL GALLERY" gold ghost button appears below Highlights grid.

---

### Step 5: Build Carousel Component (Inline in App.jsx)
**File:** `src/App.jsx` (add before the `GBeauty` component, ~line 147)

**New component:**
```jsx
function GalleryCarousel({ images, onClose }) {
  const [idx, setIdx] = useState(0);
  const [paused, setPaused] = useState(false);
  const touchStart = useRef(0);
  const touchEnd = useRef(0);
  const len = images.length;

  const next = useCallback(() => setIdx(p => (p + 1) % len), [len]);
  const prev = () => setIdx(p => (p - 1 + len) % len);

  // Auto-play
  useEffect(() => {
    if (paused) return;
    const timer = setTimeout(next, 5000);
    return () => clearTimeout(timer);
  }, [idx, paused, next]);

  // Keyboard navigation
  useEffect(() => {
    const handler = (e) => {
      if (e.key === 'ArrowRight') next();
      if (e.key === 'ArrowLeft') prev();
      if (e.key === 'Escape') onClose();
    };
    window.addEventListener('keydown', handler);
    return () => window.removeEventListener('keydown', handler);
  }, [next, onClose]);

  const handleTouchStart = (e) => { touchStart.current = e.touches[0].clientX; };
  const handleTouchMove = (e) => { touchEnd.current = e.touches[0].clientX; };
  const handleTouchEnd = () => {
    const diff = touchStart.current - touchEnd.current;
    if (diff > 75) next();
    if (diff < -75) prev();
  };

  return (
    <div className="carousel-overlay"
         onMouseEnter={() => setPaused(true)}
         onMouseLeave={() => setPaused(false)}
         onTouchStart={handleTouchStart}
         onTouchMove={handleTouchMove}
         onTouchEnd={handleTouchEnd}>
      <button className="carousel-back" onClick={onClose}>
        ← BACK
      </button>
      <div className="carousel-stage">
        {images.map((img, i) => (
          <div key={i} className={`carousel-slide ${i === idx ? 'active' : ''}`}
               style={{ backgroundImage: img, backgroundSize: 'contain',
                        backgroundPosition: 'center', backgroundRepeat: 'no-repeat' }} />
        ))}
      </div>
      <button className="carousel-arrow carousel-prev" onClick={prev}>
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none"
             stroke="currentColor" strokeWidth="1.5">
          <polyline points="15 18 9 12 15 6" />
        </svg>
      </button>
      <button className="carousel-arrow carousel-next" onClick={next}>
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none"
             stroke="currentColor" strokeWidth="1.5">
          <polyline points="9 6 15 12 9 18" />
        </svg>
      </button>
      <div className="carousel-dots">
        {images.map((_, i) => (
          <button key={i}
                  className={`carousel-dot ${i === idx ? 'active' : ''}`}
                  onClick={() => setIdx(i)} />
        ))}
      </div>
    </div>
  );
}
```

**Expected deliverable:** Self-contained carousel component with all required features.

---

### Step 6: Add Carousel CSS
**File:** `src/App.jsx` (inside `<style>` tag, after lightbox CSS ~line 970)

```css
/* ═══ GALLERY CAROUSEL ═══ */
.carousel-overlay {
  position: fixed; inset: 0; z-index: 300;
  background: #1a1412;
  display: flex; align-items: center; justify-content: center;
}
.carousel-back {
  position: absolute; top: 24px; left: 28px; z-index: 310;
  font-family: var(--sans); font-size: 12px;
  letter-spacing: 0.2em; color: var(--cream);
  background: none; border: 1px solid rgba(255,255,255,0.2);
  padding: 10px 24px; cursor: pointer;
  transition: all 0.3s;
}
.carousel-back:hover { border-color: var(--cream); }
.carousel-stage {
  position: relative; width: 80vw; height: 80vh;
}
.carousel-slide {
  position: absolute; inset: 0;
  opacity: 0;
  transition: opacity 0.8s cubic-bezier(0.22,1,0.36,1);
}
.carousel-slide.active { opacity: 1; }
.carousel-arrow {
  position: absolute; top: 50%; transform: translateY(-50%);
  background: rgba(0,0,0,0.3); border: none;
  color: rgba(255,255,255,0.6); padding: 16px 12px;
  cursor: pointer; transition: all 0.3s; z-index: 310;
}
.carousel-arrow:hover { color: var(--cream); background: rgba(0,0,0,0.5); }
.carousel-prev { left: 24px; }
.carousel-next { right: 24px; }
.carousel-dots {
  position: absolute; bottom: 28px; left: 50%;
  transform: translateX(-50%);
  display: flex; gap: 10px; z-index: 310;
}
.carousel-dot {
  width: 8px; height: 8px; border-radius: 50%;
  border: 1px solid var(--taupe); background: transparent;
  cursor: pointer; transition: all 0.3s;
}
.carousel-dot.active { background: var(--gold); border-color: var(--gold); }
@media (max-width: 768px) {
  .carousel-arrow { display: none; }
  .carousel-stage { width: 100vw; height: 75vh; }
  .carousel-back { top: 16px; left: 16px; font-size: 11px; padding: 8px 16px; }
}
```

**Expected deliverable:** Full carousel styling with responsive mobile behavior.

---

### Step 7: Wire Conditional Rendering in Main Component
**File:** `src/App.jsx` (~line 1070, at the start of the return statement)

**Add early return for gallery view:**
```jsx
if (isGalleryOpen) {
  return <GalleryCarousel images={highlightImages} onClose={() => {
    setGalleryOpen(false);
    setTimeout(() => {
      highlightsSectionRef.current?.scrollIntoView({ behavior: 'smooth' });
    }, 100);
  }} />;
}
```

**Expected deliverable:** Clicking "SEE FULL GALLERY" shows carousel, "BACK" returns to main page at Highlights.

---

## Key Files

| File | Operation | Description |
|------|-----------|-------------|
| `src/App.jsx:148-167` | Modify | Add new state variables (`isGalleryOpen`, refs) |
| `src/App.jsx:147` | Add | New `GalleryCarousel` component (before `GBeauty`) |
| `src/App.jsx:726-760` | Modify | Refactor `.services-list`, `.service-item`, `.service-img` CSS |
| `src/App.jsx:1273-1297` | Modify | Update Services section JSX (keep structure, CSS does the work) |
| `src/App.jsx:1301` | Modify | Add `ref={highlightsSectionRef}` to highlights section |
| `src/App.jsx:1314` | Add | "SEE FULL GALLERY" button after highlights grid |
| `src/App.jsx:~970` | Add | New carousel CSS block (~50 lines) |
| `src/App.jsx:~1070` | Add | Conditional return for gallery view |

## Risks and Mitigation

| Risk | Mitigation |
|------|------------|
| Base64 images in carousel cause slow rendering | Images are already loaded in memory; carousel only shows one at a time via opacity toggle (no DOM re-creation) |
| Scroll position lost when toggling views | `highlightsSectionRef` + `scrollIntoView` after state change restores position |
| Touch swipe conflicts with browser gestures | 75px threshold prevents accidental swipes; `onTouchMove` captures horizontal movement only |
| Service accent images overlap text on mid-size screens | `z-index: 0` + low opacity (0.15) + `pointer-events: none` ensures text stays readable and clickable |
| Large App.jsx file size (already 1552 lines) | Carousel component is self-contained (~60 lines); CSS additions are ~50 lines; total growth ~120 lines — acceptable for single-file architecture |

## SESSION_ID (for /multi-execute use)
- CODEX_SESSION: unavailable (API key expired — 401 Unauthorized)
- GEMINI_SESSION: completed (inline analysis, no persistent session)

---

## Design Authority: Gemini
- Services: single-column centered text, decorative margin images
- Carousel: dark charcoal fullscreen, cross-fade, gold dot indicators, SVG arrows
- Button: ghost style, gold border, uppercase sans-serif
- Mobile: hide service images, hide carousel arrows, swipe-only

## Engineering Authority: Claude
- Routing: conditional rendering (no react-router-dom needed)
- Carousel: custom React state + useEffect (no external library)
- Animation: reuse existing FadeIn + cubic-bezier patterns
- Touch: native onTouchStart/onTouchMove/onTouchEnd events
- Keyboard: ArrowLeft/ArrowRight/Escape support
