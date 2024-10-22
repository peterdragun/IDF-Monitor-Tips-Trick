---
theme: seriph
title: IDF Monitor Tips & Tricks
info: demo
class: text-center
transition: fade
layout: intro
hideInToc: true
---

# IDF Monitor: Tips & Tricks

## Peter Dragúň

<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/espressif/esp-idf-monitor" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---
hideInToc: true
---

# Table of contents

<Toc minDepth="1" maxDepth="2"></Toc>

---

# IDF Monitor

- `idf.py monitor`, but also in Espressif IDE, and VS Code extension
- Also available as separate package by running: `python -m esp_idf_monitor`
- Repository: https://github.com/espressif/esp-idf-monitor
- Most of the features available for all methods

---

# Output Filtering

- `--print_filter=<tag>:<log_level>`
- log_level = `{N, E, W, I, D, V, *}`
- `N`: for suppressing selected tag
- e.g. to filter all errors and info with `tag1` but exclude `tag2` use: `--print_filter="*:E tag1:I tag2:N"`
- **Use case**: Filter out information that is important for you

---

# Menu Options

- CTRL + T; CTRL + H

```txt
--- esp_idf_monitor (1.5.0) - ESP-IDF Monitor tool
--- based on miniterm from pySerial
---
--- Ctrl+]   Exit program
--- Ctrl+T   Menu escape key, followed by:
--- Menu keys:
---    Ctrl+T         Send the menu character itself to remote
---    Ctrl+]         Send the exit character itself to remote
---    Ctrl+R         Reset target board via RTS line
---    Ctrl+F         Build & flash project
---    Ctrl+A (or A)  Build & flash app only
---    Ctrl+Y         Toggle output display
---    Ctrl+L         Toggle saving output into file
---    Ctrl+I (or I)  Toggle printing timestamps
---    Ctrl+P         Reset target into bootloader via the DTR/RTS lines
---    Ctrl+X (or X)  Exit program
```

- *Short demo* (reflash app and reset)

---

# Config File

- customize menu keys, reset sequence and delays
- `esp-idf-monitor.cfg`, `setup.cfg`, `tox.ini`
- current directory, `$HOME/.config/`, `$HOME/`

## Configurable Reset Sequences

- **Use case:** Special HW with requirement for longer delays
- example of custom reset sequence to bootloader

```toml
[esp-idf-monitor]
custom_reset_sequence = U0,1|W0.1|D1|R0|W0.5|D0
```

- **U**: DTR,RTS (Unix only) ; **W**: wait; **D**: DTR; **R**: RTS
- Option to share this configuration with Esptool - `setup.cfg` or `tox.ini`
- More details in the next slide and IDF Monitor README: https://github.com/espressif/esp-idf-monitor#custom-reset-sequence

---

# Configurable Shortcuts

- Most keyboard shortcuts are configurable!
- Option to skip pressing the menu key (CTRL + T)
- **Use case:** localized keyboards (issues with CTRL + ])
- See documentation at https://github.com/espressif/esp-idf-monitor#configuration-file

```toml
[esp-idf-monitor]
exit_key = W
menu_key = U
skip_menu_key = True
```

Output with config:

```txt
--- Using autodetected port /dev/cu.usbserial-11210
--- esp-idf-monitor 1.5.0 on /dev/cu.usbserial-11210 115200
--- Quit: Ctrl+W | Menu: Ctrl+U | Help: Ctrl+U followed by Ctrl+H
--- Loaded custom configuration from /Users/peterdragun/esp-idf-monitor.cf
```

---

# Decoding Functions

- address decoding using the `addr2line` tool from ELF file
- panic decoding `--decode-panic backtrace` (default package: off, IDF: on)
- coredump decoding `--decode-coredumps info` (`Core dump → Data destination → UART`)
- GDB - (`Component config → GDB Stub → GDBStub at runtime`) -> CTRL + C (not configurable)

<img src="/decode.png" width="350"/>

---

# Recent Features

- auto log colors on the host side (default behavior) - save data transfer from ESP to host
- multiple ELF file support for address and panic decoding (**only for package**) - decoding panic outside of the main app (in bootloader, LP core, ESP TEE - Trusted Execution Environment)
- option to connect to port that is not yet available - e.g. chip is connected using USB JTAG/Serial and currently in a deep sleep: `--open-port-attempts N` N:-1 for infinite loop (**only for package**)

---

# Future Plans

- support for multiple ELF files in ESP-IDF
- convert base64 log (binary mode) to text - further reduce the data transferred from ESP
- running monitor without attaching STDIN to TTY - e.g. monitor in CLion, Docker may not work properly without TTY
- configurable shortcut for GDB? - e.g. allow users to map CTRL + C to exit monitor

**Fell free to reach out to me if you have any ideas for improvements!**
