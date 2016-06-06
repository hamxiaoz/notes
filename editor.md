## Mac
`ALT` to jump to word

---

## SublimeText
See preferences and used packages: https://github.com/hamxiaoz/my-scripts

Open with sublime icon in Finder toolbar: 
- export application, following [this guide](http://hohonuuli.blogspot.com/2013/07/open-filesfolder-selected-in-finder.html)
- hold down `Option+Command` and then start dragging the desired icon to the toolbar.
- You can use icons from [this theme](https://github.com/jamiewilson/predawn/tree/master/dock-icons)

Open current folder in command line, do this [setup](Open current folder in command line, do this [setup](x) and do `subl .`
) and do `subl .`

Theme: https://github.com/hamxiaoz/Monokai-Soda-hamxiaoz

### Shortcuts/Tips
#### core
http://www.cheatography.com/ducondez/cheat-sheets/core-sublimetext/

#### Pane Management
- `ALT+CMD+num`: open number of panes
- `CTRL+num`: switch to pane
- `CTRL+SHIT+num`: move tab to pane

#### Search
symbol search in ST3


### Packages
- vim ex mode: http://www.sublimetext.com/docs/2/vintage.html 
- git gutter: https://github.com/jisaacks/GitGutte
- sidebarenhancement

The following looks good but not tested
- https://github.com/vkocubinsky/SublimeTableEditor
- https://github.com/noklesta/SublimeQuickFileCreator
- https://github.com/twolfson/FindPlusPlus
- https://github.com/dzhibas/SublimePrettyJson

---

## Vim

Do this chanllege: https://www.shortcutfoo.com/app/dojos/vim

Cheatsheet:

- `H/M/L` Move to first/middle/last line of screen
- `:x` is same as `:wq`

---

## Editing Tips

### How to remove 1./2./3. for multiple lines?
Old way in vim: line by line.
- delete on 1st line then j0.
- or q to record to 1 then 10@1

New way in vim/ST: multiple line editing:
- select all lines
- split lines selection: `SHIFT+CMD+L`
- v to exit v mode then 0dw
