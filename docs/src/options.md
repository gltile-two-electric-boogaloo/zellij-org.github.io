# Options

Configuration options can be set directly at the root of the [configuration file](./configuration.md)

 These include:

### on_force_close

Choose what to do when zellij receives SIGTERM, SIGINT, SIGQUIT or SIGHUP
eg. when terminal window with an active zellij session is closed

Options:
  - detach (Default)
  - quit

```javascript
on_force_close "quit"
```

### simplified_ui

Send a request for a simplified ui (without arrow fonts) to plugins

Options:
  - true
  - false (Default)

```javascript
simplified_ui true
```
### default_shell

Choose the path to the default shell that zellij will use for opening new panes

Default: $SHELL

```javascript
default_shell "fish"
```

### pane_frames

Toggle between having pane frames around the panes

Options:
  - true (default)
  - false

```javascript
pane_frames true
```
### theme

Choose the Zellij color theme. This theme must be specified in the themes section or loaded from the themes folder. See [themes](./themes.md)

Default: default

```javascript
theme "default"
```

### default_layout

The name of the layout to load on startup (must be in the layouts folder). See [layouts](./layouts.md)

Default: "default"

```javascript
default_layout "compact"
```

### default_mode "locked"

Choose the mode that zellij uses when starting up.

Default: normal

```javascript
default_mode "locked"
```

### mouse_mode

Toggle enabling the mouse mode.
On certain configurations, or terminals this could
potentially interfere with copying text.

Options:
  - true (default)
  - false

```javascript
mouse_mode false
```

### scroll_buffer_size

Configure the scroll back buffer size
This is the number of lines zellij stores for each pane in the scroll back
buffer. Excess number of lines are discarded in a FIFO fashion.

Valid values: positive integers

Default value: 10000

```javascript
scroll_buffer_size 10000
```

### copy_command

Provide a command to execute when copying text. The text will be piped to
the stdin of the program to perform the copy. This can be used with
terminal emulators which do not support the OSC 52 ANSI control sequence
that will be used by default if this option is not set.

Examples:

```javascript
copy_command "xclip -selection clipboard" // x11
copy_command "wl-copy"                    // wayland
copy_command "pbcopy"                     // osx
```

### copy_clipboard

Choose the destination for copied text
Allows using the primary selection buffer (on x11/wayland) instead of the system clipboard.
Does not apply when using copy_command.

Options:
  - system (default)
  - primary

```javascript
copy_clipboard "primary"
```

### copy_on_select

Enable or disable automatic copy (and clear) of selection when releasing mouse

Default: true

```javascript
copy_on_select false
```

### scrollback_editor

Path to the default editor to use to edit pane scrollbuffer as well as the CLI and layout `edit` commands

Default: $EDITOR or $VISUAL

```javascript
scrollback_editor "/usr/bin/vim"
```

### mirror_session

When attaching to an existing session with other users,
should the session be mirrored (true)
or should each user have their own cursor (false)
Default: false

```javascript
mirror_session true
```

### layout_dir

The folder in which Zellij will look for layouts

```javascript
layout_dir "/path/to/my/layout_dir"
```

### theme_dir

The folder in which Zellij will look for themes

```javascript
theme_dir "/path/to/my/theme_dir"
```

### env
A key -> value map of environment variables that will be set for each terminal pane Zellij starts.

```javascript
env {
    RUST_BACKTRACE 1
    FOO "bar"
}
```

### rounded corners
Set whether the pane frames (if visible) should have rounded corners.

This config variable is set differently than others:

```javascript
ui {
    pane_frames {
        rounded_corners true
    }
}
```

