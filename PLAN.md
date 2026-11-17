# LIB_TOOLBAR: the plan

Monday, November 16, 2026. Draft 1, by Fable 5.2, for the owner to correct. TODO A-40, using the
owner's answer to O-8. A plan only: no code yet.

## 1. What it is for

The owner's brief: "a new one stop shop floating launcher tool of buttons to access all the
latest and greatest and most important items in the project. It will persist as front, but
buttons for actions to stacks or menus or file explorer locations."

His answer to O-8 was not a sketch but a pointer: the 2007 iGame3D main window
(`XTJSON/batch/rev/ig3d_MainUI050107/stack_ig3d_UI_MAIN_may2007.json`), "now it's fill in the
blanks", with Unicode characters for the missing images. Read from that harvest (data only, no
images opened):

| The 2007 main window | What the toolbar takes from it |
|---|---|
| a strip 1306 wide and 50 high, apart from the 3D window | the shape: one thin strip, floating |
| a menu row: File, Edit, Library, Mesh, Window, Dev, Help | a few menus at one end |
| rows of small icon buttons in named groups (camera tools, mesh tools, actors and effects) | buttons in named groups, a Unicode symbol each |
| `WindowPickPop`: a popup of the open windows | the Stacks menu |
| the `Dev` stack: a filterable list ("Dev OpenStacks") | the Latest menu and the filter box |
| `LevelsFilter`: type to narrow a list | the same filter box |

So LIB_TOOLBAR is **that strip, remade as data**: what the buttons are is a text file the owner
edits; the strip is drawn from it. It ships with one file, the projects launcher; any app can
bring its own (iGame3D XT floating its 26 tools as in 2007).

## 2. What it stands on

- LIB_GUI: the strip is a `.gui` layout that `Toolbar.Layout` makes from the content file and
  `Gui.Build` shows. LIB_TOOLBAR has no widget code.
- LIB_THEME for colours; LIB_USERS for the name shown at the left end and for hiding what the
  driver may not press; LIB_DATA for remembering where the strip was left.
- LIB_DEV for what already works and is reused, not rewritten: the launcher that opens the
  current script from disk at every start (so updates need no rebuild), `game_launch` (a game
  runs as its own program), `window_shape` (dragging a window that has no title bar).

## 3. The content file: `*.toolbar`

Plain UTF-8 text, the same reading rule as a `.gui` file: tabs between fields; field 1 the kind,
field 2 the name; after that, in any order, a label and `key=value` options. `#` is a comment.

```
toolbar	1
group	review	Review
button	status	☑	open=LIB_DEV/Status_Browser.livecode	tip=What the AI made: look, mark, answer
menu	latest	★	lines=LIB_LLMOUT/FOR_REVIEW.txt	newest=12	tip=The twelve newest files to review

group	play	Play
button	games	▶	open=LIB_DEV/GAME_Browser.livecode	tip=Pick a game, play it
menu	try	☞	files=LIB_TEST/*.bat	tip=Things to open and try now

group	places	Places
menu	folders	▤	tip=Folders
item	projects	Projects	in=folders	folder=.
item	inbox	Inbox (LIB_LLMIN)	in=folders	folder=LIB_LLMIN
item	data	Data folder	in=folders	folder=data:
```

### Kinds

| Kind | What it is |
|---|---|
| `group` | a named run of buttons, with a thin divider before it (the old icon groups) |
| `button` | one press, one action |
| `menu` | a button that drops a list: of `item` lines, or filled from a folder or a file at the moment it is pressed |
| `item` | one line of a menu (`in=<menu>`); a label of `-` is a dividing line |
| `filter` | a small box: typing narrows the menu that is open (the old `devfilter`) |
| `who` | the driver's name from LIB_USERS (`FOR BILL`, `Opus`); also the handle the strip is dragged by |

### Actions (one per button or item)

