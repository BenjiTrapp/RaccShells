<p align="center">
  <img src="static/raccshell_transparent.png" alt="RaccShells Logo" height="180"/>
</p>

<h1 align="center">RaccShells</h1>
<p align="center">
  <strong>Self-contained reverse shell generator · zero dependencies · single HTML file</strong><br/>
  <sub>For authorised penetration testing and CTF use only</sub>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/shells-111-00ff00?style=flat-square&labelColor=0a0a0a&color=00cc00"/>
  <img src="https://img.shields.io/badge/platform-any%20browser-00ff00?style=flat-square&labelColor=0a0a0a&color=00cc00"/>
  <img src="https://img.shields.io/badge/dependencies-zero-00ff00?style=flat-square&labelColor=0a0a0a&color=00cc00"/>
  <img src="https://img.shields.io/badge/license-MIT-00ff00?style=flat-square&labelColor=0a0a0a&color=00cc00"/>
</p>

---

## What is it?

RaccShells is a **self-contained, single-file** reverse shell reference tool. Open `index.html` — no server, no install, no internet required. Enter your IP and port, copy the shell, and go.

Built with a dark terminal aesthetic: scanline overlay, matrix green, monospace everything.

---

## Feature Overview

<p align="center">
  <img src="static/mockup.svg" alt="RaccShells UI Mockup" width="100%"/>
</p>

| Tab | Contents |
|-----|----------|
| **Reverse Shells** | 72 shells across Bash, Python, Perl, PHP, Ruby, PowerShell, Java, Go, Lua, Awk, … |
| **Bind Shells** | 14 bind-side listeners |
| **MSFVenom** | 25 ready-to-paste msfvenom payloads |
| **Shell Upgrade** | TTY spawn, stty raw, socat fully interactive, escape binaries |
| **Tools & Listeners** | pwncat-cs, pwncat (cytopia), Chashell, HellShell, SSL-AES, FuegoShell, CHAOS RAT, tmate |
| **Living off the Land** | Windows (LOLBAS) + Linux + macOS — download, exec, persistence, tunnel |

---

## Traffic Flow Diagrams

Every shell shows an animated SVG diagram when expanded — no static images, generated inline.

```mermaid
flowchart LR
    subgraph VICTIM
        V["💻 :random"]
    end
    subgraph ATTACKER
        A["🖥 :4444 LISTEN"]
    end

    V -- "① TCP connect-back" --> A
    A -. "② commands" .-> V
    V -. "③ output" .-> A
```

```mermaid
flowchart LR
    subgraph ATTACKER
        A["🖥 connects"]
    end
    subgraph VICTIM
        V["💻 :4444 LISTEN"]
    end

    A -- "① connect to port" --> V
    A -. "② commands" .-> V
    V -. "③ output" .-> A
```

```mermaid
flowchart LR
    subgraph VICTIM
        V["💻 DNS client"]
    end
    subgraph DNS["DNS Nameserver\nyour domain NS"]
        D[" "]
    end
    subgraph ATTACKER
        A["🖥 NS record"]
    end

    V -- "① DNS query\n(XSalsa20 enc)" --> D
    D -- "NS forward" --> A
    A -. "② encoded response" .-> D
    D -. "NS reply" .-> V
```

```mermaid
flowchart LR
    subgraph VICTIM
        V["💻 polling"]
    end
    subgraph ATTACKER
        A["🖥 HTTP :80"]
    end

    V -- "① GET /token\npoll for cmd" --> A
    A -. "② 200 encoded cmd" .-> V
    V -- "③ POST /token\nencoded output" --> A
```

```mermaid
flowchart LR
    subgraph VICTIM
        V["💻 :random"]
    end
    subgraph ATTACKER
        A["🖥 :443 LISTEN"]
    end

    V -- "① TLS handshake" --> A
    A -- "② 🔒 encrypted cmd" --> V
    V -- "③ 🔒 encrypted output" --> A

    style A fill:#1a1200,stroke:#cc9900
    style V fill:#1a0000,stroke:#ff4444
```

---

## Shell Obfuscation

Each shell that contains an interpreter supports **dynamic obfuscation** — click `▶ obf` to cycle modes without leaving the page.

