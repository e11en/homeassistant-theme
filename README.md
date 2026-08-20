# Aether

A single flat-background glass theme for Home Assistant, with automatic dark
mode support.

Fork of [Nezz/homeassistant-visionos-theme](https://github.com/Nezz/homeassistant-visionos-theme),
which is in turn based on [Bas Nijholt's iOS Themes](https://github.com/basnijholt/lovelace-ios-themes).

## What's different from upstream

### One theme instead of two

Upstream ships two themes in `themes/`: `visionos` and `Liquid Glass`. HACS
installs a theme repository as a whole, so downloading it gives you both —
they are not separate installs.

Aether started as **`visionos`, renamed** (see *Retuned* below for what has
changed since). `Liquid Glass` is gone. The two were ~95% identical anyway;
where they differed, Liquid Glass was the louder of the pair:

| | visionos (= Aether) | Liquid Glass (dropped) |
|---|---|---|
| `ha-card-border-radius` | 20px | 34px |
| `ha-card-backdrop-filter` | `blur(20px)` | `blur(8px)` |
| `ha-card-box-shadow` | hairline inset edges | 3px inset edges + inner glow |
| dark background | `rgb(25, 24, 22)` neutral | `rgb(18, 11, 25)` purple-tinted |
| light background | `rgb(84, 80, 76)` | `rgb(76, 80, 84)` |
| card-mod | `card-mod-card` | `card-mod-card-yaml`, plus square slider track bars |
| card-mod sidebar | base | base, plus rounded list items |

### Retuned

| | upstream visionos | Aether |
|---|---|---|
| accent (`primary-color`) | orange `#FF9F0A` | cyan `#38f2e9` |
| `ha-color-primary-05…95` | orange ramp | cyan ramp |
| `modes:` | light + dark variants | **none** — one set of values |
| background | `rgb(25,24,22)` dark / `rgb(84,80,76)` light | `#0d0f10` |
| `ha-card-background` | `rgba(0,0,0,0.3)` dark — darker than the view | `rgba(128,128,128,0.3)` — lighter |

**There is no `modes:` block.** This theme is always dark, so it carries one
set of values rather than a light and a dark variant. Upstream's light mode is
a warm grey that turns brown against these cards, and "Automatic" flips into it
during the day.

The surface values are the dark variant's, with one exception:
`ha-card-background` is the **light** variant's, so cards sit *lighter* than
the view instead of darker. `rgba(128,128,128,0.3)` over `#0d0f10` lands on
~`#303132`.

`orange-color`, `yellow-color` and `label-badge-red` are deliberately left
alone: those are entity state colours, and an active light shouldn't read as
"accent". Note `label-badge-red` is orange despite its name.

### No background image

Upstream ships a wallpaper per mode (`day.jpg` / `night.jpg`) and points
`lovelace-background` at it. Aether drops the image *and* the
`lovelace-background` variable, so a view falls back to the flat
`primary-background-color` of its mode.

Removing only the image is not enough: Home Assistant renders the view
background as

```css
background: var(--view-background, var(--lovelace-background, var(--primary-background-color)));
```

A `lovelace-background` that is defined but resolves to a missing variable is
invalid at computed-value time, so the whole declaration is dropped instead of
falling through to the last fallback. The variable has to go entirely.

Note that the glass effect leans on having something to blur. With a flat
background, `ha-card-backdrop-filter` mostly shows up where cards scroll under
the header, and the depth comes from the card edges in `ha-card-box-shadow`.

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
