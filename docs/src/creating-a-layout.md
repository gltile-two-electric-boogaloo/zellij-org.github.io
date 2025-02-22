# Creating a Layout

Quickstart:
```bash
$ zellij setup --dump-layout default > /tmp/my-quickstart-layout-file.kdl
```

The layout structure is nested under a global `layout` node.

Within it are several possible node types:

- [`pane`](#panes) - the basic building blocks of the layout, can represent shells, commands, plugins or logical containers for other `pane`s.
- [`tab`](#tabs) - represents a navigational Zellij tab and can contain `pane`s
- [`pane_template`](#pane-templates) - define new nodes equivalent to `pane`s with additional attributes or parameters.
- [`tab_template`](#tab-templates) - define new nodes equivalent to `tab`s with additional attributes or parameters.

### Panes
`pane` nodes are the basic building blocks of a layout.

They could represent standalone panes:
```javascript
layout {
    pane // panes can be bare
    pane command="htop" // panes can have arguments on the same line
    pane {
        // panes can have arguments inside child-braces
        command "exa"
        cwd "/"
    }
    pane command="ls" { // or a mixture of same-line and child-braces arguments
        cwd "/"
    }
}
```

They could also represent logical containers:
```javascript
layout {
    pane split_direction="vertical" {
        pane
        pane
    }
}
```

**Note**: if panes represent logical containers, all their arguments should be specified on their title line.

#### split_direction
`split_direction` is a pane argument that indicates whether its children will be laid out vertically or horizontally.

**Possible values:** `"vertical"` | `"horizontal"`

**Default value if omitted:** `"horizontal"`

eg.
```javascript
layout {
    pane split_direction="vertical" {
        pane
        pane
    }
    pane {
        // value omitted, will be layed out horizontally
        pane
        pane
    }
}
```

**Note**: The `layout` node itself has a set value of "horizontal". It can be changed by adding a logical pane container:

```javascript
layout {
    pane split_direction="vertical" {
        pane
        pane
    }
}
```

#### size
`size` is a pane argument that represents the fixed or percentage space taken up by this pane inside its logical container.

**Possible values:** quoted percentages (eg. "50%") | fixed values (eg. 1)

**Note**: specifying fixed values that are not `unselectable` plugins is **currently unstable** and might lead to unexpected behaviour when resizing or closing panes. Please see [this issue](https://github.com/zellij-org/zellij/issues/1758).

eg.
```javascript
layout {
    pane size=5
    pane split_direction="vertical" {
        pane size="80%"
        pane size="20%"
    }
    pane size=4
}
```

#### borderless
`borderless` is a pane argument indicating whether a pane should have a frame or not.

**Possible values:** true | false

**Default value if omitted:** false

eg.
```javascript
layout {
    pane borderless=true
    pane {
        borderless true
    }
}
```

#### focus
`focus` is a pane argument indicating whether a pane should have focus on startup.

**Possible values:** true | false
**Default value if omitted:** false

**Note**: specifying multiple panes with focus will result in the first one of them being focused.

eg.
```javascript
layout {
    pane focus=true
    pane {
        focus true
    }
}
```

#### name
`name` is a string pane argument to change the default pane title.

**Possible values:** "a quoted string"

eg.
```javascript
layout {
    pane name="my awesome pane"
    pane {
        name "my amazing pane"
    }
}
```
#### cwd
A pane can have a `cwd` argument, pointing to its Current Working Directory.

**Possible values:** "/path/to/some/folder", "relative/path/to/some/folder"

**Note**: If the `cwd` is a relative path, it will be appended to its containers' cwd [read more about cwd composition](#cwd-composition)

eg.
```javascript
layout {
    pane cwd="/"
    pane {
        command "git"
        args "diff"
        cwd "/path/to/some/folder"
    }
}
```

#### command
`command` is a string (path) to an executable that should be run in this pane instead of the default shell.

**Possible values:** "/path/to/some/executable" | "executable" (the latter should be accessible through PATH)

eg.
```javascript
layout {
    pane command="htop"
    pane {
        command "/usr/bin/btm"
    }
}
```



##### args
A pane with a `command` can also have an `args` argument. This argument can include one or more strings that will be passed to the command as its arguments.

**Possible values:** "a" "series" "of" "quoted" "strings"

**Note**: `args` must be inside the `pane`'s child-braces and cannot be specified on the same line as the pane.

eg.
```javascript
layout {
    pane command="tail" {
        args "-f" "/path/to/my/logfile"
    }

    // Hint: include "quoted" shell arguments as a single argument:
    pane command="bash" {
        args "-c" "tail -f /path/to/my/logfile"
    }

}
```

#### edit
`edit` is a string (path) to a file that will be opened using the editor specified in the `EDITOR` or `VISUAL` environment variables. This can alternatively also be specified using the `scrollback_editor` config variable.

**Possible values:** "/path/to/some/file" | "./relative/path/from/cwd"

**Note**: If the value is a relative path, it will be appended to its containers' cwd [read more about cwd composition](#cwd-composition)

eg.
```javascript
layout {
    pane split_direction="vertical" {
        pane edit="./git_diff_side_a"
        pane edit="./git_diff_side_b"
    }
}
```

#### plugin
`plugin` is a pane argument the points to a Zellij plugin to load. Currently is is only possible to specify inside the child-braces of a pane followed by a URL `location` in quoted string.

**Possible values:** `zellij:internal-plugin` | `file:/path/to/my/plugin.wasm`

eg.
```
layout {
    pane {
        plugin location="zellij:status-bar"
    }
}
```

### Tabs
`tab` nodes can optionally be used to start a layout with several tabs.

**Note**: all tab arguments should be specified on its title line. The child-braces are reserved for its child panes.

eg.
```javascript
layout {
    tab // a tab with a single pane
    tab {
        // a tab with three horizontal panes
        pane
        pane
        pane
    }
    tab name="my third tab" split_direction="vertical" {
        // a tab with a name and two vertical panes
        pane
        pane
    }
}
```

#### split_direction
Tabs can have a `split_direction` just like `pane`s. This argument indicates whether the tab's children will be laid out vertically or horizontally.

**Possible values:** `"vertical"` | `"horizontal"`

**Default value if omitted:** `"horizontal"`

eg.
```javascript
layout {
    tab split_direction="vertical" {
        pane
        pane
    }
    tab {
        // if omitted, will be "horizontal" by default
        pane
        pane
    }
}
```

#### focus
Tabs can have a `focus` just like `pane`s. This argument indicates whether a tab should have focus on startup.

**Possible values:** true | false

**Default value if omitted:** false

**Note**: only one tab can be focused.

eg.
```javascript
layout {
    tab {
        pane
        pane
    }
    tab focus=true {
        pane
        pane
    }
}
```

#### name
Tabs can have a `name` just like `pane`s. This argument is a string to change the default tab title.

**Possible values:** "a quoted string"

eg.
```javascript
layout {
    tab name="my awesome tab"
    tab name="my amazing tab" {
        pane
    }
}
```


#### cwd
Tabs can have a `cwd` just like `pane`s - pointing to their Current Working Directory.
All panes in this tab will have this `cwd` prefixed to their own `cwd` (if they have one) or start in this `cwd` if they don't.

**Possible values:** "/path/to/some/folder", "relative/path/to/some/folder"

**Note**: If the `cwd` is a relative path, it will be appended to its containers' cwd [read more about cwd composition](#cwd-composition)

eg.
```javascript
layout {
    tab name="my amazing tab" cwd="/tmp" {
        pane // will have its cwd set to "/tmp"
        pane cwd="foo" // will have its cwd set to "/tmp/foo"
        pane cwd="/home/foo" // will have its cwd set to "/home/foo", overriding the tab cwd with its absolute path
    }
}
```

### Templates
Templates can be used avoid repetition when creating layouts.
Each template has a name that should be used directly as a node name instead of "pane" or "tab".

#### Pane Templates
Pane templates can be used to shorten pane attributes:
```javascript
layout {
    pane_template name="htop" {
        command "htop"
    }
    pane_template name="htop-tree" {
        command "htop"
        args "--tree"
        borderless true
    }
    // the below will create a template with four panes
    // the top and bottom panes running htop and the two
    // middle panes running "htop --tree" without a pane frame
    htop
    htop-tree
    htop-tree
    htop
}
```

Pane templates with the `command` attribute can take the `args` and `cwd` of their consumers:

```javascript
layout {
    pane_template name="follow-log" command="tail"
    follow-log {
        args "-f" "/tmp/my-first-log"
    }
    follow-log {
        args "-f" "my-second-log"
        cwd "/tmp"
    }
}
```
**Note**: the above only works for direct consumers and not other templates.

Pane templates can be used as logical containers. In this case a special `children` node must be specified to indicate where the child panes should be inserted.

**Note**: the `children` node can be nested inside `pane`s but not inside other `pane_template`s.

```javascript
layout {
    pane_template name="vertical-sandwich" split_direction="vertical" {
        pane
        children
        pane
    }
    vertical-sandwich {
        pane command="htop"
    }
}
```

Pane templates can include other pane templates.

```javascript
layout {
    pane_template name="vertical-sandwich" split_direction="vertical" {
        pane
        children
        pane
    }
    pane_template name="vertical-htop-sandwich" {
        vertical-sandwich {
            pane command="htop"
        }
    }
    pane_template name="vertical-htop-sandwich-below" split_direction="horizontal" {
        children
        vertical-htop-sandwich
    }
    vertical-htop-sandwich
    vertical-htop-sandwich-below {
        pane command="exa"
    }
}
```

The `children` node should be thought of as a placeholder for the pane using this template. 

This:

```javascript
layout {
    pane_template name="my_template" {
        pane
        children
        pane
    }
    my_template split_direction="vertical" {
        pane
        pane
    }
}
```

Will be translated into this:

```javascript
layout {
    pane {
        pane
        pane split_direction="vertical" {
            pane
            pane
        }
        pane
    }
}
```

#### Tab Templates
Tab templates, similar to pane templates, help avoiding repetition when defining tabs. Like `pane_templates` they can include a `children` block to indicate where their child panes should be inserted.

**Note**: for the sake of clarity, arguments passed to `tab_template`s can only be specified on their title line.

```javascript
layout {
    tab_template name="ranger-on-the-side" {
        pane size=1 borderless=true {
            plugin location="zellij:compact-bar"
        }
        pane split_direction="vertical" {
            pane command="ranger" size="20%"
            children
        }
    }
    ranger-on-the-side name="my first tab" split_direction="horizontal" {
        pane
        pane
    }
    ranger-on-the-side name="my second tab" split_direction="vertical" {
        pane
        pane
    }
}
```

##### Default Tab Template
There is a special `default_tab_template` node that can be used just like a regular `tab_template` node, but that would apply to all `tab`s in the template as well as all new tabs opened in the session.

**Note**: the `default_tab_template` will not apply to tabs using other `tab_template`s.

**Another note**: if no `tab`s are specified, the whole layout is treated as a `default_tab_template`.

```javascript
layout {
    default_tab_template {
        // the default zellij tab-bar and status bar plugins
        pane size=1 borderless=true {
            plugin location="zellij:tab-bar"
        }
        children
        pane size=2 borderless=true {
            plugin location="zellij:status-bar"
        }
    }
    tab // the default_tab_template
    tab name="second tab" // the default_tab_template with a custom tab name
    tab split_direction="vertical" { // the default_tab_template with three vertical panes between the plugins
        pane
        pane
        pane
    }
}
```

### `cwd` Composition

When a relative `cwd` property is specified in a node, it is appended to its container node's cwd in the follwing order:

1. `pane`
2. `tab`
3. [global cwd](#global-cwd)
4. The `cwd` where the command was executed

eg.
```javascript
layout {
    cwd "/hi"
    tab cwd="there" {
        pane cwd="friend" // opened in /hi/there/friend
    }
}
```

### Global `cwd`
The `cwd` property can also be specified globally on the `layout` node itself.

Doing this would make all panes in this layout start in this cwd unless they have an absolute path.

Eg. 
```javascript
layout {
    cwd "/home/aram/code/my-project"
    pane cwd="src" // will be opened in /home/aram/code/my-project/src
    pane cwd="/tmp" // absolute paths override the global cwd, this will be opened in /tmp
    pane command="cargo" {
        args "test"
        // will be started in /home/aram/code/my-project
    }
}
```
