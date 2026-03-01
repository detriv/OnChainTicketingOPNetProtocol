# ⬡ OPnet OnChain Ticketing

> **Full on-chain event ticketing powered by Bitcoin L1 & OPnet Protocol.**  
> No backend. No database. No middlemen. Just Bitcoin.

---

## 🌐 Overview

**OPnet OnChain Ticketing** adalah aplikasi tiket acara yang berjalan sepenuhnya di Bitcoin L1 menggunakan protokol OPnet. Semua interaksi — pembelian tiket, verifikasi kepemilikan, hingga check-in — dilakukan langsung ke blockchain melalui OPnet MCP (Model Context Protocol).

```
Bitcoin L1  ←→  OPnet Protocol  ←→  MCP Endpoint  ←→  Browser App
```

---

## 🗂️ Struktur File

```
opnet-ticketing/
└── index.html        ← Seluruh aplikasi (HTML + CSS + JS inline)
```

> ✅ Single file. Zero dependencies. Zero build step.

---

## ⚙️ Teknologi

| Komponen | Detail |
|---|---|
| **Blockchain** | Bitcoin L1 |
| **Smart Contract Layer** | OPnet Protocol |
| **Blockchain Interaksi** | OPnet MCP — `https://ai.opnet.org/mcp` |
| **Wallet Support** | OPnet Wallet · UniSat Wallet |
| **Frontend** | Vanilla JS · HTML · Tailwind CDN |
| **QR Code** | QRCode.js (CDN) |
| **Font** | Orbitron · IBM Plex Mono |

**Tidak menggunakan:**
- ❌ EVM / Solidity
- ❌ Web3.js / Ethers.js
- ❌ Backend server
- ❌ Database terpusat
- ❌ API eksternal / payment gateway

---

## 🚀 Cara Menjalankan

### Langsung buka di browser

```bash
# Tidak perlu install apapun
open index.html
```

Atau serve lokal:

```bash
npx serve .
# Buka http://localhost:3000
```

### Deploy statis

Upload `index.html` ke platform apapun:

