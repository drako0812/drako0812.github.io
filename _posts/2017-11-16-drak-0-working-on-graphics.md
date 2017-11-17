---
layout: post
title: "DRAK-0 - Working On Graphics"
date: 2017-11-16
---

So, I wanted to talk a little about how I plan to implement the graphics
system in [DRAK-0](https://github.com/drako0812/DRAK-0).

## Class `drak::System`

`System` doesn't do much with graphics besides holding the memory for the
console and dispatching script commands to `drak::Screen`.

## Class `drak::Screen`

`Screen` handles the `drak::Palette` and screen buffer for the system.

Abridged class declaration:

```cpp
#include <gsl.h>
#include <SFML/Graphics.hpp>
#include "color.hpp"
#include "bit_array.hpp"
namespace drak {
    class Screen {
    private:
        gsl::span<unsigned char> _screen_buffer_data;
        BitArray<ScreenBytes> _screen_buffer_bits;
        gsl::span<unsigned char> _palette_data;
        Palette _palette;

        sf::Sprite _spr;
        sf::Image _img;
        sf::Texture _tex;
    public:
        Screen(gsl::span<unsigned char> & screen_buffer_data, gsl::span<unsigned char> & palette_data);
        int GetPixel(int x, int y) const;
        void SetPixel(int x, int y, int color);
        void SetPalette(int idx, RealColor color);
        RealColor GetPalette(int idx);
        void Draw();
        void DrawOnScreen(sf::RenderTarget & target);
    };
}
```

Most of the methods are self-explanatory, but:

- `Screen::SetPalette` allows setting a palette entry using a `RealColor` which is just a `struct` of 3 `unsigned char`s.
- `Screen::GetPalette` returns a palette entry.
- `Screen::Draw` converts and writes pixel data from `_screen_buffer_data` to the `_img`
- `Screen::DrawOnScreen`
  1. Loads `_img` data into `_tex`
  2. Draws `_spr` (whose texture is `_tex`) to `target`
