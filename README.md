# Windows-Storage-Troubleshooting
Investigation and resolution a Kernal PID 4 deadlock on an external SSD after High-I/O gaming sessions
# External SSD Kernel Lock Troubleshooting (PID 4)
**Operating Systems:** Windows 10/11 & Linux (Reference)

## The Problem
After high-I/O gaming sessions (Baldur's Gate 3), the Buffalo External SSD refused to eject. 
Windows reported "Device in Use," but no user-level applications were open.

## Investigation Steps
1. **Event Viewer:** Identified Event ID 225. PID 4 (System Kernel) was the vetoing entity.
2. **Handle Analysis:** Used `Handle64.exe` and `WMI` to attempt a dismount.
3. **Result:** Failed with ReturnValue: 4 (Access Denied), proving a Kernel-level deadlock.
4. **Time Test:** Waited 12+ hours; the deadlock did not resolve automatically.

## The Solution
Developed a CLI workaround using `diskpart` to force the volume offline, severing the Kernel handles safely.

## Implementation (Windows)
Create a shortcut with the following target to force an ejection:
`powershell.exe -Command "echo 'select disk 1', 'offline disk', 'online disk' | diskpart"`

## Linux Context
On Linux (Rocky/Fedora), this would be handled via `umount -f` or `udisksctl power-off`, which generally handles these deadlocks more gracefully than the Windows GUI.
