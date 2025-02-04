ENHANCED JUMPS
===============================================================================
_by Ingo Karkat_

DESCRIPTION
------------------------------------------------------------------------------

This plugin enhances the built-in CTRL-I|/|CTRL-O jump commands:
- After a jump, the line, column and text of the next jump target are printed:
```
    next: 3,9 ENHANCED JUMPS    by Ingo Karkat
```
- An error message and the valid range for jumps in that direction is printed
  if a jump outside the jump list is attempted:
```
    Only 8 older jump positions.
```
- In case the next jump would move to another buffer, only a warning is
  printed at the first attempt:
```
    next: EnhancedJumps.vim
```
  The jump to another buffer is only done if the same jump command is repeated
  once more immediately afterwards; like this: Pressing CTRL-O, noticing the
  warning, then quickly pressing CTRL-O again to overcome the warning.
  With this, you can eagerly jump around the current buffer. Because you will
  be warned when a jump would move to another buffer, you're much less likely
  to get lost.

In addition to the enhanced jump commands, separate special mappings are
available that restrict the jump targets to only local locations (in the same
buffer) and remote locations (only in other buffers).

This plugin enhances the built-in g;|/|g, change list jump commands so that
they skip jumps to previous / later edit locations that lie within the
currently visible range. This makes it easier to navigate between distant
edits in a large buffer, without having to manually skip over all the local
changes.

### RELATED WORKS

- back\_to\_recent\_buffer.vim ([vimscript #4290](http://www.vim.org/scripts/script.php?script_id=4290)) implements going back to the
  previous buffer.

USAGE
------------------------------------------------------------------------------

    Simply use the CTRL-O and CTRL-I commands to go to an older / newer cursor
    position in the jump list. When a warning "next: {file}" is echoed, quickly
    repeat the jump command to move to that buffer (a [count] need to not be
    typed again; if you do include the [count], it must be the same as before).
    If you do not want to move to that buffer, just ignore the warning, and
    continue browsing the current buffer. On the next jump attempt, the warning
    will be repeated.

    g<CTRL-O>, g<CTRL-I>    Go to [count] older / newer cursor position in the
                            current buffer. Jumps to other buffers are not
                            considered. Useful when you mainly edited one file,
                            briefly jumped to another, and now want to recall
                            older positions without considering the other file.

    <Leader><CTRL-O>, <Leader><CTRL-I>
                            Go to [count] older / newer cursor position in another
                            buffer. Jumps inside the current buffer are not
                            considered. Useful for recalling previously visited
                            buffers without going through all local positions.
                            Regardless of the jump direction, the last jump
                            position in a buffer is used when there are multiple
                            subsequent jumps in a buffer.

    <Leader><CTRL-W><CTRL-O>, <Leader><CTRL-W><CTRL-I>
                            Go to [count] older / newer cursor position in another
                            buffer. If the buffer is already visible in another
                            window / tab page (g:EnhancedJumps_UseTab), switch
                            to there instead of changing the buffer displayed in
                            the current window.

    Simply use the g; and g, commands to go to an older / newer far change
    position in the change list.

    g;                      Go to [count] older far change (that lies outside the
                            currently visible range and is more than the current
                            window height lines away from the previous change or
                            the last accepted change).
                            If [count] is larger than the number of older far
                            change positions go to the oldest far change.
                            If there is no older far change, go to the [count]
                            older near change; i.e. fall back to the original
                            behavior.
                            If there is no older change an error message is given.
                            (not a motion command)
    g,                      Go to [count] newer far change.
                            Just like g; but in the opposite direction.

INSTALLATION
------------------------------------------------------------------------------

The code is hosted in a Git repo at
    https://github.com/inkarkat/vim-EnhancedJumps
You can use your favorite plugin manager, or "git clone" into a directory used
for Vim packages. Releases are on the "stable" branch, the latest unstable
development snapshot on "master".

This script is also packaged as a vimball. If you have the "gunzip"
decompressor in your PATH, simply edit the \*.vmb.gz package in Vim; otherwise,
decompress the archive first, e.g. using WinZip. Inside Vim, install by
sourcing the vimball or via the :UseVimball command.

    vim EnhancedJumps*.vmb.gz
    :so %

To uninstall, use the :RmVimball command.

### DEPENDENCIES

- Requires Vim 7.0 or higher.
- Requires the ingo-library.vim plugin ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)), version 1.028 or
  higher.

