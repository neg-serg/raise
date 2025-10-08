# raise

Run or raise implemented for Hyprland. It will raise window if it exists,
or cycle to next window if current window matches class to focus. Otherwise
it will launch new window.

```
$ raise
Usage: raise [-c <class>] -e <launch> [-m <field[:method]=pattern>...]

Raise window if it exists, otherwise launch new window.

Options:
  -c, --class       class to focus (shorthand for `--match class=...`)
  -e, --launch      command to launch
  -m, --match       additional matcher in the form field[:method]=pattern
  --help            display usage information
```

### Matching

The `--match` flag allows choosing how a window should be selected. Each
matcher uses the format `field[:method]=pattern` and multiple matchers can be
combined; they all have to match for a window to qualify.

Supported fields:
- `class` — current window class reported by Hyprland
- `initial-class` — class when the window was first created
- `title` — current window title
- `initial-title` — original title assigned on window creation
- `tag` — window tag assigned via dynamic tags
- `xdgtag` — XDG surface tag (`xdgTag` in `hyprctl clients`)

Aliases: you can also use the short forms `c`, `initialClass`, `initialTitle`, and `xdg-tag`.

Supported methods (default is `equals`):
- `equals` / `eq`
- `contains` / `substr`
- `prefix` / `starts-with`
- `suffix` / `ends-with`
- `regex` / `re`

Examples:

```
raise --launch firefox --match class=firefox
raise --launch alacritty --match title:contains=notes
raise --launch slack --match class=Slack --match title:regex="(?i)daily"
```

## Install `raise`

There are multiple ways to install this:

1. Go to [releases](https://github.com/svelterust/raise/releases)
2. `cargo install --git https://github.com/svelterust/raise`
3. Add `github:svelterust/raise` as a flake to your NixOS configuration

For NixOS, add raise to your flake inputs:

```nix
inputs = {
  raise.url = "github:svelterust/raise";
};
```

Then add it to your system, for instance: `environment.systemPackages = [raise.defaultPackage.x86_64-linux];`

## Example configuration

I like having <kbd>Super</kbd> + `<key>` bound to run or raise, and <kbd>Super</kbd> + <kbd>Shift</kbd> + `<key>` to launch application regularly.

```
bind = SUPER, V, exec, raise --class "Alacritty" --launch "alacritty"
bind = SUPER_SHIFT, V, exec, alacritty
bind = SUPER, C, exec, raise --class "firefox" --launch "firefox"
bind = SUPER_SHIFT, C, exec, firefox
bind = SUPER, F, exec, raise --class "emacs" --launch "emacsclient --create-frame"
bind = SUPER_SHIFT, F, exec, emacsclient --create-frame
```

## How to find class?

Run `hyprctl clients` while window is open, and look for `class: <class>`.
