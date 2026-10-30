# MacVM-BlurEnabler

A small SwiftUI app that tries to bring back Dock blur in macOS virtual machines that have no GPU acceleration. It injects a hook library into the Dock process, forces a fixed blur radius, and caps the blur update rate (30 or 60 FPS) to reduce tearing and CPU load.

> **Experimental. Use only inside a virtual machine, never on your main Mac.** It requires System Integrity Protection (SIP) to be disabled.

## How it works

- `Sources/vmfx.m` builds `libvmfx.dylib`, which interposes SkyLight's `SLSSetWindowBackgroundBlurRadius`.
  - Every non-zero blur request is replaced with a fixed radius (8 or 12).
  - Updates are coalesced per window to the target frame rate, which simulates double buffering.
- The app copies the dylib to `/Users/Shared/MacVM-BlurEnabler/`, sets `DYLD_INSERT_LIBRARIES` through `launchctl setenv`, and restarts Dock. Restoring unsets the variables and restarts Dock again.
- On launch the app runs `csrutil status` and shows the SIP state. After enabling, it checks whether the library is actually mapped into Dock.

## Limitations

This is **not** GPU emulation. The hook only changes the blur requests Dock sends to SkyLight. If the VM's WindowServer does not render blur without Metal, you may see no visible change. Results vary by hypervisor (VMware, VirtualBox, Parallels, UTM) and by macOS version.

## Requirements

- A macOS virtual machine, Ventura or later
- SIP disabled inside the VM: boot into Recovery and run `csrutil disable`
- Depending on the version, Dock may reject the ad-hoc signed library. If the app reports that the hook is not loaded, try:

  ```bash
  sudo nvram boot-args="amfi_get_out_of_my_way=1"
  ```

  then reboot the VM.

## Install

1. Download `MacVM-BlurEnabler.dmg` from the Releases page and drag the app to Applications.
2. The app is not notarized. Remove the quarantine flag:

   ```bash
   xattr -cr /Applications/MacVM-BlurEnabler.app
   ```

3. Open the app and click **Enable Dock Blur & Fix Tearing**.

## Uninstall

Click **Restore to Default** in the app, then re-enable SIP from Recovery with `csrutil enable`.

## Build from source

Requires Xcode or the Command Line Tools.

```bash
./build.sh      # builds build/MacVM-BlurEnabler.app (universal)
./make_dmg.sh   # builds build/MacVM-BlurEnabler.dmg
```

## Disclaimer

Provided as is, with no warranty. Disabling SIP lowers your system's security. Use it only in a virtual machine you are comfortable experimenting with.