```mermaid
stateDiagram-v2
    [*] --> plain
    plain --> base64 : click obf
    base64 --> var_split : click obf
    var_split --> hex_printf : click obf
    hex_printf --> plain : click obf

    state "bash base64" as base64
    note right of base64
        bash -c "$(echo '...'|base64 -d)"
    end note

    state "$var split" as var_split
    note right of var_split
        b='ba';s='sh'; eval $b$s${IFS}-i...
    end note

    state "hex printf" as hex_printf
    note right of hex_printf
        eval "$(printf '\x62\x61\x73\x68...')"
    end note
```

**Obfuscation modes per interpreter:**

| Interpreter | Modes |
|------------|-------|
| Bash / Zsh / Sh | `base64` · `$var split` · `hex printf` |
| PowerShell | `-EncodedCommand` (UTF-16LE) · `[char[]]` IEX |
| Python | `exec(base64.b64decode(...))` |
| Perl | `eval(pack('H*', hex))` |
| PHP | `eval(base64_decode(...))` |
| Ruby | `eval(Base64.decode64(...))` |

---

## Encrypted Shells

Shells with encrypted transport are highlighted in **gold** and carry a 🔒 badge.

```mermaid
flowchart TD
    subgraph Encrypted["🔒 Encrypted Transport"]
        direction LR
        E1["OpenSSL s_client"]
        E2["PowerShell TLS #4"]
        E3["Ncat --ssl"]
        E4["HoaxShell HTTPS"]
        E5["SSL-AES C++ Shell"]
        E6["Chashell DNS (XSalsa20)"]
    end
```

Use the **🔒 encrypted** filter chip to show only these shells.

---

## Living off the Land

Techniques organised by OS, each with its own filter view.

```mermaid
mindmap
  root((LotL))
    Windows
      Download & Execute
        certutil
        bitsadmin
        curl Win10+
        PowerShell cradle
      Code Execution
        mshta HTA
        regsvr32 Squiblydoo
        rundll32 JS
        MSBuild C#
        InstallUtil
        wmic
      Persistence
        Registry Run key
        Scheduled Task
        Startup folder
    Linux
      Download & Execute
        curl pipe bash
        wget pipe bash
        python3 fetch
      File Transfer
        nc / socat
        dd raw
        base64 paste
      Tunneling
        SSH reverse tunnel
        SSH SOCKS5
        Chisel
      Persistence
        crontab
        systemd user service
        LD_PRELOAD
    macOS
      Download & Execute
        curl pipe bash
        osascript
      Persistence
        LaunchAgent plist
        crontab
```

---

## Tools & Listeners

| Tool | Type | Platform |
|------|------|----------|
| [pwncat-cs](https://github.com/calebstewart/pwncat) | Post-exploitation platform | Linux |
| [pwncat (cytopia)](https://github.com/cytopia/pwncat) | Netcat on steroids | Linux / Mac / Win |
| [Chashell](https://github.com/kost/chashell) | DNS reverse shell | All |
| [HellShell](https://github.com/NUL0x4C/HellShell) | Shellcode obfuscator | Windows |
| [SSL-AES Reverse Shell](https://github.com/V-i-x-x/SSL-AES-Reverse-Shell) | TLS C++ shell | Windows |
| [FuegoShell](https://github.com/v1k1ngfr/fuegoshell) | SMB named-pipe shell | Windows |
| [CHAOS RAT](https://github.com/tiagorlampert/CHAOS) | Go RAT with web UI | Linux / Win |
| [tmate](https://github.com/tmate-io/tmate) | Terminal sharing via SSH | Linux / Mac |

---

## Usage

```bash
# Option 1 — open directly in browser (no server needed)
open index.html   # macOS
start index.html  # Windows
xdg-open index.html  # Linux

# Option 2 — serve locally
python3 -m http.server 8080
# → http://localhost:8080
```

1. Set **LHOST / IP**, **PORT**, and preferred **shell binary**
2. Pick a **listener** from the dropdown — the attacker-side command auto-updates
3. Use the **filter chips** to narrow by OS, encryption, or obfuscation support
4. Click a shell to expand — diagram animates, copy button is ready
5. Optionally click **▶ obf** to cycle through obfuscation modes before copying

---

## Listener Dropdown

```mermaid
flowchart LR
    L["Listener Dropdown"] --> NC["nc -lvnp PORT"]
    L --> NCAT["ncat -lvnp PORT"]
    L --> SOCAT["socat file:tty,raw TCP-LISTEN:PORT"]
    L --> RLWRAP["rlwrap nc -lvnp PORT  (TTY)"]
    L --> MSF["msf multi/handler"]
    L --> PCS["pwncat-cs -lp PORT"]
    L --> PCY["pwncat -l -p PORT"]
```

