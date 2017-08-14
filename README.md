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

  This imposes some additional soft constraints on the file format, if you want to make use of userfields, 
  as files would have to follow the following format:
  ```
  my super secret passphrase
  user: exampleuser
  mail: user@example.org
  pin: 0000
  subdivision: Hackers
  ```
  Now, each of the userfields ending in a colon followed by a white space can be extracted on their own
  with the `-u/--userfield` option, eg `passmenu-plus -u user`.

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
* _Compatible to passmenu_: With just the `--typeit` option given, same as with the passmenu bundled with pass,
  `passmenu-plus` will just output the password, nothing else.
