# CODEX TASK — Full-device VPN using xray-core (AndroidLibXrayLite-Z-NET)

## Goal
Make AndroidLibXrayLite-Z-NET work as a full-device VPN (TUN mode) using xray-core + tun2socks so that ALL apps work reliably (not only YouTube).
Reference behavior: v2rayNG-Z-NET in TUN/VPN mode.

Repos inside lab:
- deps/v2rayNG (reference)
- deps/AndroidLibXrayLite-Z-NET (target)

## Definition of Done (DoD)
1) In AndroidLibXrayLite-Z-NET, when VPN is enabled:
   - Full-device traffic goes through xray-core (TUN mode).
   - Works in mobile network and Wi-Fi.
   - Works for typical “hard cases”: YouTube video playback, Telegram/WhatsApp, browser, Play Store.
2) Stable start/stop:
   - No crashes on start/stop.
   - No leaks after stop (traffic returns to normal).
3) Proper foreground service:
   - On Android 10+ service is foreground with correct notification channel.
   - On Android 14+ comply with VPN foreground service requirements.
4) Logs:
   - Clean, readable logs for VPN lifecycle.
   - Tun interface up, tun2socks started, xray started, routing OK.

## Key Technical Requirements
### A) VPNService / TUN setup
Implement or fix the VPNService in target app:
- Configure Builder() correctly:
  - addAddress (IPv4 at minimum, IPv6 optional)
  - addRoute 0.0.0.0/0 (full tunnel) and ::/0 if IPv6 supported
  - setMtu (start with 1400; allow user override or adaptive fallback)
  - addDnsServer (ensure DNS goes through tunnel; avoid DNS leaks)
  - setSession / setBlocking if needed
- Implement "protect()" for sockets used by:
  - xray-core control channel (if any)
  - tun2socks connection to local socks/redirect ports
  to avoid routing loops.

### B) tun2socks integration
Ensure tun2socks is started with correct parameters:
- TUN fd from VPNService is passed correctly.
- UDP handling is correct (YouTube/QUIC should not break; if UDP is disabled, ensure fallback strategy exists).
- Ensure lifecycle:
  - Start order is correct and race-free.
  - Stop order is correct: stop xray, stop tun2socks, close tun fd, stop foreground.

### C) xray-core integration
In target project:
- Ensure xray-core binary/so is packaged correctly for required ABIs.
- Ensure config generation is correct:
  - inbound/outbound mapping for tun2socks (SOCKS/REDIR ports).
  - DNS settings that work on Android (avoid blocking private DNS scenarios).
  - routing rules for full tunnel; optional bypass for local networks.
- Ensure permissions and file locations are correct:
  - assets extraction (geoip.dat/geosite.dat if used)
  - config file path and access rights

### D) Compatibility & robustness
- Handle Android power management:
  - keep service alive properly, avoid aggressive killing.
- Handle private DNS:
  - Document behavior, implement safer defaults.
- Implement MTU fallback:
  - If connectivity fails, attempt MTU 1280 as fallback (optional).
- Handle IPv6:
  - Either support properly or disable IPv6 routes explicitly (documented).

## Work Plan (Codex must do)
1) Identify reference implementation in deps/v2rayNG:
   - Find VPNService class and its Builder() configuration
   - Find tun2socks start/stop code and parameters
   - Find protect() usage and rationale
   - Note MTU and DNS strategy
2) Identify target implementation in deps/AndroidLibXrayLite-Z-NET:
   - Current VPNService, TUN builder config, tun2socks integration, xray start/stop
3) Produce a diff-style implementation:
   - Modify target project to match working strategies from reference
   - Keep code clean: add comments, remove dead code, ensure thread safety
4) Add/Improve logging:
   - log important lifecycle states with timestamps
5) Provide outputs:
   - List of changed files
   - Explanation for each change (1–2 sentences)
   - How to build & run
   - How to test (steps)

## Test Checklist
- Start VPN → open browser and load a site
- YouTube app: play video (not just thumbnails)
- Telegram / any messenger sends/receives
- Switch Wi-Fi ↔ mobile network while connected
- Stop VPN → traffic returns to normal; no “half-connected” state
- Reconnect VPN 3 times подряд без крашей

## Notes / Constraints
- This is Windows dev environment; project should build in Android Studio on Windows.
- Use v2rayNG behavior as reference; do not require extra external services.
