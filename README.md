# X11/Xserver input mirroring utility

Captures mouse and keyboard events on one PC, then broadcasts them over the network. Then you can replay those events on other PCs. Thus, if they have the same screen resolution and keyboard mappings, you can effectively control multiple devices with GUI interface at once.

## [DEMO](https://youtu.be/HMadvD87JvE)

## Security

It is **highly** recommended to just tunnel the traffic over SSH/OpenVPN/WireGuard etc.

There is an over-engineered ugly built-in encryption though, which you set up in `SecurityConfig.toml`. Setting `password = ""` will disable it. Set it to a non-empty string (12 characters or more is required) to enable. If an attacker has been recording your traffic and gets your password, they will be able to decrypt all the traffic they recorded (no PFS). This program simply uses XChaCha20 with password as a key, and random initial nonce being sent in plaintext. Then message is sent twice, encrypted using different nonces, as a simple integrity check.

## Installation

Download [the latest release](https://github.com/pzmarzly/x11-input-mirror/releases).

```text
sudo apt install xdotool numlockx xinput
```

Optionally, install and use `screenkey` for debugging.

## Usage

Use `grabber` binary on master server, and `server` on slaves.

`grabber` requires `GrabberConfig.toml` and `SecurityConfig.toml`. `server` requires `ServerConfig.toml` and `SecurityConfig.toml`. Example files are in this repo.

If you want to use the program inside VirtualBox, you may need to disable Mouse Integration on slave servers.

## Compiling

Download Rust nightly toolchain, then run `cargo build --release`.

## X11 grabbing workaround

Since version 0.3, Chromium-based browsers work out-of-the-box, but you need to decrease `keyboard_and_clicks_interval_ms` (I recommend 1 ms now). If you need maximum performance, you can reverse commits "use slower method to grab clicks...", "update default grabber config values" and "remove unnecessary Xephyr stuff", and use Xephyr workaround for Chromium-based browsers.

## Misc

Licensed MIT.

Exposes many functions and can be also used as a library. But since the crate is not well documented, I recommend browsing the source before using it.

Uses synchronous networking code, so may be not suited well for controlling multiple machines over high-latency networks.

The code is pretty bad, but it works for now. Feel free to improve it.

Please keep in mind that devices can de-synchronize due to external factors (e.g. connectivity problems, VM having uneven resources, leading to program requiring more time to start, RNG). If you open YouTube in 2 browsers with no cookies, same User Agent, at the same time, from the same IP, you will probably get different order of videos.
