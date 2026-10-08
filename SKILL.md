---
name: ashi-liquid-glass
description: Use when the user wants liquid-glass or glassmorphism UI with real light refraction (frosted-glass components, glassy pages, edge highlights, chromatic dispersion), or wants a glass effect applied over an existing design.
---

# ashi-liquid-glass

## Overview

Build liquid-glass interfaces: frosted surfaces with real refraction (Snell's law),
two-sided rim light, and optional RGB dispersion on the edges. Two engine variants
ship inside this skill: **full** (best look) and **lite** (fastest). Follow the
workflow below step by step; use the embedded code verbatim.

**Language rule:** converse with the user in the user's own language. Code
identifiers stay English. Visible app text follows the user's target audience,
not the conversation language.

## When NOT to use

- The end users are mostly on non-Chromium browsers (Safari, Firefox) and the
  refraction effect is the core requirement — they will only get blur + rim
  light, so do not promise the full effect. Say this upfront.
- Plain frosted glass (simple blur with no refraction or rim light) is enough —
  plain CSS `backdrop-filter` does the job without this skill.

## Workflow (in order, one step per user message)

### Step 1 — Ask which version: full, lite, or both?

Ask before anything else. Explain the trade-off in one line each:

- **full** — three-channel RGB dispersion on the edges (subtle rainbow fringe
  like a real magnifier), Snell refraction, rim light. Best look, slightly heavier.
- **lite** — single-channel refraction, same rim light and blur, plus
  performance tricks (shared maps, async encoding, viewport-only filters).
  Fastest, edges look a touch flatter side-by-side.
- **both** — deliver two outputs, one per engine.

### Step 2 — Existing design, or design from scratch?

Ask: does the user already have a design (mockup, screenshot, Figma, HTML)?

- **Existing design → glass-only path.** Apply the glass effect onto their
  design. Do NOT touch layout, colors, typography, or spacing. Only add the
  `lg` class, the layer element, and the engine.
- **No design → go to Step 3.**

### Step 3 — Learn the need step by step (scratch path only)

One question per message. Do NOT write code until you understand all of:

1. App or website? What output format (single HTML file, framework
   components, something else)? The output follows the user's need —
   never impose a format.
2. Which components are needed? (see Component reference below)
3. Colors, fonts, background (photo or gradient — any background works).

### Step 4 — Build with the embedded code

Use the Shared CSS plus the chosen engine below. After adding `.lg`
elements dynamically, call `LiquidGlass.refresh()`. After a global
setting change, call `LiquidGlass.rebuild()`.

## Shared CSS — material, knobs, hard rule

```css
:root{
  --ink:#0b2a1c; --ink2:rgba(11,42,28,.68); --acc:#e8672a;
  --spring:cubic-bezier(.3,1.6,.5,1);
  /* --bgimg: your own background photo or gradient — any backdrop works */
}

/* =================== MATERIAL (do not rename) ===================
   .lg = any glass surface. The engine adds <i class="lg-layer"> inside it.
   Per-component knobs: --lg-tint (colour)  --lg-shadow
                        --lg-zoom (lens magnification)  --lg-bezel (rim width)
   RULE: never put opacity / filter / mask on a .lg or on its ancestors
         (it kills the refraction). */
.lg{position:relative;isolation:isolate;--lg-zoom:1.1} /* 10% zoom default; override per component */
.lg-layer{position:absolute;inset:0;z-index:-1;border-radius:inherit;pointer-events:none;
  background:var(--hl,none) center/100% 100% no-repeat,linear-gradient(var(--lg-tint,transparent),var(--lg-tint,transparent)),linear-gradient(rgba(255,255,255,var(--lg-white,0)),rgba(255,255,255,var(--lg-white,0))),linear-gradient(180deg,rgba(255,255,255,.2),rgba(255,255,255,.04));
  backdrop-filter:blur(1.5px) saturate(1.6) brightness(1.1);-webkit-backdrop-filter:blur(1.5px) saturate(1.6) brightness(1.1);
  box-shadow:var(--lg-shadow,0 14px 30px -10px rgba(4,50,28,.4),0 3px 10px rgba(4,50,28,.15))}
```

## Component CSS (copy what you need)

```css
/* === .lg-button  (variants: --accent  --lg  --icon) === */
.lg-button{height:48px;padding:0 24px;border-radius:24px;display:inline-flex;align-items:center;justify-content:center;gap:8px;font-weight:600;font-size:15px;--lg-zoom:1.2;transition:transform .5s var(--spring)}
.lg-button:hover{transform:scale(1.04)}.lg-button:active{transform:scale(.94)}
.lg-button--accent{color:#fff;--lg-tint:rgba(232,103,42,.6);text-shadow:0 1px 2px rgba(0,0,0,.25)}
.lg-button--lg{height:60px;padding:0 34px;font-size:17px;border-radius:30px}
.lg-button--icon{width:48px;padding:0}
/* === .lg-chip / .lg-badge / .lg-avatar === */
.lg-chip{height:38px;padding:0 16px;border-radius:19px;font-size:13px;font-weight:600;display:inline-flex;align-items:center;gap:6px;--lg-shadow:0 6px 14px rgba(4,50,28,.2)}
.lg-chip[aria-pressed=true]{--lg-tint:rgba(232,103,42,.55);color:#fff}
.lg-badge{font-size:11px;font-weight:800;padding:3px 11px;border-radius:12px;color:#fff;--lg-tint:rgba(232,103,42,.65);--lg-shadow:none}
.lg-avatar{width:44px;height:44px;border-radius:50%;display:grid;place-items:center;font-weight:800;margin-inline-start:-10px;--lg-shadow:0 4px 10px rgba(4,50,28,.25)}
/* === .lg-navbar === */
.lg-navbar{position:sticky;top:12px;z-index:50;margin-top:14px;height:68px;border-radius:34px;display:flex;align-items:center;gap:12px;padding:0 12px 0 16px}
.lg-brand{display:flex;align-items:center;gap:8px;font-weight:800;font-size:20px}
.lg-logo{width:32px;height:32px;border-radius:50%;background:radial-gradient(circle at 35% 30%,#fff,#5fd39a 60%,#1f8f5a)}
.lg-nav-links{display:flex;gap:2px;margin-inline:auto 12px;font-weight:600;font-size:14px}
.lg-nav-links a{padding:9px 14px;border-radius:20px}.lg-nav-links a:hover{background:rgba(255,255,255,.35)}
/* === .lg-toolbar  (.lg-tool buttons, .lg-divider) === */
.lg-toolbar{display:inline-flex;align-items:center;gap:2px;padding:6px;border-radius:26px;max-width:100%;flex-wrap:wrap}
.lg-tool{width:42px;height:42px;border-radius:50%;display:grid;place-items:center}
.lg-tool:hover{background:rgba(255,255,255,.35)}
.lg-tool[aria-pressed=true]{background:rgba(255,255,255,.6);box-shadow:inset 0 1px 3px rgba(4,50,28,.25)}
.lg-divider{width:1px;height:24px;background:rgba(11,42,28,.22);margin:0 6px}
/* === .lg-input === */
.lg-input{display:flex;align-items:center;gap:10px;height:52px;padding:0 20px;border-radius:26px}
.lg-input input{flex:1;min-width:0;height:100%}.lg-input input::placeholder{color:var(--ink2)}
.lg-input:focus-within{outline:2px solid rgba(255,255,255,.9);outline-offset:3px}
/* === .lg-select  (.lg-select-btn, .lg-menu) === */
.lg-select{border-radius:26px;z-index:5}
.lg-select-btn{width:100%;height:52px;padding:0 20px;display:flex;align-items:center;justify-content:space-between;font-weight:600}
.lg-menu{display:none;position:absolute;top:calc(100% + 8px);right:0;left:0;border-radius:24px;padding:8px;gap:2px}
.lg-select.open .lg-menu{display:grid}
.lg-menu button{padding:12px 16px;border-radius:16px;text-align:right;font-weight:600;font-size:14px}.lg-menu button:hover{background:rgba(255,255,255,.4)}
/* === .lg-switch === */
.lg-switch{position:relative;width:64px;height:36px;border-radius:18px;direction:ltr;flex:none;background:rgba(255,255,255,.35);box-shadow:inset 0 1px 3px rgba(4,50,28,.3);transition:background .3s}
.lg-switch[aria-checked=true]{background:var(--acc)}
.lg-knob{position:absolute;top:-1px;left:-2px;width:46px;height:38px;border-radius:19px;--lg-zoom:1.15;--lg-shadow:0 6px 14px rgba(4,50,28,.3);transition:left .45s var(--spring)}
.lg-switch[aria-checked=true] .lg-knob{left:20px}
/* === .lg-check / .lg-radio === */
.lg-check{display:flex;align-items:center;gap:10px;cursor:pointer;font-weight:600;font-size:14px}
.lg-check input{position:absolute;opacity:0;pointer-events:none}
.lg-box{width:28px;height:28px;border-radius:9px;flex:none;--lg-zoom:1.1;--lg-bezel:9;--lg-shadow:0 4px 10px rgba(4,50,28,.25)}
.lg-radio .lg-box{border-radius:50%}
.lg-check input:checked+.lg-box::after{content:'';position:absolute;left:10px;top:4px;width:8px;height:14px;border:solid var(--acc);border-width:0 3px 3px 0;transform:rotate(45deg)}
.lg-radio input:checked+.lg-box::after{left:8px;top:8px;width:12px;height:12px;border:0;border-radius:50%;background:var(--acc);transform:none}
.lg-check input:focus-visible+.lg-box{outline:2px solid #fff;outline-offset:3px}
/* === .lg-slider  (.lg-track .lg-fill .lg-thumb) === */
.lg-slider{position:relative;height:34px;direction:ltr;touch-action:none;cursor:pointer;outline:0}
.lg-track,.lg-fill{position:absolute;top:11px;left:0;height:12px;border-radius:6px}
.lg-track{right:0;background:rgba(255,255,255,.35);box-shadow:inset 0 1px 3px rgba(4,50,28,.3)}
.lg-fill{background:var(--acc)}
.lg-thumb{position:absolute;top:0;width:46px;height:34px;border-radius:17px;--lg-zoom:1.15;--lg-shadow:0 6px 14px rgba(4,50,28,.3)}
/* === .lg-progress === */
.lg-progress{height:14px;border-radius:7px;direction:ltr;overflow:hidden;background:rgba(255,255,255,.35);box-shadow:inset 0 1px 3px rgba(4,50,28,.3)}
.lg-progress>i{display:block;height:100%;width:var(--v,50%);background:var(--acc);border-radius:7px}
/* === .lg-tabs  (also .lg-pagination, .lg-tabbar) === */
.lg-tabs{display:inline-flex;direction:ltr;padding:5px;border-radius:28px;gap:2px;max-width:100%}
.lg-tabs button{position:relative;z-index:2;height:44px;padding:0 22px;border-radius:22px;font-weight:600;font-size:14px;white-space:nowrap}
.lg-tab-thumb{position:absolute;z-index:1;top:5px;left:5px;height:44px;border-radius:22px;--lg-zoom:1.1;--lg-shadow:0 6px 14px rgba(4,50,28,.25);transition:left .5s var(--spring),width .3s}
.lg-pagination button{width:44px;padding:0}
.lg-tabbar{position:fixed;bottom:calc(18px + env(safe-area-inset-bottom,0px));left:50%;transform:translateX(-50%);z-index:60;border-radius:35px;padding:6px}
.lg-tabbar button{width:70px;height:58px;padding:0;display:grid;place-items:center;border-radius:29px}.lg-tabbar .lg-tab-thumb{top:6px;left:6px;height:58px;border-radius:29px}
/* === .lg-breadcrumb === */
.lg-breadcrumb{display:inline-flex;gap:10px;align-items:center;padding:10px 20px;border-radius:22px;font-size:14px;font-weight:600}.lg-breadcrumb span{color:var(--ink2)}
/* === .lg-sidebar  (.lg-side-item) === */
.lg-sidebar{padding:12px;border-radius:30px;display:grid;gap:2px}
.lg-side-item{display:flex;gap:12px;align-items:center;padding:12px 14px;border-radius:18px;font-weight:600;font-size:14px;text-align:right}
.lg-side-item:hover{background:rgba(255,255,255,.3)}.lg-side-item[aria-current]{background:rgba(255,255,255,.55);box-shadow:inset 0 1px 3px rgba(4,50,28,.2)}
/* === .lg-card  (--media --stat --price) === */
.lg-card{border-radius:32px;padding:22px;--lg-tint:rgba(255,255,255,.14)}
.lg-card h3{font-size:18px;font-weight:800;margin-bottom:6px}
.lg-card--media{padding:10px}.lg-card--media .pic{height:170px;border-radius:24px;background-size:cover;box-shadow:inset 0 0 0 1.5px rgba(255,255,255,.7)}.lg-card--media .body{padding:14px 12px 8px}
.lg-card--stat b{font-size:40px;font-weight:800;display:block}
.lg-card--price .n{font-size:38px;font-weight:800;margin:10px 0}.lg-card--price ul{list-style:none;padding:0;margin:0 0 18px;display:grid;gap:8px;font-size:14px}
.lg-card--price li::before{content:'✓ ';color:var(--acc);font-weight:800}
/* === .lg-table === */
.lg-table{width:100%;border-collapse:collapse;font-size:14px}.lg-table th{text-align:right;color:var(--ink2);font-weight:600;padding:8px 6px}.lg-table td{padding:12px 6px;border-top:1px solid rgba(11,42,28,.14)}
/* === .lg-alert === */
.lg-alert{display:flex;gap:12px;align-items:center;padding:14px 18px;border-radius:24px;font-size:14px;font-weight:600}.lg-alert .lg-button{margin-inline-start:auto}
/* === .lg-tooltip === */
.lg-tipwrap{position:relative;display:inline-block}
.lg-tooltip{position:absolute;bottom:calc(100% + 10px);left:50%;transform:translateX(-50%) scale(.8);visibility:hidden;white-space:nowrap;padding:8px 14px;border-radius:14px;font-size:12px;font-weight:600;transition:transform .3s var(--spring);z-index:20}
.lg-tipwrap:hover .lg-tooltip{visibility:visible;transform:translateX(-50%) scale(1)}
/* === .lg-accordion  (.lg-acc-item .lg-acc-head .lg-acc-body) === */
.lg-acc-item{border-radius:26px;margin-bottom:12px}
.lg-acc-head{width:100%;display:flex;justify-content:space-between;align-items:center;padding:18px 22px;font-weight:600;text-align:right}
.lg-acc-body{display:none;padding:0 22px 18px;font-size:14px;line-height:2;color:var(--ink2)}
.lg-acc-item.open .lg-acc-body{display:block}
/* === .lg-modal  (.lg-overlay) === */
.lg-overlay{display:none;position:fixed;inset:0;z-index:100;background:rgba(5,30,18,.4);place-items:center;padding:18px}.lg-overlay.show{display:grid}
.lg-modal{width:100%;max-width:440px;border-radius:36px;padding:30px;display:grid;gap:14px}
/* === .lg-toast === */
.lg-toast{position:fixed;right:50%;top:20px;z-index:120;transform:translate(50%,-140px);transition:transform .5s var(--spring);border-radius:26px;padding:14px 26px;font-weight:600;font-size:14px}
.lg-toast.show{transform:translate(50%,0)}
/* === .lg-fab === */
.lg-fab{position:fixed;left:18px;bottom:calc(24px + env(safe-area-inset-bottom,0px));z-index:60;width:62px;height:62px;border-radius:50%;display:grid;place-items:center;--lg-tint:rgba(232,103,42,.6);color:#fff;--lg-zoom:1.15;transition:transform .5s var(--spring)}.lg-fab:hover{transform:scale(1.08)}
/* === .lg-footer === */
.lg-footer{margin-top:56px;border-radius:36px;padding:30px;display:grid;grid-template-columns:2fr repeat(3,1fr);gap:20px;font-size:14px}
.lg-footer h4{margin:0 0 8px}.lg-footer a{display:block;padding:4px 0;color:var(--ink2)}
@media (max-width:860px){.lg-nav-links{display:none}.lg-footer{grid-template-columns:1fr 1fr}}
@media (prefers-reduced-motion:reduce){*{transition:none!important}}
```

## Engine FULL — Snell refraction + 3-channel dispersion + rim light

Use for the **full** version. Auto-applies to every `.lg` element.

```js
(() => {
  const IOR = 1.5, BEZEL = 30, THICK = 40, DISP = 0.10, BLUR = 1.5;
  const BIG = 60000;
  const chromium = /Chrome\//.test(navigator.userAgent);
  const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
  svg.style.cssText = 'position:absolute;width:0;height:0'; document.body.prepend(svg);
  const PLAIN = `blur(${BLUR}px) saturate(1.8) brightness(1.12)`;

  const shiftFor = bz => {
    const th = THICK * bz / BEZEL;
    const f = t => { const u = 1 - t, q = 1 - u ** 4, a1 = Math.atan(th / bz * q ** -.75 * u ** 3), a2 = Math.asin(Math.sin(a1) / IOR); return (th * .3 + th * q ** .25) * Math.tan(a1 - a2); };
    let max = 0; for (let i = 1; i < 200; i++) max = Math.max(max, f(i / 200));
    return { f, max };
  };

  const toCanvas = d => { const c = document.createElement('canvas'); c.width = d.width; c.height = d.height; c.getContext('2d').putImageData(d, 0, 0); return c; };
  const toURL = c => new Promise((ok, no) => c.toBlob(b => {
    if (!b) return no(new Error('toBlob failed'));
    const fr = new FileReader(); fr.onload = () => ok(fr.result); fr.onerror = no; fr.readAsDataURL(b);
  }));

  function scan(w, h, r, k, fn) {
    const W = w * k, H = h * k, hw = w / 2 - r, hh = h / 2 - r;
    for (let j = 0; j < H; j++) for (let i = 0; i < W; i++) {
      const px = (i + .5) / k - w / 2, py = (j + .5) / k - h / 2;
      const qx = Math.abs(px) - hw, qy = Math.abs(py) - hh, sx = px < 0 ? -1 : 1, sy = py < 0 ? -1 : 1;
      let d, nx, ny;
      if (qx > 0 && qy > 0) { const l = Math.hypot(qx, qy); d = l - r; nx = sx * qx / l; ny = sy * qy / l; }
      else if (qx > qy) { d = qx - r; nx = sx; ny = 0; } else { d = qy - r; nx = 0; ny = sy; }
      fn((j * W + i) * 4, -d, nx, ny, px, py);
    }
  }

  const entries = new Map(), pending = new Map(); let uid = 0;
  function build(key, sp) {
    if (pending.has(key)) return pending.get(key);
    const p = (async () => {
      const { w, h, r, zoom, bz } = sp, big = w * h > BIG, rk = big ? 1 : 2;
      const rim = new ImageData(w * rk, h * rk), rd = rim.data;
      scan(w, h, r, rk, (o, depth, nx, ny) => {
        if (depth >= 40) return;
        const dot = Math.abs(nx * -.55 + ny * -.83);
        const a = (Math.exp(-depth) * (.3 + .85 * dot ** 1.4) + Math.exp(-depth / 7) * dot * .28) * Math.min(1, Math.max(0, depth * 2 + .5));
        rd[o] = rd[o + 1] = rd[o + 2] = 255; rd[o + 3] = a * 255;
      });
      let map = null, M = 1;
      if (chromium) {
        const S = shiftFor(bz), Z = 1 - 1 / zoom; M = S.max + Math.max(w, h) / 2 * Z;
        map = new ImageData(w, h); const md = map.data;
        scan(w, h, r, 1, (o, depth, nx, ny, px, py) => {
          const m = depth > 0 && depth < bz ? S.f(depth / bz) : 0;
          md[o] = 128 + 127 * (-m * nx - px * Z) / M; md[o + 1] = 128 + 127 * (-m * ny - py * Z) / M; md[o + 2] = 128; md[o + 3] = 255;
        });
      }
      const [rimU, mapU] = await Promise.all([toURL(toCanvas(rim)), map ? toURL(toCanvas(map)) : null]);
      let id = null, el = null;
      if (chromium) {
        id = 'lg' + ++uid; const s = M * 2;
        const ch = (sc, mt, n) => `<feDisplacementMap in="SourceGraphic" in2="m" scale="${sc}" xChannelSelector="R" yChannelSelector="G"/><feColorMatrix type="matrix" values="${mt}" result="${n}"/>`;
        svg.insertAdjacentHTML('beforeend', `<filter id="${id}" x="0" y="0" width="100%" height="100%" color-interpolation-filters="sRGB"><feImage href="${mapU}" x="0" y="0" width="${w}" height="${h}" preserveAspectRatio="none" result="m"/>${ch(s * (1 + DISP), '1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0', 'r')}${ch(s, '0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 1 0', 'g')}${ch(s * (1 - DISP), '0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 1 0', 'b')}<feBlend in="r" in2="g" mode="screen" result="rg"/><feBlend in="rg" in2="b" mode="screen"/></filter>`);
        el = svg.lastElementChild;
      }
      const res = { id, rim: rimU, el }; entries.set(key, res); return res;
    })();
    pending.set(key, p);
    const done = () => pending.delete(key); p.then(done, done);
    return p;
  }

  function apply(host) {
    const L = host._lg, res = host._res; if (!L || !res) return;
    const v = host._vis && res.id ? `url(#${res.id}) ${PLAIN}` : PLAIN;
    if (host._cur === v) return; host._cur = v;
    L.style.backdropFilter = L.style.webkitBackdropFilter = v;
  }

  async function init(host) {
    const w = host.offsetWidth, h = host.offsetHeight; if (!w || !h) return;
    const cs = getComputedStyle(host), g = n => parseFloat(cs.getPropertyValue(n));
    const rs = getComputedStyle(document.documentElement), za = parseFloat(rs.getPropertyValue('--lg-zoom-all')), ba = parseFloat(rs.getPropertyValue('--lg-bezel-all'));
    const r = Math.min(parseFloat(cs.borderTopLeftRadius) || 0, w / 2, h / 2);
    const zoom = za || g('--lg-zoom') || 1, bz = Math.min(ba || g('--lg-bezel') || BEZEL, Math.min(w, h) / 2);
    const key = [w, h, r.toFixed(1), zoom.toFixed(3), bz.toFixed(1)].join('|');
    host._w = w; host._h = h;
    const tok = host._tok = (host._tok || 0) + 1;
    let res = entries.get(key);
    if (!res) { try { res = await build(key, { w, h, r, zoom, bz }); } catch (e) { return; } if (tok !== host._tok) return; }
    host._res = res;
    host._lg.style.setProperty('--hl', `url(${res.rim})`);
    apply(host); gc();
  }

  let gct;
  function gc() {
    clearTimeout(gct);
    gct = setTimeout(() => {
      const used = new Set(); document.querySelectorAll('.lg[data-lg]').forEach(h => used.add(h._res));
      entries.forEach((res, key) => { if (!used.has(res)) { if (res.el) res.el.remove(); entries.delete(key); } });
    }, 1500);
  }

  const ro = new ResizeObserver(es => es.forEach(e => {
    const h = e.target; cancelAnimationFrame(h._raf);
    h._raf = requestAnimationFrame(() => {
      if (h._w && Math.abs(h.offsetWidth - h._w) <= 1 && Math.abs(h.offsetHeight - h._h) <= 1) return;
      init(h);
    });
  }));
  const io = new IntersectionObserver(es => es.forEach(e => { e.target._vis = e.isIntersecting; apply(e.target); }), { rootMargin: '300px 0px' });

  const refresh = (root = document) => root.querySelectorAll('.lg:not([data-lg])').forEach(h => {
    h.dataset.lg = 1; const L = document.createElement('i'); L.className = 'lg-layer'; h._lg = L; h.prepend(L); ro.observe(h); io.observe(h);
  });
  let rid = 0;
  const rebuild = () => { const id = ++rid, q = [...document.querySelectorAll('.lg[data-lg]')]; (function step() { if (id !== rid) return; q.splice(0, 6).forEach(h => init(h)); if (q.length) requestAnimationFrame(step); })(); };
  refresh(); window.LiquidGlass = { refresh, rebuild };
})();
```

## Engine LITE — same look, single channel, fastest

Use for the **lite** version. Identical to FULL except the SVG filter:
one `feDisplacementMap` instead of three channels + `feBlend`.
Everything else (shared maps, async `toBlob`, viewport-only refraction,
1px resize guard, token guard, cache GC) is the same — copy the FULL
engine above and replace ONLY the `if (chromium) { ... }` block inside
`build()` with this:

```js
      let id = null, el = null;
      if (chromium) {
        id = 'lg' + ++uid; const s = M * 2;
        svg.insertAdjacentHTML('beforeend', `<filter id="${id}" x="0" y="0" width="100%" height="100%" color-interpolation-filters="sRGB"><feImage href="${mapU}" x="0" y="0" width="${w}" height="${h}" preserveAspectRatio="none" result="m"/><feDisplacementMap in="SourceGraphic" in2="m" scale="${s}" xChannelSelector="R" yChannelSelector="G"/></filter>`);
        el = svg.lastElementChild;
      }
