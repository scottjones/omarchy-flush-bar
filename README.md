# Flush bar

An [Omarchy](https://omarchy.org) shell plugin that lets a transparent bar sit
flush over the windows below it — no widget overlap, no new setting, and no
change to any default. Works on any edge, live, and multi-monitor. This lets
the text of the workspaces and icons be centered in the "empty" space above
your windows rather than offset by the gap.

This is the plugin form of [basecamp/omarchy#6381](https://github.com/basecamp/omarchy/pull/6381).

## How it works

The bar already reserves only its own thickness (its layer-shell exclusive
zone), so it never overlaps a window. What's missing is the window gap under
it: this plugin keeps the gap on the bar's edge in step with the bar — `0`
when the bar is transparent (windows flush) or the same as the other edges
when opaque.

It ships as a headless `service` plugin. The shell persists every bar change
(edge moves and the transparency toggle) into `shell.json`, which the service
observes through `shell.barConfig`, so it reacts to the same state the bar
itself renders — on change, once at startup, and on a Hyprland config reload
(which resets `gaps_out`, so the service re-asserts it).

The gap logic lives in `gaps.sh`. It reads the current `gaps_out` and only
ever touches two edges: the bar's own edge, and the edge the bar just moved
off of (restored to the window gap so a moved-away edge isn't stranded at 0).
Every other edge is left exactly as configured, so per-edge custom gaps, a
hand-set zero, and toggles like `window-no-gaps` are preserved.

## Install

```bash
omarchy plugin add https://github.com/scottjones/omarchy-flush-bar.git
omarchy plugin enable scottjones.flush-bar
```

Or by hand:

```bash
git clone https://github.com/scottjones/omarchy-flush-bar.git ~/.config/omarchy/plugins/scottjones.flush-bar
omarchy-shell shell rescanPlugins
omarchy plugin enable scottjones.flush-bar
```

Disabling the plugin (`omarchy plugin disable scottjones.flush-bar`) stops the
syncing; if your bar was transparent and flush, reload Hyprland or set
`general:gaps_out` to restore the gap on the bar's edge.

## Test

```bash
./test/gaps-test.sh
```
