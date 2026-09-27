# visidata

## Installation

```bash
brew install visidata
```

## Default keymaps

| sheet      | module    | longname           | keystrokes               | description                                                           |
| ---------- | --------- | ------------------ | ------------------------ | --------------------------------------------------------------------- |
| BaseSheet  | help      | help-commands      | zCtrl+H                  | list commands and keybindings available on current sheet              |
| BaseSheet  | sheets    | quit-sheet         | q                        | quit current sheet                                                    |
| TableSheet | layout    | resize-col-max     | \_                       | toggle width of current column between full and default width         |
| TableSheet | search    | search-col         | /                        | search for regex forwards in current column                           |
| TableSheet | sheets    | type-int           | #                        | set type of current column to int                                     |
| TableSheet | sort      | sort-asc           | [                        | sort ascending by current column; replace any existing sort criteria  |
| TableSheet | sort      | sort-desc          | ]                        | sort descending by current column; replace any existing sort criteri… |
|            |           |                    | Ctrl+F                   | scroll one page forward                                               |
|            |           |                    | Ctrl+B                   | scroll one page back                                                  |
| TableSheet | selection | select-col-regex   | \|                       | select rows matching regex in current column                          |
| TableSheet | sheets    | dup-selected       | "                        | open a duplicate sheet with only the selected rows                    |
| TableSheet | selection | unselect-rows      | gu                       | unselect all rows                                                     |
| TableSheet | selection | select-rows        | gs                       | select all rows                                                       |
| TableSheet | selection | unselect-col-regex | \                        | unselect rows matching regex in current column                        |
| TableSheet | sheets    | type-float         | %                        | set type of current column to float                                   |
|            |           |                    | zY                       | yank (copy) current cell to system clipboard                          |
|            |           |                    | \<Space\>highlight-clear | clear the current highlight pattern                                   |
