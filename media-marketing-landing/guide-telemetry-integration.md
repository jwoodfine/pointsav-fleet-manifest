---
schema: foundry-runbook-v1
title: "Telemetry Engine Integration"
category: help
component: media-marketing-landing
status: active
last_edited: "2026-04-26"
---

# Telemetry Engine Integration

**Operator:** PointSav Digital Systems (Operator)
**Vendor Software:** PointSav Sovereign Telemetry (v1.2.0 - Rust Compiled)

## Execution Protocol
This document proves how Woodfine Operations deploys the generic PointSav Engine into the active corporate fleet to protect investor privacy.

1. **Compilation:** Operations pull the source code to the Tier-2 Cloud Node and execute `cargo build --release` to forge native Linux binaries.
2. **Kernel Locking (systemd):** The `telemetry-daemon` is locked into continuous execution via a Linux `systemd` service, listening on the internal proxy port.
3. **Data Provisioning:** Woodfine edge servers append live traffic to `app-mediakit-telemetry/assets/ledger_telemetry.csv`.
4. **Geographic Brain:** Woodfine injects its licensed `GeoLite2-City.mmdb` into the `assets/` vault.
5. **Ignition (Cron):** The `omni-matrix-engine` fires daily via crontab to synthesize the raw ledgers into 8 institutional tables.
6. **Secure Extraction:** Operations utilize a strictly scoped `rsync` Pull Diode to securely extract `REPORT_POINTSAV.md` back to local terminals.

---

*Copyright © 2026 Woodfine Management Corp. All rights reserved.*

*Woodfine Capital Projects™, Woodfine Management Corp™, PointSav Digital Systems™, Totebox Orchestration™, and Totebox Archive™ are trademarks of Woodfine Capital Projects Inc., used in Canada, the United States, Latin America, and Europe. All other trademarks are the property of their respective owners.*
