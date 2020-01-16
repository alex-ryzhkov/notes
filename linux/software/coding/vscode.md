# vscode

## vim extension ingores swap escape
put `"keyboard.dispatch": "keyCode"` into settings

## relative numbers in editor
set `editor.lineNumbers` to `relative`

## fix vim key bindings in autosuggestion
put
```
// Place your key bindings in this file to override the defaults
[
{
  "key": "ctrl+j",
  "command": "selectNextSuggestion",
  "when": "suggestWidgetMultipleSuggestions && suggestWidgetVisible && textInputFocus"
},
{
  "key": "ctrl+k",
  "command": "selectPrevSuggestion",
  "when": "suggestWidgetMultipleSuggestions && suggestWidgetVisible && textInputFocus"
}
]
```
into `keybindings.json`