| Option | Does |
|---|---|
| `open=<file>` | opens it the way a double-click would (a stack, a `.bat`, a text file) |
| `folder=<folder>` | shows it in the file explorer. `data:` or `data:Models` = the Data folder or one of its kinds (LIB_DATA) |
| `play=<game>` | starts a game as its own program (LIB_DEV `game_launch`) |
| `url=<address>` | the web browser |
| `send=<message>` | to the toolbar itself (`Toolbar.Reload`, `Toolbar.Dock top`, `Toolbar.Quit`), or, when the toolbar runs inside an app, to that app |
| `note=<file>` | asks for one line and appends it, stamped with who and when (game notes; Tell Claude) |

### Menus that fill themselves

| Option | The menu lists |
|---|---|
| `files=<folder>/<pattern>` | the files that match, newest first; picking one opens it |
| `lines=<file>` with `newest=<n>` | the last n lines of a list file such as `FOR_REVIEW.txt`; picking one shows that file in the explorer |
| `stacks=open` | the open windows of the app the toolbar runs inside (the old WindowPickPop); hidden when it runs alone |

Other options: `tip=`, `may=<word>` (hidden unless `Users.May`), `key=`.
Paths are written from the projects folder (the folder that holds `LIB_LLMOUT`, found by
walking up, as the Status Browser launcher does); in an installed copy the same names are
found by the install's find-file rule. No path in a toolbar file names a drive or a user.

### The projects launcher, as shipped (the "fill in the blanks")

Every target below was checked to exist today, except where marked.

