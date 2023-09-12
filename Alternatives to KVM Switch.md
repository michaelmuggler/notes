Keyboard, Video and Mouse (KVM) Switch allows a user to switch between multiple PCs via a single keyboard, monitor and mouse.

Sub-$100 KVMs commonly found on online retailers (Amazon, NewEgg, AliExpress, etc.) are typically subpar quality and not electrically safe.

Further, if you need a KVM which can support 4K 120Hz HDR (HDMI 2.1 or DP 1.4a) the price increases significantly. Reputable brands like Belkin and IOGEAR are quoting between $200-$2000.

## Alternative 1: Software-based

Most computer monitors have multiple inputs and support the [Display Data Channel (DDC) / Command Interface (CI) standard](https://en.wikipedia.org/wiki/Display_Data_Channel). DDC commands are sent over a dedicated serial bus cables built into DisplayPort, HDMI and VGA cables. Typically monitors will accept DDC/CI from any PC connected to it even if it is not currently showing the video signal from that PC.

Using software commands can be sent to the monitor to change input. Linux users should use  [`ddcutil`](https://www.ddcutil.com/). Mac OS users should use [Lunar](https://lunar.fyi/). And, Windows users should use [NirSoft ControlMyMonitor](https://www.nirsoft.net/utils/control_my_monitor.html) . All of these programs can be interfaced via the command line which is useful for invoking from other software.

>[!info]
>An alterative to Lunar on MacOS is [MonitorControl](https://github.com/MonitorControl/MonitorControl) but at the time of writing [does not have a CLI](https://github.com/MonitorControl/MonitorControl/issues/392).

Combine the DDC/CI software with a USB tool like the Elgato Stream Deck one effectively has a KVM. 

> [!note]
> DDC/CI can also control the monitors brightness and volume (if inbuilt speakers).
> Windows users might consider both [Monitorian](https://apps.microsoft.com/store/detail/monitorian/9NW33J738BL0) or  [Twinkle Tray](https://twinkletray.com/)  which provide a UI in the Windows Taskbar System Tray.

Below is an example of using `ControlMyMonitor.exe` to change the current input on a Dell UltraSharp display:

```
ControlMyMonitor.exe /SetValue "\\.\DISPLAY1\Monitor0" 60 15
```

In this case the VCP Code for Input select is `60` and the valid values are `0`, `15`, `17`, and `18` which correspond to Auto, DP, HDMI 1 and HDMI 2. Launching the ControlMyMonitor a GUI will show the Monitor Device Name, VCP Code and valid values.


## Alternative 2: USB Switching Hub

A USB Switching Hub allows users to share USB peripherals between one or more computers (not at the same time). It typically has a button and/or remote to switch between PC's.

USB devices can be identified by a "Product ID" and "Vendor ID". Most operating systems contain facilities to detect and act when a USB device is connected. On Linux this can be done with [`udev`](https://unix.stackexchange.com/a/65892). On Windows this can be done with [Windows Event Logs](https://www.techrepublic.com/article/how-to-track-down-usb-flash-drive-usage-in-windows-10s-event-viewer/) or [Windows Management Instrumentation (WMI)](https://stackoverflow.com/questions/21805166/start-powershell-script-when-usb-drive-is-inserted). On MacOS this can be done with [`launchd`](https://stackoverflow.com/a/12259762).

I've found a Rust-based open-source library called [`display-switch`](https://github.com/haimgel/display-switch) which works cross-platform and can do this without the need to configure different OS-level facilities.
