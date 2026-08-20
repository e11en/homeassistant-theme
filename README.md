# Aether

A single, flat-background glass theme for Home Assistant, with automatic dark
mode support.

Fork of [Nezz/homeassistant-visionos-theme](https://github.com/Nezz/homeassistant-visionos-theme),
which is in turn based on [Bas Nijholt's iOS Themes](https://github.com/basnijholt/lovelace-ios-themes).

## What's different from upstream

### One theme instead of two

Upstream ships `visionos` and `Liquid Glass`. They are ~95% identical; the
differences that mattered:

| | visionos | Liquid Glass | Aether |
|---|---|---|---|
| dark background | `rgb(25, 24, 22)` neutral | `rgb(18, 11, 25)` purple-tinted | **visionos** |
| light background | `rgb(84, 80, 76)` | `rgb(76, 80, 84)` | **visionos** |
| `ha-card-border-radius` | 20px | 34px | **Liquid Glass** |
| `ha-card-backdrop-filter` | `blur(20px)` | `blur(8px)` | **Liquid Glass** |
| `ha-card-box-shadow` | hairline inset edges | 3px inset edges + inner glow | **Liquid Glass** |
| card-mod | `card-mod-card` | `card-mod-card-yaml`, same rules **plus** square slider track bars | **Liquid Glass** |
| card-mod sidebar | base | base **plus** rounded list items | **Liquid Glass** |
| `ha-card-features-border-radius` | – | follows card radius | **Liquid Glass** |

Liquid Glass is a strict superset in everything except colour, so it is the
base. Its purple-tinted dark mode was the one thing worth swapping out — the
neutral near-black from visionOS sits better next to a dashboard that is
already almost black.

The obvious dials, if you want to retune it: `ha-card-border-radius` (34px is
generous on small tiles), `ha-card-backdrop-filter`, and the mode colours at
the top of the file.

### No background image

Upstream ships a wallpaper per mode (`day.jpg` / `night.jpg`,
`macOS_26_*.webp`) and points `lovelace-background` at it. Aether drops the
image *and* the `lovelace-background` variable, so a view falls back to the
flat `primary-background-color` of its mode.

Removing only the image is not enough: Home Assistant renders the view
background as

```css
background: var(--view-background, var(--lovelace-background, var(--primary-background-color)));
```

A `lovelace-background` that is defined but resolves to a missing variable is
invalid at computed-value time, so the whole declaration is dropped instead of
falling through to the last fallback. The variable has to go entirely.

Note that the glass effect leans on having something to blur. With a flat
background the depth comes from the card edges and the inner glow in
`ha-card-box-shadow`, not from the blur.

Set a wallpaper per view if you want one back — *edit view → Background
settings*, or in YAML:

```yaml
views:
  - title: Home
    path: home
    background:
      image: /local/wallpaper.jpg
      size: cover
      alignment: center
      repeat: no-repeat
      attachment: fixed
      opacity: 100
```

That sets `--view-background`, which takes precedence over the theme.

## Installation

1. This repository is not in the HACS default list, so add it as a **custom
   repository** first: HACS → ⋮ → *Custom repositories* → repository
   `e11en/homeassistant-theme`, type *Theme*. Then download it from the HACS
   theme list.

   Once added, this button opens it directly:

[![Open your Home Assistant instance and open a repository inside the Home Assistant Community Store.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=e11en&repository=homeassistant-theme&category=theme)

> [!NOTE]
> Install the [`uix`](https://github.com/Lint-Free-Technology/uix) integration via HACS for the transparent sidebar. It's a drop-in replacement for card-mod with backwards compatibility. After installing, don't forget to [add the integration for it](https://uix.lf.technology/quick-start/#add-ui-extension-service).

2. "Aether" should appear in your list of themes.

If it's missing, try reloading your themes or adding the following code to your `configuration.yaml` file (reboot required):

```yaml
frontend:
  themes: !include_dir_merge_named themes
```

3. (Optional) Set it as the default theme with this automation:

```yaml
alias: Frontend - Change theme
trigger:
  - platform: homeassistant
    event: start
action:
  - service: frontend.set_theme
    data:
      name: Aether
```

## Remarks

HACS installs a theme into `/config/themes/<filename without .yaml>/`, taken
from the *last* `.yaml` it finds under `themes/`. With one file that is
`/config/themes/aether/`. Renaming the file therefore leaves the old directory
behind — delete it by hand, or the old theme keeps showing up in the list.

Dropdown fixes from [Wessam Lauf's Frosted Glass Theme](https://github.com/wessamlauf/homeassistant-frosted-glass-themes)
