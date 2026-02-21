# Gena Beauty — Makeup Artist Portfolio Website

A luxury makeup artist portfolio and booking website for **Gena Botrous**, a New York City-based makeup artist available for travel worldwide.

---

## About the Project

This is a single-page React website that showcases Gena's work, services, and client testimonials, and provides a comprehensive inquiry/booking form for prospective clients.

**Live site:** [github.com/genab6/Gena-Websitev1](https://github.com/genab6/Gena-Websitev1)

---

## Tech Stack

| Technology | Purpose |
|---|---|
| React 18 | UI framework |
| EmailJS | Contact form email delivery |
| Behold | Instagram feed widget |
| Create React App | Build tooling |

---

## Site Sections

| Section | Description |
|---|---|
| **Home** | Animated logo intro + hero slideshow |
| **About** | Artist bio and background |
| **Services** | Bridal, Soft Glam, Trials, Editorial, Lessons |
| **Portfolio** | Gallery grid with lightbox viewer + Instagram feed |
| **Contact** | Full inquiry/booking form |

---

## Key Features

- **Intro animation** — Logo fades in on a white splash screen, then transitions to the top-left nav position
- **Hero slideshow** — Auto-rotating full-screen images, navigable via dot indicators
- **Gallery grid** — 6-tile photo grid with click-to-expand lightbox
- **Inquiry form** — Collects client details, service type, event date/location, and inspiration links; sent via EmailJS
- **Instagram feed** — Live @gena.mua feed powered by Behold widget
- **Press bar** — Featured publication names (VOGUE, MAX, GETTYIMAGES, FASHION MAGAZINE, NYFW)
- **Testimonials** — 3 client quotes
- **Responsive design** — Mobile-friendly with collapsible nav menu
- **Scroll-to-top on refresh** — Page always loads at the top

---

## Project Structure

```
Gena-Websitev1/
├── public/
│   ├── index.html
│   └── softglam.jpg       # Portfolio photos go here
├── src/
│   ├── App.jsx            # Entire application (components + styles)
│   └── index.js
├── package.json
└── README.md
```

---

## Getting Started

### Install dependencies
```bash
npm install
```

### Run locally
```bash
npm start
```
Opens at `http://localhost:3000`

### Build for production
```bash
npm run build
```

---

## Adding Photos to the Gallery

1. Save the image to the `public/` folder (e.g. `public/myphoto.jpg`)
2. Open `src/App.jsx` and find line 37:
   ```js
   const galleryImages = [...]
   ```
3. Replace a placeholder (e.g. `P.blush`) with `"url('/myphoto.jpg')"`
4. Commit and push

---

## Contact & Socials

- **Email:** genabeauty.mua@gmail.com
- **Instagram:** [@gena.mua](https://instagram.com/gena.mua)
- **Location:** New York City — available for travel
