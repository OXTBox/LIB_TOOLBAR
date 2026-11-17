# LIB_TOOLBAR

Monday, November 16, 2026. Planned, not built yet: read `PLAN.md`.

A thin floating strip of buttons that stays in front: the one place to reach the latest and
most important stacks, menus and folders of the project. It is the 2007 iGame3D main strip
remade as data: what the buttons are is a text file (`*.toolbar`) you edit; a button is a
Unicode symbol and one action.

```
toolbar	1
button	status	☑	open=LIB_DEV/Status_Browser.livecode	tip=What the AI made
menu	try	☞	files=LIB_TEST/*.bat	tip=Things to open and try now
```

| It owns | It leaves to |
|---|---|
| the launcher strip, the `*.toolbar` content file, what a press does | drawing the window: LIB_GUI. Colours: LIB_THEME. Who is driving and what they may press: LIB_USERS |
