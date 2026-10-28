<div align="center">

<img src="https://www.rutken.com/rutken-logo.png" alt="Rutken" width="96" />

# Rutken

**Find it. Prove it. Fix it. One tool for Android app security.**

Static analysis, dynamic analysis, and live offense in a single workbench, backed by an in-house hooking engine that gets onto hardened apps where off-the-shelf tooling gets caught.

[Website](https://www.rutken.com) · [Blog](https://www.rutken.com/blog) · [Pricing](https://www.rutken.com/pricing) · [Plugins](https://github.com/Rutken-Company/rutken-plugins)

</div>

---

## Install

Two lines. The installer verifies a pinned Ed25519 release key before anything runs (fail-closed), then the launcher starts so you pick an edition.

```bash
curl -fsSL https://www.rutken.com/install.sh | bash
source ~/.bashrc   # use ~/.zshrc for zsh; this puts `rutken` on your PATH
```

Pick **Community** (free, no sign-in) at the prompt. Then:

```bash
rutken --help                 # see everything you can do
rutken app.apk rules          # run the SAST rules over an APK
rutken-gui                    # or open the workbench
```

> **Community is Linux x86_64 today** (Windows is in progress). Every download is signed with a pinned release key, so the trust root is the key, not the download host.

---

## What it does

Rutken takes an APK apart and tells you what's wrong, statically, then **proves it on a real device**.

- **SAST.** Decode → IR → call-graph, CFG, taint, const-prop, hierarchy → a fixed rule set, with findings sorted by a deterministic order. Interprocedural taint (opt-in) follows the real flows: an intent extra read in one method, sent to an HTTP sink in another.
- **DAST and offense.** Drive the app on a device, capture traffic, unpin TLS, intercept and replay. Change what the app sees at runtime, flip a field, rewrite a return value, and watch the logic downstream react.
- **SCA.** Third-party dependency CVEs against a live OSV-derived dataset, with version fingerprinting.
- **The workbench.** A native GUI, a scriptable CLI, and an **MCP server**, so your own AI agent (Claude, Cursor, VS Code) can query the findings directly.

### The part you can't get elsewhere

Most of the dynamic work (attach, hook a method, read the arguments, watch the traffic) Rutken does with Frida, and that part is free. But the apps you most want to look at are the ones that fight back: they fingerprint injected instrumentation and, when they find it, corrupt their own state and crash.

So we built our own **in-house hooking engine**, with our own instruction encoding and delivery path. It gets onto a hardened target *without* repackaging it and keeps working where off-the-shelf injection gets caught. The bar we set: match or beat Frida on the cases that matter, or there's no reason for it to exist.

It turns a static maybe into proof. The analyzer says "this value gates the root check"; the engine lets you force it and confirm the gate opens on a real device. Finding and proof, in the same tool, with the A/B/A to show the intervention was the variable.

---

## Editions

| | Community | Paid tiers |
|---|---|---|
| SAST (rules, taint, SCA) | ✅ | ✅ |
| Dynamic capture, TLS unpin, intercept, repeater (Frida-based) | ✅ | ✅ |
| GUI workbench + CLI + MCP | ✅ | ✅ |
| **In-house hooking engine** (hardened targets, no-repack, on-device proof) | ✗ | ✅ |
| AI assistant, remediation, team workspaces | ✗ | ✅ |

Community is free because it runs on your machine, and a tool you can't try isn't a tool. The in-house engine is what the paid tiers add. See [pricing](https://www.rutken.com/pricing) for the full matrix.

---

## Verify a download yourself (advanced)

Every artifact in a release is signed with Rutken's pinned Ed25519 release key. To verify the launcher before running it:

```bash
# from a release's assets, or the signed blob:
curl -fsSLO https://www.rutken.com/dl/launcher/0.4.1/linux-x86_64/rutken-launcher
curl -fsSLO https://www.rutken.com/dl/launcher/0.4.1/linux-x86_64/rutken-launcher.sig.raw
curl -fsSLO https://www.rutken.com/dl/rutken-release.pem

openssl pkeyutl -verify -pubin -inkey rutken-release.pem -rawin \
  -in rutken-launcher -sigfile rutken-launcher.sig.raw   # Signature Verified Successfully

chmod +x rutken-launcher && ./rutken-launcher
```

The installer does this automatically and refuses to continue if verification fails.

---

## Links

- **Docs and download:** https://www.rutken.com
- **Blog**, how it's built, honestly: https://www.rutken.com/blog
- **Community rule packs:** https://github.com/Rutken-Company/rutken-plugins
- **SCA dataset:** https://github.com/Rutken-Company/rutken-sca-dataset

---

<div align="center">
<sub>© Rutken. Proprietary, all rights reserved. The Community edition is free to use.</sub>
</div>
