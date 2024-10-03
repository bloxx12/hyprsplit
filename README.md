# hyprsplit

awesome / dwm like workspaces for [hyprland](https://github.com/hyprwm/hyprland)

A complete rewrite of
[split-monitor-workspaces](https://github.com/Duckonaut/split-monitor-workspaces)
that attempts to fix the issues I experienced with it. Improvements include:

- Workspaces on each monitor are determined by that monitor's ID
- Ability to grab windows that get lost in invalid workspaces when disconnecting
  monitors
- Dispatcher to swap all windows in active workspaces between two monitors
- Better handling of workspace params. For example `empty` will work properly
  and select an empty workspace on the current monitor.

## Installation

Requires Hyprland version >=`v0.36.0`

### Hyprpm

Load plugins on startup by putting exec-once = hyprpm reload -n in your hyprland
config.

```
hyprpm update
hyprpm add https://github.com/shezdy/hyprsplit
hyprpm enable hyprsplit
```

### Manual

Make sure you have the Hyprland headers installed (see
[hyprland wiki](https://wiki.hyprland.org/Plugins/Using-Plugins/#manual))

If you are compiling for a numbered version of Hyprland, check for a commit pin
in hyprpm.toml, and reset the plugin to the second hash in the pair.

Compile the plugin:

```
make all
```

Finally add the following to your config
`plugin = /path/to/hyprsplit/hyprsplit.so`, or run
`hyprctl plugin load /path/to/hyprsplit/hyprsplit.so`

### NixOS
The following code snippets describes an example installation of hyprsplit on a NixOS system:
```nix
inputs = {
    hyprland.url = "github:hyprwm/hyprland";

    hyprsplit = {
      url = "github:shezdy/hyprsplit";
      # if you have hyprland from nixpkgs use this
      # inputs.hyprland.follows = "nixpkgs";

      inputs.hyprland.follows = "hyprland";
    };
  };
```
Then, in your Hyprland home-manager configuration, add the plugin to your plugins:
```nix
{
  inputs,
  pkgs,
  ...
}: let
  hyprsplit = inputs.hyprsplit.packages.${pkgs.system};
in {
  wayland.windowManager.hyprland = {
    enable = true;
    plugins = [
      hyprsplit
    ];
  };
}
```
## Configuration

### Options

| name                  | description                                                                                                            | type | default |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------- | ---- | ------- |
| num_workspaces        | Number of workspaces on each monitor                                                                                   | int  | 10      |
| persistent_workspaces | if true, will make workspaces on each monitor persistent (they will always exist and will not be destroyed when empty) | bool | false   |

### Dispatchers

| Dispatcher                  | Description                                                                                                                | Params                                                |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| split:workspace             | Replacement for `workspace`                                                                                                | workspace                                             |
| split:movetoworkspace       | Replacement for `movetoworkspace`                                                                                          | workspace OR `workspace,window` for a specific window |
| split:movetoworkspacesilent | Replacement for `movetoworkspacesilent`                                                                                    | workspace OR `workspace,window` for a specific window |
| split:swapactiveworkspaces  | Swaps all windows in active workspaces between two monitors                                                                | two monitors separated by a space                     |
| split:grabroguewindows      | Finds all windows that are in invalid workspaces and moves them to the current workspace. Useful when unplugging monitors. | none                                                  |

### Example Config

```
plugin {
    hyprsplit {
        num_workspaces = 6
    }
}

bind = SUPER, 1, split:workspace, 1
bind = SUPER, 2, split:workspace, 2
bind = SUPER, 3, split:workspace, 3
bind = SUPER, 4, split:workspace, 4
bind = SUPER, 5, split:workspace, 5
bind = SUPER, 6, split:workspace, 6

bind = SUPER SHIFT, 1, split:movetoworkspacesilent, 1
bind = SUPER SHIFT, 2, split:movetoworkspacesilent, 2
bind = SUPER SHIFT, 3, split:movetoworkspacesilent, 3
bind = SUPER SHIFT, 4, split:movetoworkspacesilent, 4
bind = SUPER SHIFT, 5, split:movetoworkspacesilent, 5
bind = SUPER SHIFT, 6, split:movetoworkspacesilent, 6

bind = SUPER, D, split:swapactiveworkspaces, current +1
bind = SUPER, G, split:grabroguewindows
```