| Group | Button | Symbol | Opens |
|---|---|---|---|
| | who | | the driver; drag here to move the strip |
| Review | Status | ☑ | `LIB_DEV/Status_Browser.livecode` |
| | Latest | ★ | menu: newest 12 of `LIB_LLMOUT/FOR_REVIEW.txt` |
| | Tell | ✉ | one line to `LIB_LLMIN/LIB_DEV/TELL_CLAUDE.txt` (the Status Browser's button, same file) |
| Play | Games | ▶ | `LIB_DEV/GAME_Browser.livecode` |
| | Try | ☞ | menu: `LIB_TEST/*.bat` |
| | Note | ✍ | one line to `LIB_LLMIN/LIB_GAMES/GAME_NOTES.txt` |
| Make | Editor | ✎ | iGame3D XT (target to settle when built: it runs in the Game Player) |
| | Tools | ⚙ | menu: BitEditor, Theme Studio, TTRPG Studio, XTJSON Batch; GUI Builder when it exists |
| | Spark | ⚡ | menu: `LIB_SPARK/START_SPARK_*.bat` |
| Places | Folders | ▤ | menu: Projects, Inbox, Read pile (`LIB_LLMOUT/Read`), Data folder, `LIB_TEST` |
| | Docs | ? | menu: `LIB_SYSTEM/LIB_ALPHA.md`, `STATUS.md`, `TODO.md`, `NIGHT_SHUTDOWN.md` |
| | Stacks | ❐ | menu: open windows (only inside an app) |
| | More | ⋮ | menu: Reload, Dock top / bottom / left / right / free, About, Quit |

## 4. The handlers

```
start using stack ".../LIB_TOOLBAR/livecode/lib_toolbar.livecodescript"

Toolbar.Open tFolder & "/toolbars/projects.toolbar"     -- reads, lays out, shows the strip
Toolbar.Dock "top"                                      -- top, bottom, left, right, free
Toolbar.Reload                                          -- after editing the file
put Toolbar.Action("status") into tWhat                 -- "open <tab> <full path>": what a press would do
Toolbar.Press "status"                                  -- does it (what a click calls)
```

| Handler | Gives or does |
|---|---|
| `Toolbar.Parse(pText)` / `Toolbar.Load(pFile)` | the content as an array; `Toolbar.Problems()` lists what was wrong |
| `Toolbar.Layout(pBarA, pLength, pSide)` | a LIB_GUI layout array for a strip that long: sizes, order, a second row when it does not fit |
| `Toolbar.Open pFile` | Load, Layout, `Gui.Build`, show, in front |
| `Toolbar.Action(pName)` | what pressing it would do, as text, without doing it |
| `Toolbar.Press pName` | does it; logs it with `Users.Log` |
| `Toolbar.MenuItems(pName)` | a menu's lines as they would be now (folder and list menus are read at this moment) |
| `Toolbar.Dock pSide` / `Toolbar.Place()` | where the strip sits; remembered in the Data folder |
| `Toolbar.DryRun pFlag` | true: `Press` records instead of launching (tests) |
| `Toolbar.Quit` | closes and frees the stack (LIB_ALPHA 12b) |

### The window

| Decision | |
|---|---|
| Shape | a strip one button high (32 px), as long as the screen edge it is docked to; free = as long as its buttons |
| In front | the layout's `float=front`: the native face makes it a window that stays above every program. To be confirmed by the owner's eyes; if it will not, the fallback is a palette (above our own windows only) |
| No title bar | dragged by the `who` label; the title is still set with `Users.Title` (it shows in the task switcher) |
| Refreshing | menus read their folder or file **when pressed**. No timer, no watching: nothing runs while the strip sits there |
| Missing target | the button is greyed and `Toolbar.Problems()` says which; the strip still opens |
| Two ways to run | **alone** (default): its own small program, started once, opening the current script and content file from disk at each start, so edits need no rebuild. **Inside an app**: loaded as a library, `send=` goes to that app, `stacks=open` works |

## 5. Who owns what (the same words in all four plans)

| Library | Owns | Never does |
|---|---|---|
| **LIB_USERS** | who is at the controls: the roster, the current user, the wording of window titles, the stamp on log lines, the "may I" words | draws nothing, opens no windows |
| **LIB_THEME** (exists) | colours and line weight, as `themes/<name>.txt` | no layout, no widgets |
| **LIB_GUI** | what a window contains: the `.gui` layout file, the widget kinds, the calls `Gui.*`, the message `guiDo`, the native face (real xTalk controls) and the GUI Builder app | no colours of its own (asks LIB_THEME), no content of its own (no launcher list, no user names) |
| **LIB_RAYGUI** | one more face for the same `.gui` file: raygui drawn inside xt3d (iGame3D xRay); the `xt3d_Gui*` calls; how a theme becomes a raygui style | no layout format and no widget kinds of its own |
| **LIB_TOOLBAR** | one app: the floating launcher strip, and its content file `*.toolbar` (what the buttons are, what they open) | no widget code (its window is built by LIB_GUI); it does not decide who may press what (asks LIB_USERS) |

The rules that follow from the table:

1. A window is laid out once, as a `.gui` file, and shown by any face. Nobody ports a window by hand.
2. A new widget kind is added to LIB_GUI first (the format and the native face), then to the other faces.
3. Colour is LIB_THEME's. Who is LIB_USERS's. What is on the launcher is LIB_TOOLBAR's. How it is drawn in raylib is LIB_RAYGUI's.
4. A row of tools inside an app's own window (iGame3D XT's 26 tools) is ordinary `.gui` buttons belonging to that app. LIB_TOOLBAR is only the floating strip that sits outside a window: the projects launcher, or an app's own tools floated the way the 2007 iGame3D main strip was.
5. XTJSON stays the full, lossless record of a stack. A `.gui` file is the small design a person or Spark can write by hand. There is one tool, one way: XTJSON harvest to `.gui`.
6. No script builds a window title by hand: it asks `Users.Title`.

Build order across the four: (1) LIB_USERS core, (2) LIB_GUI stage 1: the format and the native face,
(3) LIB_TOOLBAR, the first real customer, (4) LIB_RAYGUI: one button proven in the bench, then the
player, (5) the GUI Builder app, (6) the vector face for iGame3D XT.

Where LIB_TOOLBAR meets the existing libraries: LIB_DEV's Status Browser and Game Browser stay
what they are and become two of its buttons; `LIB_TEST` is its Try menu (LIB_ALPHA rule 15:
what the owner should open and try now); `FOR_REVIEW.txt` is its Latest menu. iGame3D XT's tool
row stays inside the editor window (LIB_EDITOR); if the owner wants it floating as in 2007, the
editor ships an `igame3d_xt.toolbar` made from its `kTools` list (same 26 symbols) and loads this
library. A game never uses the toolbar (the engine contract has no call for it).

