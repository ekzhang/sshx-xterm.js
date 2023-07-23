# Terminal Patches

The library has a few changes to support use by sshx.

## Patch 1: Remove support for data requests

We need to remove these because they trigger spurious messages when replayed.

This removes support for several commands, which is not great for full feature
support, but without the patch the requests cause problems because they cause
the terminal to send data before any user interactions, so the data is
duplicated with multiple connections.

Search the xterm.js source for calls to "triggerDataEvent" to understand why
these specific functions were patched.

I'm so sorry about this. In the future we should parse ANSI sequences correctly
on the server side and pass them through a state machine that filters such
status requests and replies to them exactly once, while being transparent to the
sshx client.

## Patch 2: Fix terminal when zoomed in/out

When resizing the terminal using CSS transformations, the measurements for font
and canvas size get messed up, causing rendering to be off. This patch fixes
that by using the `clientWidth` property, rather than the bounding box.

Also, click events don't get sent to the correct cell in the terminal window due
to math calculations using mouse client coordinates. We adjust those
calculations as well.