```

Also remove the unused `DISP` constant from the first line
(`const IOR = 1.5, BEZEL = 30, THICK = 40, BLUR = 1.5;`).

## Interaction patterns (wire with tiny JS)

- **Toggle (switch, chip, toolbar):** flip `aria-pressed` / `aria-checked`
  on click; CSS reacts. State lives in the attribute, not in JS variables.
- **Toast:** any element with `data-toast="message"` shows it in a
  `.lg-toast` box for ~2.4s.
- **Modal:** `data-open="#modalId"` adds `.show` to the overlay;
  `data-close` (or clicking the backdrop itself) removes it.
- **Slider:** `.lg-slider[data-value]` — pointer + keyboard driven, fires a
  custom `lgchange` event with the value; listeners (e.g. a glass-settings
  panel) set `--lg-zoom-all` / `--lg-bezel-all` / `--lg-white` on `:root`
  and call `LiquidGlass.rebuild()`.
- **Tabs:** a `.lg-tab-thumb` element slides (`left`/`width`) to the active
  button; recompute on resize and after fonts load.

## Component reference

| Class | Use for |
|---|---|
| `.lg-button` | Buttons (`--accent` primary, `--lg` large, `--icon` square) |
| `.lg-chip` | Toggleable filter/tag chips |
| `.lg-badge` | Small highlight labels |
| `.lg-avatar` | Round avatars (overlap with negative margin) |
| `.lg-navbar` | Sticky top navigation bar |
| `.lg-toolbar` / `.lg-tool` | Wrapping icon toolbar with dividers |
| `.lg-input` | Text/email inputs with icon |
| `.lg-select` | Dropdown (button + menu) |
| `.lg-switch` | On/off switch with sliding glass knob |
| `.lg-check` / `.lg-radio` | Checkbox / radio with glass box |
| `.lg-slider` | Drag slider (volume, brightness…) |
| `.lg-progress` | Static progress bar (`--v:64%`) |
| `.lg-tabs` | Sliding-thumb tabs (also pagination, bottom tabbar) |
| `.lg-breadcrumb` | Home / section / page trail |
| `.lg-sidebar` | Vertical menu with active item |
| `.lg-card` | Generic card (`--media` image, `--stat` number, `--price` plan) |
| `.lg-table` | Data tables with glass badges |
| `.lg-alert` | Info bar with action button |
| `.lg-tooltip` | Hover hint bubble |
| `.lg-accordion` | Expandable Q&A items |
| `.lg-modal` | Dialog inside `.lg-overlay` backdrop |
| `.lg-toast` | Temporary message popup |
| `.lg-fab` | Floating action button |
| `.lg-footer` | Multi-column footer |

## Rules and limits

1. **Chromium-only refraction.** Only Chromium applies the SVG displacement
   filter; other browsers keep blur + rim light. Say this upfront —
   never promise the full effect everywhere.
2. **Glass-only path means glass only.** On an existing design, do not
   change layout, colors, fonts, or spacing.
3. **No code before understanding.** On the scratch path, do not write code
   until Step 3 questions are answered.
4. **Output follows the user.** HTML file, framework components, or
   whatever the user needs — the skill never imposes a format.
5. **Never `opacity` / `filter` / `mask` on `.lg` or its ancestors.**
6. **Reduced motion:** honor `prefers-reduced-motion` (disable transitions).