- [Vercel](https://vercel.com) — drag & drop folder
- [Netlify](https://netlify.com) — drag & drop folder
- [GitHub Pages](https://pages.github.com) — push ke repo, aktifkan Pages
- IPFS — untuk fully decentralized hosting

---

## 🔐 Wallet Connection

Aplikasi mendukung tiga metode koneksi wallet:

1. **OPnet Wallet** (prioritas utama) — via `window.opnet.connect()`
2. **UniSat Wallet** — via `window.unisat.requestAccounts()`
3. **MCP Simulation** — fallback otomatis jika tidak ada wallet extension terinstall (mode demo)

Wallet address digunakan sebagai identity user. Tidak ada email, password, atau akun.

---

## 🎟️ Fitur Aplikasi

### 1. Browse Events
- Query event dari OPnet contract via MCP
- Tampilkan nama, kategori, tanggal, lokasi, harga BTC, sisa tiket
- Filter berdasarkan **Kategori** dan **Tanggal**
- Progress bar ketersediaan tiket real-time

### 2. Buy Ticket (Full Onchain)
Saat user klik **BUY TICKET**:
1. BTC dikirim via OPnet ke contract organizer
2. Contract mencatat: TXN hash · sender · receiver · nominal · event ID · quantity · timestamp
3. Ticket token di-mint ke wallet pembeli
4. Kepemilikan dicatat permanen di Bitcoin L1

### 3. My Tickets
- Query semua tiket milik wallet yang terhubung
- Tampilkan: event name · quantity · ticket ID · status · TXN hash · block height
- Tombol **View Receipt** dan **Check In**

### 4. Onchain Receipt
Klik **View Receipt** untuk melihat:
- TXN Hash
- Block height & konfirmasi
- Sender & Receiver address
- Nominal BTC
- Event name & quantity
- Timestamp

Tersedia tombol:
- 📋 **Copy TXN** — salin hash ke clipboard
- 🔍 **View on Explorer** — buka OPnet Explorer
- 📷 **QR Code** — generate QR dari TXN hash
- 🖨️ **Print PDF** — `window.print()`

### 5. Ticket Validation (Check-In)
- Input Ticket ID atau TXN Hash
- Query blockchain untuk verifikasi owner & status
- Jika valid → update status ke **"Used"** via OPnet transaction
- Semua perubahan status bersifat permanen on-chain

---

## 🧠 MCP Integration

### Endpoint

```
POST https://ai.opnet.org/mcp
Content-Type: application/json
```

### Payload Format

```json
{
  "action": "buy_ticket",
  "params": {
    "eventId": "evt_001",
    "quantity": 2,
    "address": "bc1q...",
    "amount": 0.01
  },
  "network": "bitcoin"
}
```

### Actions yang Tersedia

| Action | Deskripsi |
|---|---|
| `get_events` | Ambil daftar event dari contract |
| `get_my_tickets` | Query tiket milik wallet tertentu |
| `buy_ticket` | Eksekusi pembelian + mint token |
| `get_transaction` | Ambil detail transaksi dari blockchain |
| `validate_ticket` | Verifikasi kepemilikan & status tiket |
| `update_ticket_status` | Update status tiket ke "Used" (on-chain) |
| `connect_wallet` | Koneksi wallet via MCP |

### Fungsi JavaScript Utama

```javascript
callMCP(action, params)       // Generic MCP wrapper
createEvent(params)           // Deploy event ke contract
buyTicket(eventId, qty)       // Beli tiket + mint token
getMyTickets(address)         // Ambil tiket per wallet
getReceipt(txHash)            // Ambil receipt dari blockchain
validateTicket(id)            // Verifikasi + check-in tiket
```

### Fallback Mock

Jika MCP endpoint belum aktif atau tidak tersedia, aplikasi otomatis menggunakan **simulasi mock data** yang realistis dengan delay acak (600–1200ms) untuk mensimulasikan latency blockchain. Semua logic siap dipakai saat endpoint aktif — tanpa perlu mengubah kode.

---

## 🎨 UI Design System

| Elemen | Nilai |
|---|---|
| **Mode** | Dark mode default |
| **Background** | `#070a0f` dengan animated grid |
| **Accent** | Orange `#f97316` · Gold `#fbbf24` |
| **Card Style** | Glassmorphism (`backdrop-filter: blur`) |
| **Font Display** | Orbitron (crypto aesthetic) |
| **Font Body** | IBM Plex Mono (terminal/code feel) |
| **Animation** | CSS keyframes · smooth transitions |
| **Layout** | Responsive grid · Mobile-first |

---

## 🔄 Alur Data Onchain

```
User Klik Buy
     │
     ▼
callMCP('buy_ticket')
     │
     ▼
OPnet Contract ← BTC Transfer
     │
     ├─ Catat TXN metadata
     ├─ Catat kepemilikan
     └─ Mint Ticket Token
          │
          ▼
     Wallet Pembeli
     (ticket ownership confirmed)
```

---

## 🛡️ Keamanan

- Tidak ada private key yang diproses di frontend
- Semua signing dilakukan oleh wallet extension user
- Data tiket bersifat immutable di Bitcoin L1
- Status check-in diupdate via signed transaction (bukan admin panel)

---

## 📦 Ticket Token — Metadata Struktur

```json
{
  "ticketId": "TKT-8f3a2b1c",
  "eventId": "evt_001",
  "eventName": "Bitcoin Summit 2025",
  "ticketType": "General Admission",
  "quantity": 2,
  "owner": "bc1q...",
  "txHash": "4a7b2c...",
  "blockHeight": 845210,
  "timestamp": 1720000000,
  "status": "Active"
}
```

---

## 🌍 Live Explorer

Semua transaksi dapat diverifikasi di:

```
https://explorer.opnet.org/tx/{txHash}
```

---

## 📝 Lisensi

MIT License — bebas digunakan, dimodifikasi, dan didistribusikan.

---

> Built on Bitcoin. Secured by OPnet. Owned by you.