CONFIGURATION
------------------------------------------------------------------------------

For a permanent configuration, put the following commands into your vimrc:

The time span in which the jump command must be repeated to overcome the
warning about jumping into another buffer defaults to (roughly) two seconds.
To change the timeout, set a different value (in milliseconds):

    let g:stopFirstAndNotifyTimeoutLen = 2000

Jumps to another buffer happen with "redir =&gt; l:fileJumpCapture". If other
plugins triggered by the (e.g. BufWinEnter) event do another :redir, this
causes an error, because nested redirs are prohibited. You can avoid this
problem by turning off the capture of jump messages:

    let g:EnhancedJumps_CaptureJumpMessages = 0

If you don't want the &lt;Leader&gt;CTRL\_W\_CTRL-O / &lt;Leader&gt;CTRL\_W\_CTRL-I
mappings to search other tab pages for windows containing the jump target:

    let g:EnhancedJumps_UseTab = 0

By default, the nearest window that contains the buffer with the jump target
is used. If you like to use the first buffer (like :sbuffer does), configure:

    let g:EnhancedJumps_SwitchStrategy = 'first'

If you want no or only a few of the available mappings, you can completely
turn off the creation of the default mappings by defining:

    :let g:EnhancedJumps_no_mappings = 1

This saves you from mapping dummy keys to all unwanted mapping targets.

If you do not want to override the built-in jump commands and use separate
mappings, or change the special additional mappings, map your keys to the
&lt;Plug&gt;... mapping targets _before_ sourcing the script (e.g. in your vimrc).

    nmap {          <Plug>EnhancedJumpsOlder
    nmap }          <Plug>EnhancedJumpsNewer
    nmap g{         <Plug>EnhancedJumpsLocalOlder
    nmap g}         <Plug>EnhancedJumpsLocalNewer
    nmap <Leader>{  <Plug>EnhancedJumpsRemoteOlder
    nmap <Leader>}  <Plug>EnhancedJumpsRemoteNewer
    nmap <C-w>{     <Plug>EnhancedJumpsSwitchRemoteOlder
    nmap <C-w>}     <Plug>EnhancedJumpsSwitchRemoteNewer

There are also mappings that do both local and remote jumps, the latter
potentially to another window / tab page, i.e. a combination of CTRL-O and
&lt;Leader&gt;CTRL-W\_CTRL-O. These are not mapped by default:

    nmap ,{         <Plug>EnhancedJumpsSwitchOlder
    nmap ,}         <Plug>EnhancedJumpsSwitchNewer

For the change list jump commands, you can choose between two alternatives,
the default one that falls back to near changes when there are no far changes

    nmap z; <Plug>EnhancedJumpsFarFallbackChangeOlder
    nmap z, <Plug>EnhancedJumpsFarFallbackChangeNewer

and a pure "far jumps" variant:

    nmap z; <Plug>EnhancedJumpsFarChangeOlder
    nmap z, <Plug>EnhancedJumpsFarChangeNewer

To disable the special additional mappings:

    nmap <Plug>DisableEnhancedJumpsLocalOlder  <Plug>EnhancedJumpsLocalOlder
    nmap <Plug>DisableEnhancedJumpsLocalNewer  <Plug>EnhancedJumpsLocalNewer
    nmap <Plug>DisableEnhancedJumpsRemoteOlder <Plug>EnhancedJumpsRemoteOlder
    nmap <Plug>DisableEnhancedJumpsRemoteNewer <Plug>EnhancedJumpsRemoteNewer

LIMITATIONS
------------------------------------------------------------------------------

- If the jump target is an empty line in another unnamed buffer, and the
  current buffer contains an empty line at the target line, the jump outside
  the current buffer is not detected, and no warning given.
- If the jump target is in another buffer, and the current buffer contains
  that buffer's filespec at the target line, the jump outside the current
  buffer is not detected, and no warning given. Same for empty buffers, viz.
  "[No Name]".

