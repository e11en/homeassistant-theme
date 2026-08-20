# visionOS & iOS 26 Liquid Glass Theme

Theme inspired by visionOS for Home Assistant with automatic dark mode support.

Fork of [Nezz/homeassistant-visionos-theme](https://github.com/Nezz/homeassistant-visionos-theme).

## What's different in this fork

**No background image.** Upstream ships a wallpaper per mode (`day.jpg` /
`night.jpg`, `macOS_26_*.webp`) and points `lovelace-background` at it. Both
themes here drop the image *and* the `lovelace-background` variable, so a view
falls back to the flat `primary-background-color` of its mode.

Removing only the image is not enough: Home Assistant renders the view
background as

```css
background: var(--view-background, var(--lovelace-background, var(--primary-background-color)));
```

A `lovelace-background` that is defined but resolves to a missing variable is
invalid at computed-value time, so the whole declaration is dropped instead of
falling through to the last fallback. The variable has to go entirely.

Set your own wallpaper per view instead — *edit view → Background settings*, or
in YAML:

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

### visionOS
<img width="500" alt="vision-light" src="https://github.com/user-attachments/assets/f054c59e-7198-4476-9a2e-4e0caec49df8" /><img width="500" alt="vision-dark" src="https://github.com/user-attachments/assets/61179b34-d25b-4902-9883-91156f5dc659" />

### Liquid Glass
<img width="500" alt="ios-light" src="https://github.com/user-attachments/assets/c60d760b-4531-41c2-b8b5-47404e8743d7" /><img width="500" alt="ios-dark" src="https://github.com/user-attachments/assets/273f0e86-180e-42b3-abe0-bab25c359584" />

> [!NOTE]
> The screenshots are upstream's and still show the bundled wallpapers.

## Installation

1. This fork is not in the HACS default list, so add it as a **custom
   repository** first: HACS → ⋮ → *Custom repositories* → repository
   `e11en/homeassistant-theme`, type *Theme*. Then download it from the HACS
   theme list.

   Once added, this button opens it directly:

[![Open your Home Assistant instance and open a repository inside the Home Assistant Community Store.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=e11en&repository=homeassistant-theme&category=theme)

> [!NOTE]  
> Install the [`uix`](https://github.com/Lint-Free-Technology/uix) integration via HACS to make the sidebar transparent. It's a drop-in replacement for card-mod with backwards compatibility. After installing, don't forget to [add the integration for it](https://uix.lf.technology/quick-start/#add-ui-extension-service).

2. You should see the "Liquid Glass" and "visionos" themes appear in your list of themes.

If it's missing, try reloading your themes or adding the following code to your `configuration.yaml` file (reboot required):

```yaml
frontend:
  themes: !include_dir_merge_named themes
```

3. (Optional) You can set this as the default theme with the following automation:
```
alias: Frontend - Change theme
trigger:
  - platform: homeassistant
    event: start
action:
  - service: frontend.set_theme
    data:
      name: visionos
```

## Remarks

Sample dashboard configuration from the themes is available [here](https://github.com/Nezz/homeassistant-visionos-theme/blob/sample/sample.yaml) (upstream)

Based on [Bas Nijholt's iOS Themes](https://github.com/basnijholt/lovelace-ios-themes)

Dropdown fixes from [Wessam Lauf's Frosted Glass Theme](https://github.com/wessamlauf/homeassistant-frosted-glass-themes)
