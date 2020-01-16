# firefox

## white canvas during new tab loading
https://github.com/overdodactyl/ShadowFox

## hide tab bar
put into `userChrome.css`
`#TabsToolbar { visibility: collapse !important; }`

## change tab title font
put into `userChrome.css`
```
tabbrowser-tab .tab-label {
  font-weight: bold !important;
  font-size: 14px;
  color: #DCDCDC !important;
}
```

## change scroll bar color and appearance
### for normal pages:
```
@namespace url("http://www.w3.org/1999/xhtml");

:root{
  scrollbar-color: #0079D3 #2E3645 !important;
  scrollbar-width: thin !important;
}

* {
  scrollbar-width: thin !important;
}`
```
### for about pages:
```
@namespace url("http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul");
@-moz-document url-prefix("about:"){
  page,
  scrollbar{
    scrollbar-color: rgb(210,210,210) rgb(46,54,69);
    scrollbar-width: thin;
  }
}
```


## vim vixen
* Add complete property to control suggestions. This property allows users to customize suggestion items on :open, :tabopen, and :winopen commands. The property must be a character set including the following characters:

    s: search engines
    h: history
    b: bookmarks

	For example, the following command configures completion items as search engines and histories:

	set complete=sh

	This property is also configurable on add-on options.


* Focus first input in a page: "gi": { "type": "focus.input" }
* Open an URL from clipboard: "p": { "type": "urls.paste", "newTab": false }, open in new tab "P": { "type": "urls.paste", "newTab": true }
*  Blacklist support. The new setting "blacklist"is supported. You can now specify URLs to disable plugin by patterns. For instance, when you describe *.slack.com, the plugin are disabled on any Slack rooms. In addition, you can specify path patterns by concatenating a path or pattern, such as foo.example.com/mail/*. Disabled plugin can be made re-enabled by Shift+Esc (in default).

## scroll speed in firefox
### mousewheel
about:config -> **mousewheel.min_line_scroll_amount**

### arrow keys
about:config -> **toolkit.scrollbox.verticalScrollDistance**

### vixen
change value in keybindings in vixen's preferences

## Increase font size of bookmarks bar, tab titles, etc
```
#toolbar-menubar .menubar-text,
#toolbar-menubar menuitem,
#toolbar-menubar menu {
  font-size: 20px !important;
}

/* Tab bar */
#TabsToolbar {
  font-size: 20px !important;
}

/* Main Toolbar */
#nav-bar {
  font-size: 20px !important;
}

/* Bookmarks Toolbar */
#PersonalToolbar,
#PersonalToolbar menuitem,
#PersonalToolbar menu {
  font-size: 20px !important;
}

/* Context menus (this is not all of them) */
menupopup[id*="ContextMenu"] menuitem,
menupopup[id*="ContextMenu"] menu,
#placesContext menuitem,
#placesContext menu,
#backForwardMenu menuitem {
  font-size: 20px !important;
}
```

## increase font size of tab's tooltip
```
#btTooltip,
#un-toolbar-tooltip,
#tooltip,
.tooltip,
#aHTMLTooltip,
#urlTooltip,
tooltip
{
  font-size: 16px !important;
}
```
##  useful links

[CSS tricks for Firefox](https://github.com/Aris-t2/CustomCSSforFx)
