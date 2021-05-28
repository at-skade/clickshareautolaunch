# clickshareautolaunch

This an autolauncher to be paired with a preinstalled ClickShare app.

The autolaunch will run the in background an listen for USB events containing the name 'ClickShare'.

When a ClickShare device is plugged in it will automatically load the ClickShare app.

When a ClickShare is disconnected it will automatically kill the ClickShare app.


Code:

#!/bin/bash

function prntdbg {
  if [ ! -z "$DEBUG" ]; then
    echo "$@"
  fi
}

function open_app {
  open -a "ClickShare"
}

function close_app {
killall clickshare
}

function wait_for_plugin {
  while true; do
    if system_profiler SPUSBDataType | grep 'ClickShare' 2>/dev/null >/dev/null; then
      prntdbg clickshare plugged in
      open_app
      break
    else
      prntdbg clickshare removed
    fi
    sleep 2
  done
}

function wait_for_unplug {
  while true; do
    if system_profiler SPUSBDataType | grep 'ClickShare' 2>/dev/null >/dev/null; then
      prntdbg clickshare plugged in
    else
      prntdbg clickshare removed
      close_app
      break
    fi
    sleep 2
  done
}

while true; do
  wait_for_plugin
  wait_for_unplug
done		
