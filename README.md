**SkyLight-VM-Patcher** is a software-based patcher designed for macOS virtual machines (running on VMware or VirtualBox) to force-enable advanced graphical effects that are normally disabled due to the lack of hardware graphics acceleration (QE/CI).

## Features

- **Force Dock Blur:** Hooks into `SkyLight.framework` to force dock blur and window transparency effects via optimized CPU software rendering.
- **Tearing Reduction:** Implements frame-rate limiting and basic double-buffering simulation to minimize screen tearing caused by the lack of native V-Sync inside the VM.
- **CPU Optimization:** Utilizes dirty-region (clip-rect) rendering logic to ensure core-image filters only redraw updated pixels, heavily reducing processing overhead.

## How It Works

The patcher injects hooks or patches into the core window server library (`SkyLight.framework`) and force-routes Core Image filter calls through `AppleSoftwareRenderer.bundle`. This bypasses native Metal/OpenGL hardware requirements, shifting the heavy rendering pipelines to optimized CPU threads.

## Requirements

- **Guest OS:** macOS Ventura, Sonoma, or Sequoia (running inside a Virtual Machine)
- **Host OS:** Windows 10 / 11
- **Security:** System Integrity Protection (SIP) must be fully disabled inside the virtual machine for the patcher to hook system binaries.

## License

This project is licensed under the **MIT License**. See the `LICENSE` file for more details.