## 6. What is built first

| Step | What | Done when |
|---|---|---|
| 1 | `Toolbar.Parse / Load / Problems / Action / MenuItems`, `projects.toolbar` | headless test passes; every shipped target exists |
| 2 | `Toolbar.Layout` and `Toolbar.Open` on LIB_GUI's native face; `Press` for `open`, `folder`, `send` | the owner sees the strip, presses Status and Folders |
| 3 | stays in front; dock and remembered place; drag by `who` | the owner works a session with it up and says where it should sit |
| 4 | `files=`, `lines=`, `note=`, `play=`, the filter box | Latest shows what the AI just made, without a refresh button |
| 5 | the stand-alone program (the launcher pattern from LIB_DEV) | it is on the taskbar and survives closing everything else |
| 6 | inside an app: `stacks=open`, an app's own `.toolbar` | iGame3D XT floats its tools, if wanted |

Needs LIB_GUI stage 2 and LIB_USERS step 2 first. If the owner wants the strip before LIB_GUI
exists, steps 1 and 2 can be done with a throwaway native build of about 60 lines, replaced
later without changing the content file. Default: wait for LIB_GUI (a few days).

## 7. How it is tested headless

`OXT-BEYOND/tests/oxt_headless.py`, body `tests/t_toolbar.txt`, on a temporary copy with a
made-up projects folder (three files, one folder, one list file), `Toolbar.DryRun true`:

- the shipped `projects.toolbar` reads with no problems; group, button and menu counts are as in the table above
- a file with a missing name, a repeated name and an unknown action gives three problem lines and still loads
- `Toolbar.Action("status")` is `open` and the full path of the made-up file; a path that climbs out of the projects folder (`../..`) is refused
- `Toolbar.MenuItems("try")` lists the made-up `.bat` files newest first; `("latest")` gives the last lines of the list file
- `Toolbar.Layout` at 1300 px: one row, every button inside the strip, none overlapping; at 300 px: two rows
- `Toolbar.Open` builds an invisible stack: the control count matches; `Toolbar.Press "status"` records one launch and one log line, and launches nothing
- as SPARK (`Users.Take "SPARK"`), buttons with `may=launch` are not built
- a missing target: its button is disabled

Headless cannot check that the strip stays in front or that a real program starts: step 2 and 3
are checked by the owner pressing the buttons.

## 8. Options rejected

| Option | Why not |
|---|---|
| The button list written into the script | the owner should change his launcher in a text file, not in code |
| A plugin or palette of the IDE only | it dies with the IDE, and the point is to put the IDE away |
| Windows taskbar pins and a folder of `.bat` files (LIB_TEST today) | no "latest", no menus, no names of who is driving, and nothing to carry to another PC |
| A web page of links | cannot stay in front, cannot open stacks or folders |
| Watching folders on a timer to stay fresh | nothing should poll; a menu read when pressed is always fresh |
| Picture icons | images are not handled; a Unicode symbol is text, themeable, and survives every face |
| Its own drawing code | then it would be redone for raygui and the vector face; LIB_GUI draws it once |
| One strip that is also the editor's toolbar | a launcher and an editor's tools change for different reasons; same library, two content files |

## 9. Questions only the owner can answer

| # | Question | Default taken |
|---|---|---|
| 1 | Is the 2007 strip the right shape (thin, across a screen edge), and which edge? | yes; top edge, one row, 32 px |
| 2 | Is the button list in section 3 right: what is missing, what is noise? | as listed; you edit `projects.toolbar` |
| 3 | Above every program, or only above our own windows? | above every program |
| 4 | Symbols: Unicode characters, or 1-bit glyphs drawn from LIB_BIT ("the binary strings")? | Unicode now; LIB_BIT glyphs can replace a symbol later by name, without changing the file format |
| 5 | Should iGame3D XT's tools float in their own strip as in 2007, or stay in the editor window? | stay in the window; floating is step 6, on your word |
| 6 | Should the strip start with Windows? | no; you start it; it remembers its place |
| 7 | Do you want the strip before LIB_GUI is ready (a throwaway build)? | no: wait the few days |
