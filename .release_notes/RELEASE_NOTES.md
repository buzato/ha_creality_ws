# Changelog

All notable changes to HA Creality WS will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).


## [0.9.1] - 2026-01-24
> [List of issues (0.9.1)](https://github.com/3dg1luk43/ha_creality_ws/issues?q=is%3Aissue+milestone%3Av0.9.1

### Added
- **Manual Reconnect Button**: Added a new `button` entity (`button.*_reconnect`) to force a WebSocket reconnection if the printer becomes unresponsive.
- **Service Targeting**: Added `device_id` selector to `request_cfs_info`, allowing users to target specific printers instead of all connected devices.
- **Service Feedback**: Added persistent notifications to `request_cfs_info` to confirm success/failure counts.

### Fixed
- **Startup Robustness (Smart & Simple)**: Refactored the entire startup architecture.
  - Integration explicitly waits for `boxsInfo` (CFS) and chamber temps during setup, ensuring 100% entity coverage at booting.
  - Implemented a "hybrid" safety net: `sensor.py` retains a thread-safe dynamic loader to catch any entities that arrive late, preventing "Duplicate ID" errors.
- **Chamber Control**: Fixed missing "Chamber Target" entity for K2 Pro/Plus by auto-enabling control if the printer reports a target temperature, regardless of model detection defaults.
- **WebRTC Regression**: Fixed camera initialization failure when custom go2rtc settings were unreachable; added automatic fallback to discovery.
- **Service Stability**: Fixed crash in `request_cfs_info` when printer disconnected.

## [0.9.0] - 2026-01-23
> [List of issues (0.9.0)](https://github.com/3dg1luk43/ha_creality_ws/issues?q=is%3Aissue+milestone%3Av0.9.0

### Added
- **CFS Support (Creality Filament System)** (@buzato):
  - **Comprehensive Sensors**: Added sensors for each CFS box (temperature, humidity) and slot (filament type, color, percentage, active status).
  - **Native UI Card**: Introduced the **Creality CFS Card** with a built-in visual editor.
    - Renders tiles for all slots (up to 4 boxes x 4 slots) + external filament.
    - Dynamic UI: Active filament pulses, humidity color coding (Green/Orange/Red).
    - No YAML required: Fully configurable via entity mapping in the UI.
  - **New Services**: Added `request_cfs_info` (manual refresh), `cfs_load`, and `cfs_unload` for programmatic filament management.
- **Safety Features**:
  - **Confirmation Dialog**: Added a "double-check" modal for destructive actions like "Stop Print" to prevent accidental cancellations.

### Fixed
- **K2 Base Compatibility** (@PavelStoyan0v):
  - **Chamber Control**: Fixed chamber temperature control by implementing a Moonraker fallback for fetching accurate targets when the primary method fails.
  - **Data Accuracy**: Suppressed erroneous `targetBoxTemp:0` values.
  - **Threshold Removal**: Removed the hardcoded 40°C threshold for chamber heating, allowing for more flexible control.
- **go2rtc Custom Configuration**: Fixed an issue where custom go2rtc URL and Port settings were ignored.
- **Coordinator & Stability**: 
  - Refactored the central data coordinator for efficient high-frequency WebSocket updates.
  - Resolved merge conflicts and sync issues for reliable state tracking.
- **Frontend Assets**: Improved resource loading and fixed loading issues for custom card resources.

## [0.8.0] - 2026-01-05
> [List of issues (0.8.0)](https://github.com/3dg1luk43/ha_creality_ws/issues?q=is%3Aissue+milestone%3Av0.8.0

### Added
- **Diagnostics Service**: Enhanced `diagnostic_dump` service to include WebSocket connection health stats (`reconnect_count`, `msg_count`, `last_error`, `uptime`).
- **Notifications**: Added configurable notifications for print completion, errors, and time remaining (configurable via Options Flow).
- **Chamber Control for K2**: Enabled chamber temperature control for the base "K2" model.
- **Polling Rate**: New option to configure polling rate to reduce CPU usage. Throttling only applies **when the printer is actively printing**; idle/error states update immediately.
- **Translations**: Added `strings.json` and `en.json` for localization support.
- **Device Class**: Added `duration` device class to "Print Job Time" and "Print Time Left" sensors.

### Changed
- **Unavailable State**: Entities now report as `unavailable` when the printer is known to be powered off via the configured switch (static model info remains available).
- **Documentation**: Updated README to reflect K2 chamber support, K1C 2025 camera capabilities, and power switch configuration.

### Fixed
- **Connection Stability**: Slightly improved liveness detection and retry behavior.
  - Power-off check interval reduced to 10s (was 60s) for faster power-on detection.
  - Non-power-switch users utilize gradual backoff for initial failures (up to 5 attempts), transitioning to a fixed 60s retry mechanism for long-term idle detection.
  - Added application-level probes to detect and recover from stale WebSocket connections.
- **Log Noise**: Connection warnings are now limited to the first 3 failures; subsequent failures are logged as debug only to prevent spam when the printer is intentionally off.

## [0.7.1] - 2026-01-04
> [List of issues (0.7.1)](https://github.com/3dg1luk43/ha_creality_ws/issues?q=is%3Aissue+milestone%3Av0.7.1

### Added
- **Zeroconf**: Added improved Zeroconf discovery signatures for K2 and K1 series printers.

### Fixed
- Minor bug fixes and performance improvements.

## [0.7.0] - 2025-12-19
> [List of issues (0.7.0)](https://github.com/3dg1luk43/ha_creality_ws/issues?q=is%3Aissue+milestone%3Av0.7.0

### Added
- **Robust Network Management**: MAC-based discovery to automatically handle IP changes from DHCP reassignments.
- **Enhanced WebRTC Camera**: Uses official `go2rtc-client` Python library for robust stream configuration.
- **Intelligent Power-Off Detection**: Pauses connection attempts when printer power is OFF and auto-resets backoff on power return.
- **Card Customization**: New custom button targeting any entity type, with custom MDI icons for all buttons.
- **Domain Support**: Power & light controls now support `input_boolean` and `light` domains.

### Fixed
- Fixed `UnboundLocalError` in WebSocket reconnection timing logic.
- Improved `go2rtc` client error handling with descriptive messages.
- Refactored card event handling using event delegation.
- Enhanced Zeroconf flow with MAC address extraction and validation.

### Configuration Changes
- **Host/IP Update**: Host/IP is now editable from integration options.
- **Hide Chamber Temperature**: New option to toggle chamber temp pill visibility on card.