### CONTRIBUTING

Report any bugs, send patches, or suggest features via the issue tracker at
https://github.com/inkarkat/vim-EnhancedJumps/issues or email (address below).

HISTORY
------------------------------------------------------------------------------

##### 3.11    RELEASEME
- ENH: Allow to disable all default mappings via a single
  g:EnhancedJumps\_no\_mappings configuration flag. Thanks to infokiller for the
  patch.

##### 3.10    04-Nov-2018
- ENH: Add &lt;Leader&gt;&lt;C-w&gt;&lt;C-o&gt; / &lt;Leader&gt;&lt;C-w&gt;&lt;C-i&gt; mappings to jump to the
  target buffer in an existing window.
- Abort command sequence in case of jump errors.

__You need to update to ingo-library ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)) version 1.028!__

##### 3.03    18-Nov-2016
- After a jump to another file, also re-query the jumps, because the jumplist
  got updated with the text for the jumps, whereas it previously only
  contained the buffer name. Thanks to Daniel Hahler for sending a patch.
- Especially in small terminals, jump messages may not fit and cause a
  hit-enter prompt. Truncate messages in s:Echo(). Local jump message only
  considers the header, but not the file jump messages. If its one, and
  cmdheight is 1, add its width to the number of reserved columns, as we
  append the following location. Thanks to Daniel Hahler for the patch.
- The warning message before a remote jump isn't truncated to fit.
- Minor: Use ingo#compat#abs().

##### 3.02    29-Sep-2014
- Add g:EnhancedJumps\_CaptureJumpMessages configuration to turn off the
  capturing of the messages during the jump, as the used :redir may cause
  errors with another, concurrent capture. This was first reported by
  by Alexey Radkov on 06-Jul-2012 (conflict with the Recover.vim plugin that
  was fixed in that plugin), now again by Maxim Gonchar (conflict with
  Vimfiler plugin).
- Use ingo#msg#WarningMsg().
- Use ingo#record#Position().

__You need to update to ingo-library ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)) version 1.020!__

##### 3.01    19-Nov-2013
- Handle it when the :changes command sometimes outputs just the header
  without a following "&gt;" marker by catching the plugin exception in
  EnhancedJumps#Changes#GetJumps() and returning an empty List instead. This
  will cause the callers to fall back on the default g; / g, commands, which
  will then report the "E664: changelist is empty" error.
- Add dependency to ingo-library ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)).

__You need to separately
  install ingo-library ([vimscript #4433](http://www.vim.org/scripts/script.php?script_id=4433)) version 1.008 (or higher)!__

##### 3.00    09-Feb-2012
- Implement enhanced change list navigation commands g; / g, that skip jumps to
previous / later edit locations that lie within the currently visible range.

##### 2.00    20-Sep-2011
- Implement "local jumps" and "remote jumps" special mappings.
- Restructure internal jump list representation to get rid of index
  arithmetic, duplicated checks for current index in, and enhance filter
  performance.
- Split off functions to separate autoload script to help speed up Vim
  startup.

##### 1.13    16-Jul-2010
- BUG: Jump opened fold at current position when "No newer/older jump position"
error occurred.

##### 1.12    17-Jul-2009
- BF: Trailing space after the command to open the folds accidentally moved
cursor one position to the right of the jump target.

##### 1.11    14-Jul-2009
- BF: A '^\\)' in the jump text caused "E55: Unmatched \\)"

##### 1.10    06-Jul-2009
- ENH: To overcome the next buffer warning, a previously given [count] need
  not be specified again. A jump command with a different [count] than last
  time now is treated as a separate jump command and thus doesn't overcome the
  next buffer warning.
- BF: Folds at the jump target must be explicitly opened.

##### 1.00    01-Jul-2009
- First published version.

------------------------------------------------------------------------------
Copyright: (C) 2009-2019 Ingo Karkat -
The [VIM LICENSE](http://vimdoc.sourceforge.net/htmldoc/uganda.html#license) applies to this plugin.

Maintainer:     Ingo Karkat &lt;ingo@karkat.de&gt;
