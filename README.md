# Passmenu Plus
For most use cases, the standard passmenu included with [pass](https://www.passwordstore.org/),
serves well as a dmenu wrapper to interactively input passwords. However, my own usage over the years
has made me crave some features that passmenu itself didn't natively provide. `passmenu-plus` is supposed to
work as a replacement to the original passmenu, but makes some assumptions about the internal structure
of files in the pass tree.

## Additional dependencies (compared to vanilla pass)
* awk

## Fixes
* _Soft-linked sub-password-stores_: The original passmenu used shell globbing to autocomplete the
  path. This made soft-linking other password-stores into the main store (for example for shared team
  passwords) unfeasable, as some subdirectories couldn't be found by a shell glob if "hidden" behind
  a soft-link.

## Features
* _Userfields_: Most websites/services don't just need a password to login: they require a user identifier (eg an
  email address or customer id) to be input in a standard measure: first enter the identifier, press
  tab, then enter the actual password. `passmenu-plus` automates this process by adding support for
  userfields.

  This imposes some additional soft constraints on the file format. Any file format still works. However, if
  you want to make use of userfields, then all lines starting with a keyword or -phrase ended by a colon will 
  be individually callable:
  ```
  my super secret passphrase
  just some general explanation of stuff, this line isn't callable by userfield!
  user: exampleuser
  mail: user@example.org
  more general stuff amidst userfields.
  pin: 0000
  subdivision: Hackers
  this is a userfield with spaces: yay

  and even more text!
  ```
  Now, each of the userfields ending in a colon followed by a white space can be extracted on their own
  with the `-u/--userfield` option, eg `passmenu-plus -u user` or `passmenu-plus -u "this is a userfield with spaces"`.

  With just this option given, `passmenu-plus` acts as a wrapper around pass that outputs __only__ the 
  userfield to a shell (acting like the `pass --clip` option, but with human readable keys instead of line
  numbers).
* _Direct input of both a userfield and the password_: If `passmenu-plus` is called with both a userfield given
  and the `-t/--typeit` option specified, the userfield will be entered as keypresses via xdotool, then
  a tab, then the password from the first line. An example output for the password file given above called
  with `passmenu-plus -t -u mail` would be:
  ```
  user@example.org      my super secret passphrase
  ```
* _Direct input of just the userfield and copying the password to clipboard_: More and more websites use a two-tiered login
  page nowadays: First you put in your username, have to press enter and only after that the password input field
  appears. This is where the `-o/--only` option comes in. It will only type the given userfield and copy the password
  itself to the clipboard to be inserted manually once the password field is visible.

  For example, `passmenu-plus -tou user` would type `exampleuser` into the selected field directly, and have `my
  super secret passphrase` saved in the clipboard for 45 seconds.
* _Compatible to passmenu_: With just the `--typeit` option given, same as with the passmenu bundled with pass,
  `passmenu-plus` will just output the password, nothing else.
