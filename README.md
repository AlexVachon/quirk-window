# window

Open windows and draw in them from Quirk: shapes, text, images,
keyboard and mouse events, and a frame loop.

```quirk
use window

w := window.open("Hello", 640, 480)
x := 0
while w.is_open() {
    for e in w.events() {
        if e.kind == "key_down" and e.key == "escape" { w.close() }
    }
    if not w.is_open() { break }
    w.clear(window.rgb(30, 30, 40))
    w.rect(x, 200, 80, 80, window.rgb(80, 160, 255))
    w.text("Hello from Quirk", 20, 20, window.white(), scale=2)
    w.present()
    x = (x + 4) % 640
    window.sleep(16)
}
```

Or let `window.run` drive the loop:

```quirk
use window

define frame(w: window.Window, events: List) -> void {
    w.clear(window.black())
    pos := w.mouse()
    w.circle(pos.0, pos.1, 20, window.yellow())
}

window.run(window.open("Follow the mouse", 800, 600), frame)
```

## API

| | |
|---|---|
| `window.open(title, w, h, resizable=, hidden=, vsync=, headless=)` | A new `Window` (throws `WindowError` if it can't) |
| `w.events()` / `w.wait_event(timeout_ms)` | `Event`s: `kind` = `close`, `key_down` / `key_up` (`key`, `shift`, `ctrl`...), `text`, `mouse_move` / `mouse_down` / `mouse_up` (`x`, `y`, `button`), `wheel`, `resize`, `focus`, `blur` |
| `w.clear`, `rect`, `line`, `point`, `circle`, `text`, `image` | Drawing; `present()` shows the frame. `image(img, x, y, width=, height=, src_x=, src_y=, src_w=, src_h=, alpha=)` scales, crops (sprite sheets) and fades |
| `w.load_image(path)` | An `Image` from PNG (with transparency), JPEG, BMP, GIF (first frame) or TGA |
| `w.width()`, `height()`, `mouse()`, `key_down(name)`, `set_title`, `close`, `is_open` | Window state |
| `w.clip(x, y, width, height)`, `w.unclip()` | Only draw inside a rectangle (scrolling areas, panels) |
| `window.clipboard()`, `window.set_clipboard(text)` | Copy and paste through the system clipboard |
| `window.load_font(path, size)` → `Font` | A TrueType / OpenType font; `w.text(..., font=f)`, `f.width(text)`, `f.ascent`, `f.descent`, `f.line_height`. UTF-8 text, any character the font has |
| `w.screenshot(path)`, `w.pixel(x, y)` | Read back what was drawn (BMP / 0xRRGGBB) |
| `window.run(w, frame, fps=60)` | Frame loop |
| `window.rgb`, `rgba`, `hex`, `channels`, `white()`, `black()`, `red()`... | Colours (Ints, 0xAARRGGBB) |
| `window.text_width(text, scale)`, `ticks()`, `sleep(ms)`, `available()` | Helpers |

Text uses the compiler's built-in 8x16 font (ASCII + Latin-1), or a
TrueType font you load (`window.load_font`) — no font file ships with it. Several
windows can be open at once; open and draw from the main program, not
from an `async` task. `headless=true` draws off-screen with no display
(tests, CI).

## Requirements

- The Quirk compiler 7.33.0+ (its runtime has the `Window_*` functions and the image decoder).
- SDL2, loaded when the first window opens:
  `sudo apt install libsdl2-2.0-0` (Debian / Ubuntu),
  `brew install sdl2` (macOS). `window.available()` checks.

Bundled with the compiler at `<QUIRK_HOME>/packages/window/`, so
`use window` works out of the box; this repo is its canonical home:

```bash
quirk install window            # latest tag from this repo
quirk install window@1.1.0      # pinned
```

## License

MIT (see [`LICENSE`](LICENSE)).
