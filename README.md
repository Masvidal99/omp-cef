# omp-cef

![Version](https://img.shields.io/github/v/release/aurora-mp/omp-cef?include_prereleases&label=version)
![License](https://img.shields.io/github/license/aurora-mp/omp-cef)
[![Wiki](https://img.shields.io/badge/docs-wiki-blue)](https://github.com/aurora-mp/omp-cef/wiki)
![CEF](https://img.shields.io/badge/CEF-148.0.10-blue)
![SA--MP](https://img.shields.io/badge/SA--MP-0.3.7%20%7C%200.3.DL-orange)
![open.mp](https://img.shields.io/badge/open.mp-supported-brightgreen)

Client/server CEF plugin for **open.mp** and **SA-MP**.

`omp-cef` embeds Chromium Embedded Framework into GTA San Andreas Multiplayer clients and allows servers to create browser-based UI in several rendering modes: Overlay2D, World2D and WorldObject3D.

## Features

- Off-screen CEF rendering inside GTA SA
- Overlay2D browser rendering for HUDs and menus
- Browser layers to control which 2D interfaces appear on top
- World2D browser rendering at world positions
- WorldObject3D browser rendering through object texture replacement
- Packaged server resources served through `http://cef/...`
- Pawn natives and callbacks
- JavaScript <-> Pawn/C# event bridge
- Focus, cursor, input and audio controls
- Custom ESC menu support
- Custom TAB menu (scoreboard) support
- Secure client/server handshake and UDP transport

## Documentation

The full documentation is available in the GitHub Wiki:

https://github.com/aurora-mp/omp-cef/wiki

## Supported clients

- SA-MP 0.3.7-R1
- SA-MP 0.3.7-R3-1
- SA-MP 0.3.7-R5-1
- SA-MP 0.3.DL-R1

## Current CEF version

```txt
148.0.10+g7ee53f5+chromium-148.0.7778.218
```

## Repository layout

```txt
src/
  client/          # client plugin (CEF offscreen + DX9 + SA:MP hooks)
  server/
    common/        # network core, sessions, resources, protocol
    omp/           # open.mp component (bridge + lifecycle + natives)
    samp/          # SA-MP plugin (bridge + lifecycle + natives)
   shared          # shared protocol/types (packets, serialization, common utilities)
deps/              # deps (minhook, sdk, etc.)
vcpkg.json         # vcpkg manifest (if using manifest mode)
CMakeLists.txt
CMakePresets.json  # CMake presets (configure/build presets for VS/CMake)
```

## Building

Build requirements and detailed setup instructions are documented in the wiki:

https://github.com/aurora-mp/omp-cef/wiki

Short version:

- Visual Studio 2022
- CMake
- vcpkg
- Windows SDK
- x86 target for GTA SA / SA-MP client-side

## Samples

- Overlay2D HUD
- World2D browser
- WorldObject3D browser
- JavaScript <-> Pawn events
- Browser lifecycle
- Hidden browser navigation
- Stress testing
## Browser layers

Use `CEF_SetBrowserLayer` to order Overlay2D and World2D browsers. Higher layers
are drawn on top. Browsers start at layer `0`; negative layers are supported.
When layers match, the higher browser ID is drawn on top.

```pawn
CEF_CreateBrowser(playerid, 10, "http://cef/inventory/index.html", true);
CEF_SetBrowserLayer(playerid, 10, 100);

CEF_CreateBrowser(playerid, 20, "http://cef/notifications/index.html", false);
CEF_SetBrowserLayer(playerid, 20, 200);
```

The notification page should have a transparent background so the inventory
remains visible. Layer changes do not change focus or visibility: in this
example, the inventory keeps receiving mouse and keyboard input. Use
`CEF_FocusBrowser` separately when an upper interface needs input.

Layers can change at runtime and persist across hide/show, reload and navigation.
Destroying and recreating a browser resets its layer to `0`. Setting the layer
immediately after creation also works while resources are downloading.
WorldObject3D browsers follow the game's object rendering and do not support layers.

Update both client and server to a build with this feature and use the matching
`cef.inc`. C++ open.mp components can call `ICefComponent::setBrowserLayer` with
the updated component. Try `/ceflayers` in the demo to see two overlapping pages.

## Contributing

Pull requests are welcome <3.

## License

See the repository license.
