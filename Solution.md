# Assignment 6.3 — Solution: Overflow & Layout Fixing

## Project Structure
```
overflow-practice/
├── index.html          (the 4 overflow values + scrollable panel)
├── style.css
├── broken.html         (BEFORE: page with overflow problems)
├── broken.css
├── fixed.html          (AFTER: same page, fixed)
└── fixed.css
```
All code is in the attached files; the key rules are explained below.

## Part 1 — The Four Overflow Values (`index.html` + `style.css`)
Every box has `height: 120px` and the same long text, which is taller than the box (about 300px of content). Only `overflow` changes:

```css
.box     { height: 120px; }      /* fixed height -> content overflows */
.visible { overflow: visible; }
.hidden  { overflow: hidden;  }
.scroll  { overflow: scroll;  }
.auto    { overflow: auto;    }
```

| Value | Result in the example |
|-------|-----------------------|
| `visible` (default) | Text spills **outside** the box and runs over the area below (that is why there is extra space under the grid). |
| `hidden` | The extra text is **clipped**; the user cannot reach it. |
| `scroll` | Scrollbars are **always** shown (both bars appear, even when one is not needed). |
| `auto` | Scrollbar appears **only when needed** — here, the vertical one. Usually the best choice. |

I measured this in a browser: each box has a content height of about 300px inside a 116px visible area, so all four overflow.

## Part 2 — Second Container with `overflow: auto`
`.scroll-panel` is a fixed-size container (`max-width: 420px; height: 180px`) holding a 10-item list, with `overflow: auto`. Its heading stays visible while scrolling thanks to `position: sticky; top: 0`. It is also keyboard-accessible with `tabindex="0"` (the user can focus it and scroll with the arrow keys).

## Part 3 — Identify and Fix an Overflow Issue in a Sample Page
`broken.html` is a "Student Dashboard" page that looks fine on a desktop but overflows on a phone. At **375px** wide the page is **832px** wide (`document.documentElement.scrollWidth`), so there is a horizontal scrollbar. After the fixes (`fixed.html`) the page is exactly **375px** — no horizontal scroll.

| # | Problem (`broken.css`) | How to find it in DevTools | Fix (`fixed.css`) |
|---|------------------------|----------------------------|-------------------|
| 1 | `.container { width: 600px; }` — fixed width wider than the phone | Device toolbar (`Ctrl + Shift + M`) at 375px; the page scrolls sideways | `width: 100%; max-width: 600px; margin: 0 auto;` |
| 2 | `.card { width: 100%; padding: 20px; border: 4px }` with `content-box`: the real width is 100% + 48px | **Computed** tab box model: width is larger than the parent | `*, *::before, *::after { box-sizing: border-box; }` |
| 3 | `.hero { width: 800px; }` — fixed-size box wider than the screen | Hover it in **Elements**; it extends past the viewport | `width: 100%; max-width: 800px;` |
| 4 | Wide table with 8 columns and `white-space: nowrap` | The table is the widest element on the page | Wrap it: `<div class="table-wrapper">` with `overflow-x: auto;` — only the table scrolls sideways |
| 5 | A long unbroken URL inside `.info { width: 260px; }` spills out of its box | Text extends past the yellow box | `max-width: 100%; overflow-wrap: anywhere;` |

**Tip for finding overflow:** in the Console run
```js
[...document.querySelectorAll('*')].filter(e => e.scrollWidth > document.documentElement.clientWidth)
```
to list the elements that are wider than the screen.

## Submission
- ✅ An example of each overflow value: `index.html` / `style.css` (attached)
- 📸 Screenshot of the fixed layout: open `fixed.html` at phone width (device toolbar, 375px) — attached `fixed-375px.png` shows how it should look; `broken-375px.png` shows the "before"
- Note: the screenshots I attached are renderings of the same pages; take your own for the submission.
