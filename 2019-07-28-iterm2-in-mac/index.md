# Iterm2 in MAC


Well, it's been a long time that not posting things... and I know the excuse is very easy to find... So I have to admit I was lazy...

Got a new Macbook from Apple (after repair), same as usual, install brew, then bulk install all applications I need... 

Uhm, wait, my iterm2 is not working as I expected... all shortcuts are not working at all! The last time I configured the shortcuts was looooong time ago, and I forgot to record down everything...

OK, learnt this time so I decided to put something here to remind myselft...

Open the iTerm preferences `⌘`+`,` and navigate to the Profiles tab (the Keys tab can be used, but adding keybinding to your profile allows you to save your profile and sync it to multiple computers) and keys sub-tab and enter the following:

# Delete all characters left of the cursor
`⌘+←Delete` Send Hex Codes:
  * `0x18 0x7f` – Less compatible, doesn't work in node and won't work in zsh by default, see below to fix zsh (bash/irb/pry should be fine), performs desired functionality when it does work.
  * `0x15` – More compatible, but typical functionality is to delete the entire line rather than just the characters to the left of the cursor.

# Delete all characters right of the cursor
`⌘+fn+←Delete` or `⌘+Delete→` Send Hex Codes:
  * `0x0b`

# Delete one word to left of cursor
`⌥+←Delete` Send Hex Codes:
  * `0x01b 0x08`

# Delete one word to right of cursor
`⌥+fn←Delete` or `⌥+Delete→` Send Hex Codes: 
  * `0x01b 0x64`

# Move cursor to the front of line
`⌘+←` Send Hex Codes:
  * `0x01`

# Move cursor to the end of line
`⌘+→` Send Hex Codes:
  * `0x05`

# Move cursor one word left
`⌥+←` Send Hex Codes:
  * `0x1b 0x62`

# Move cursor one word right
`⌥+→` Send Hex Codes:
  * `0x1b 0x66`

# Undo
`⌘+z` Send Hex Codes:
  * `0x1f`

# Redo
Typically not bound in bash, zsh or readline, so we can set it to a unused hexcode which we can then fix in zsh.

`⇧+⌘+Z` or `⌘+y` Send Hex Codes:
  * `0x18 0x1f`

_Stolen from: http://stackoverflow.com/questions/6205157/iterm2-how-to-get-jump-to-beginning-end-of-line-in-bash-shell#answer-29403520_

Make sure you backup your own profile well... 



