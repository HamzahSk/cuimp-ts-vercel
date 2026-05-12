<div align="center">

# Cuimp

**Wrapper Node.js yang powerful untuk curl-impersonate**

[![npm version](https://img.shields.io/npm/v/cuimp.svg?style=flat-square)](https://www.npmjs.com/package/cuimp)
[![npm downloads](https://img.shields.io/npm/dm/cuimp.svg?style=flat-square)](https://www.npmjs.com/package/cuimp)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](https://opensource.org/licenses/MIT)
[![Node.js Version](https://img.shields.io/badge/node-%3E%3D18.17-brightgreen.svg?style=flat-square)](https://nodejs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.6-blue.svg?style=flat-square&logo=typescript)](https://www.typescriptlang.org/)
[![GitHub Release](https://img.shields.io/github/v/release/F4RAN/cuimp-ts?style=flat-square)](https://github.com/F4RAN/cuimp-ts/releases)
[![GitHub Stars](https://img.shields.io/github/stars/F4RAN/cuimp-ts?style=flat-square)](https://github.com/F4RAN/cuimp-ts/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/F4RAN/cuimp-ts?style=flat-square)](https://github.com/F4RAN/cuimp-ts/issues)

[Hosted on TierHive Hourly VPS](https://tierhive.com/r/F726648AD09A)

Buat HTTP request yang meniru perilaku browser asli dan bypass proteksi anti-bot dengan mudah.

**🚀 Mendukung Vercel Serverless**: Library ini sudah dimodifikasi untuk berjalan di environment serverless seperti Vercel dengan menyimpan binary di direktori `/tmp/`.

[Fitur](#-fitur) •
[Instalasi](#-instalasi) •
[Mulai Cepat](#-mulai-cepat) •
[Dokumentasi](#api-reference) •
[Contoh](#contoh-penggunaan-dalam-project)

</div>

---

## ✨ Fitur

- 🚀 **Penyamaran Browser**: Meniru Chrome, Firefox, Safari, dan Edge
- 🔧 **Mudah Digunakan**: API sederhana mirip axios/fetch
- 📦 **Tanpa Dependency**: Hanya butuh `tar` untuk ekstraksi binary
- 🎯 **Dukungan TypeScript**: Lengkap dengan definisi tipe
- 🔄 **Auto Binary Management**: Otomatis download dan kelola binary curl-impersonate
- 🌐 **Cross-Platform**: Berjalan di Linux, macOS, dan Windows
- 🔒 **Dukungan Proxy**: Built-in support untuk HTTP, HTTPS, dan SOCKS proxy dengan autentikasi
- 📁 **Instalasi Bersih**: Binary disimpan di direktori package, bukan di root project Anda
- 🍪 **Manajemen Cookie**: Otomatis simpan dan kirim cookie antar request
- ✅ **Body Response Error**: Mengembalikan body HTTP lengkap saat error 4xx/5xx (seperti axios/Postman)
- ☁️ **Vercel Serverless**: Mendukung environment serverless dengan penyimpanan di `/tmp/`

## 📑 Daftar Isi

- [Fitur](#-fitur)
- [Instalasi](#-instalasi)
- [Mulai Cepat](#-mulai-cepat)
- [Contoh Penggunaan dalam Project](#contoh-penggunaan-dalam-project)
- [API Reference](#api-reference)
- [Konfigurasi](#konfigurasi)
- [Browser yang Didukung](#browser-yang-didukung)
- [Format Response](#format-response)
- [Manajemen Binary](#manajemen-binary)
- [Troubleshooting](#troubleshooting)
- [Requirements](#requirements)
- [Kontribusi](#-kontribusi)
- [Lisensi](#-lisensi)
- [Kontributor](#-kontributor)

## 📦 Instalasi

```bash
npm install cuimp@github:HamzahSk/cuimp-ts-vercel
```

## 🚀 Mulai Cepat

```javascript
import { get, post, createCuimpHttp } from 'cuimp'

// Request GET sederhana
const response = await get('https://httpbin.org/headers')
console.log(response.data)

// POST dengan data
const result = await post('https://httpbin.org/post', {
  name: 'John Doe',
  email: 'john@example.com',
})

// Menggunakan instance HTTP client
const client = createCuimpHttp({
  descriptor: { browser: 'chrome', version: '123' },
})

const data = await client.get('https://api.example.com/users')
```

## Contoh Penggunaan dalam Project

### Web Scraping dengan Penyamaran Browser

**Penjelasan**: Contoh ini menunjukkan cara melakukan scraping website yang memblokir request biasa dengan meniru Chrome browser.

```javascript
import { get, createCuimpHttp } from 'cuimp'

// Buat client yang meniru Chrome 123
const scraper = createCuimpHttp({
  descriptor: { browser: 'chrome', version: '123' },
})

// Scrape website yang memblokir request biasa
const response = await scraper.get('https://example.com/protected-content', {
  headers: {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36',
    Accept: 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
  },
})

console.log('Konten yang di-scrape:', response.data)
```

### Testing API dengan Browser Berbeda

**Penjelasan**: Loop ini akan menguji API Anda dengan signature dari berbagai browser.

```javascript
import { createCuimpHttp } from 'cuimp'

// Test API Anda dengan signature browser berbeda
const browsers = ['chrome', 'firefox', 'safari', 'edge']

for (const browser of browsers) {
  const client = createCuimpHttp({
    descriptor: { browser, version: 'latest' },
  })

  const response = await client.get('https://your-api.com/test')
  console.log(`${browser}: ${response.status}`)
}
```

### Manajemen Cookie Otomatis

**Penjelasan**: Cookie akan otomatis disimpan dan dikirim ulang di request berikutnya, berguna untuk session management.

```javascript
import { createCuimpHttp } from 'cuimp'

// Aktifkan manajemen cookie otomatis
const client = createCuimpHttp({
  descriptor: { browser: 'chrome', version: '123' },
  cookieJar: true, // Cookie otomatis disimpan dan dikirim
})

// Request pertama - server set cookies
await client.get('https://httpbin.org/cookies/set/session_id/abc123')

// Request kedua - cookies otomatis dikirim
const response = await client.get('https://httpbin.org/cookies')
console.log(response.data.cookies) // { session_id: 'abc123' }

// Akses cookies secara programmatic
const cookieJar = client.getCookieJar()
const cookies = cookieJar.getCookies()

// Hapus semua cookies
client.clearCookies()

// Bersihkan resource saat selesai (hapus file cookie temp)
client.destroy()
```

### Menggunakan Proxy

**Penjelasan**: Contoh penggunaan berbagai jenis proxy untuk request HTTP.

```javascript
import { request } from 'cuimp'

// HTTP proxy
const response1 = await request({
  url: 'https://httpbin.org/ip',
  proxy: 'http://proxy.example.com:8080',
})

// SOCKS5 proxy dengan autentikasi
const response2 = await request({
  url: 'https://httpbin.org/ip',
  proxy: 'socks5://user:pass@proxy.example.com:1080',
})

// Deteksi proxy otomatis dari environment variables
// HTTP_PROXY, HTTPS_PROXY, ALL_PROXY
const response3 = await request({
  url: 'https://httpbin.org/ip',
  // Akan otomatis gunakan HTTP_PROXY jika tersedia
})
```

### Streaming Response

**Penjelasan**: Untuk mendapatkan data secara bertahap (misalnya SSE - Server-Sent Events), gunakan `requestStream`.

**Catatan**: Untuk streaming (SSE atau chunked responses), tambahkan `--no-buffer` di `extraCurlArgs` agar curl langsung flush output. Anda juga bisa set di default client.

```javascript
import { requestStream } from 'cuimp'

await requestStream(
  { url: 'https://httpbin.org/stream/3', extraCurlArgs: ['--no-buffer'] },
  {
    onHeaders: ({ status, headers }) => {
      console.log('status', status)
      console.log('content-type', headers['Content-Type'])
    },
    onData: chunk => {
      process.stdout.write(chunk)
    },
    onEnd: info => {
      console.log('selesai', info.status)
    },
    onError: err => {
      console.error('error stream', err)
    },
  }
)
```

**Mengatur di default client:**

```javascript
const client = createCuimpHttp({ extraCurlArgs: ['--no-buffer'] })
await client.requestStream({ url: 'https://httpbin.org/stream/3' }, { onData: chunk => {} })
```

**Jika ingin full body, aktifkan buffering:**

```javascript
const res = await requestStream({ url: 'https://httpbin.org/stream/3' }, { collectBody: true })
console.log(res.rawBody?.toString('utf8'))
```

### Pre-download Binary

**Penjelasan**: Download binary curl-impersonate sebelum digunakan, berguna untuk persiapan offline atau CI/CD.

```javascript
import { Cuimp, downloadBinary } from 'cuimp'

// Metode 1: Menggunakan class Cuimp
const cuimp = new Cuimp({ descriptor: { browser: 'chrome' } })
const binaryInfo = await cuimp.download()
console.log('Downloaded:', binaryInfo.binaryPath)

// Metode 2: Menggunakan fungsi convenience
const info = await downloadBinary({
  descriptor: { browser: 'firefox', version: '133' },
})

// Pre-download beberapa browser untuk penggunaan offline
const browsers = ['chrome', 'firefox', 'safari', 'edge']
for (const browser of browsers) {
  await downloadBinary({ descriptor: { browser } })
  console.log(`${browser} binary siap`)
}
```

### Custom Logging

**Penjelasan**: Anda bisa menonaktifkan log atau menggunakan logger custom (seperti Winston, Pino, dll).

```javascript
import { createCuimpHttp } from 'cuimp'

// Nonaktifkan semua log
const silentLogger = {
  info: () => {},
  warn: () => {},
  error: () => {},
  debug: () => {},
}

const client = createCuimpHttp({
  descriptor: { browser: 'chrome' },
  logger: silentLogger,
})

// Atau gunakan structured logger (Winston, Pino, dll.)
const client = createCuimpHttp({
  descriptor: { browser: 'chrome' },
  logger: myStructuredLogger,
})
```

## API Reference

### Fungsi Convenience (Mudah Digunakan)

#### `get(url, config?)`

**Fungsi**: Melakukan HTTP GET request.

```javascript
const response = await get('https://api.example.com/users')
```

#### `post(url, data?, config?)`

**Fungsi**: Melakukan HTTP POST request dengan data.

```javascript
const response = await post('https://api.example.com/users', {
  name: 'John Doe',
  email: 'john@example.com',
})
```

#### `put(url, data?, config?)`

**Fungsi**: Melakukan HTTP PUT request.

#### `patch(url, data?, config?)`

**Fungsi**: Melakukan HTTP PATCH request.

#### `del(url, config?)`

**Fungsi**: Melakukan HTTP DELETE request.

#### `head(url, config?)`

**Fungsi**: Melakukan HTTP HEAD request (hanya ambil headers, tanpa body).

#### `options(url, config?)`

**Fungsi**: Melakukan HTTP OPTIONS request.

#### `downloadBinary(options?)`

**Fungsi**: Download binary curl-impersonate tanpa melakukan HTTP request.

```javascript
// Download binary default
const info = await downloadBinary()

// Download binary browser spesifik
const chromeInfo = await downloadBinary({
  descriptor: { browser: 'chrome', version: '123' },
})
```

### HTTP Client

#### `createCuimpHttp(options?)`

**Fungsi**: Membuat instance HTTP client yang bisa digunakan berulang kali.

```javascript
const client = createCuimpHttp({
  descriptor: { browser: 'chrome', version: '123' },
  path: '/custom/path/to/binary',
})

// Gunakan client
const response = await client.get('https://api.example.com/data')
```

#### `request(config)`

**Fungsi**: Melakukan request dengan konfigurasi lengkap.

```javascript
const response = await request({
  url: 'https://api.example.com/users',
  method: 'POST',
  headers: {
    Authorization: 'Bearer token',
    'Content-Type': 'application/json',
  },
  data: { name: 'John Doe' },
  timeout: 5000,
})
```

#### `requestStream(config, handlers?)`

**Fungsi**: Stream response body secara bertahap dengan callback.

**Penjelasan**: Berguna untuk response yang besar atau real-time (SSE, chunked transfer).

```javascript
const res = await requestStream(
  { url: 'https://httpbin.org/stream/3' },
  {
    onHeaders: ({ status }) => console.log('status', status),
    onData: chunk => process.stdout.write(chunk),
    onEnd: info => console.log('selesai', info.status),
    onError: err => console.error(err),
  }
)
```

**Interface `handlers`:**

```typescript
interface CuimpStreamHandlers {
  onHeaders?: (info: {
    status: number
    statusText: string
    headers: Record<string, string>
  }) => void
  onData?: (chunk: Buffer) => void
  onEnd?: (info: CuimpStreamResponse) => void
  onError?: (error: Error) => void
  collectBody?: boolean
}
```

### Class Inti

#### `Cuimp`

**Penjelasan**: Class inti untuk mengelola binary curl-impersonate dan descriptor.

```javascript
import { Cuimp } from 'cuimp'

const cuimp = new Cuimp({
  descriptor: { browser: 'chrome', version: '123' },
  path: '/custom/path',
})

// Verifikasi binary
const info = await cuimp.verifyBinary()

// Preview command yang akan dijalankan
const command = cuimp.buildCommandPreview('https://example.com', 'GET')

// Download binary tanpa verifikasi
const binaryInfo = await cuimp.download()
```

#### `CuimpHttp`

**Penjelasan**: Class HTTP client yang membungkus Cuimp core.

```javascript
import { CuimpHttp, Cuimp } from 'cuimp'

const core = new Cuimp()
const client = new CuimpHttp(core, {
  baseURL: 'https://api.example.com',
  timeout: 10000,
})
```

`CuimpHttp` juga mendukung streaming via `client.requestStream(config, handlers)`.

## Konfigurasi

### CuimpDescriptor

**Penjelasan**: Konfigurasi browser mana yang akan ditiru.

```typescript
interface CuimpDescriptor {
  browser?: 'chrome' | 'firefox' | 'edge' | 'safari'
  version?: string // contoh: '123', '124', atau 'latest' (default)
  architecture?: 'x64' | 'arm64'
  platform?: 'linux' | 'windows' | 'macos'
  forceDownload?: boolean // Paksa download ulang meskipun binary sudah ada
}
```

### CuimpRequestConfig

**Penjelasan**: Opsi konfigurasi untuk request HTTP.

```typescript
interface CuimpRequestConfig {
  url: string
  method?: 'GET' | 'POST' | 'PUT' | 'PATCH' | 'DELETE' | 'HEAD' | 'OPTIONS'
  headers?: Record<string, string>
  data?: any
  timeout?: number
  maxRedirects?: number
  proxy?: string // URL proxy HTTP, HTTPS, atau SOCKS
  insecureTLS?: boolean // Skip verifikasi sertifikat TLS
  signal?: AbortSignal // Pembatalan request
}
```

### CuimpOptions

**Penjelasan**: Opsi inti untuk konfigurasi Cuimp.

```typescript
interface CuimpOptions {
  descriptor?: CuimpDescriptor
  path?: string // Path custom ke binary curl-impersonate
  extraCurlArgs?: string[] // Argumen curl global untuk semua request
  logger?: Logger // Custom logger untuk pesan download/verifikasi binary
  proxy?: string // Proxy default untuk semua request (HTTP, HTTPS, atau SOCKS URL)
  cookieJar?: boolean | string // Aktifkan manajemen cookie otomatis
  autoDownload?: boolean // Jika false, throw error alih-alih auto-download binary (default: true)
}
```

### Konfigurasi Proxy

**Penjelasan**: Set proxy default untuk semua request saat membuat client. `proxy` per-request akan override yang ini.

```typescript
// Proxy level session: semua request gunakan proxy ini secara default
const client = createCuimpHttp({
  proxy: 'http://proxy.example.com:8080',
})

await client.get('https://api.example.com/data') // Gunakan proxy

// Override untuk satu request
await client.get('https://api.example.com/other', {
  proxy: 'socks5://other-proxy:1080',
})
```

**Format proxy yang didukung**: `http://host:port`, `https://host:port`, `socks4://host:port`, `socks5://host:port`, atau `host:port` (default HTTP). Autentikasi: `http://user:pass@host:port`.

### Konfigurasi Cookie Jar

**Penjelasan**: Opsi `cookieJar` mengaktifkan manajemen cookie otomatis.

```typescript
// Opsi 1: File temp otomatis (dibersihkan saat destroy)
const client = createCuimpHttp({
  cookieJar: true,
})

// Opsi 2: Path file custom (persisten antar run)
// Rekomendasi: Gunakan direktori home user untuk keamanan
import os from 'os'
import path from 'path'

const cookiePath = path.join(os.homedir(), '.cuimp', 'cookies', 'my-cookies.txt')
const client = createCuimpHttp({
  cookieJar: cookiePath, // Lokasi user-specific, aman
})

// Opsi 3: Nonaktif (default)
const client = createCuimpHttp({
  cookieJar: false, // atau tidak usah ditulis
})
```

**Best Practice untuk Path File Cookie:**

- ✅ Gunakan `~/.cuimp/cookies/` (direktori home user) - aman, user-specific, konsisten dengan penyimpanan binary
- ✅ Gunakan direktori temp untuk cookie sementara - auto-dibersihkan
- ❌ Hindari root project (`./cookies.txt`) - risiko commit data sensitif ke git

**Method Cookie Jar:**

```typescript
// Dapatkan instance cookie jar
const jar = client.getCookieJar()

// Dapatkan semua cookies
const cookies = jar.getCookies()

// Dapatkan cookies untuk domain spesifik
const domainCookies = jar.getCookiesForDomain('example.com')

// Set cookie secara manual
jar.setCookie({
  domain: 'example.com',
  name: 'my_cookie',
  value: 'my_value',
  path: '/',
  secure: true,
  expires: new Date('2025-12-31'),
})

// Hapus cookie
jar.deleteCookie('my_cookie', 'example.com')

// Hapus semua cookies
client.clearCookies()

// Bersihkan resource (hapus file temp jika pakai cookieJar: true)
client.destroy()
```

### Kontrol Auto-Download Binary

**Penjelasan**: Secara default, cuimp otomatis download binary jika tidak ditemukan. Anda bisa menonaktifkan perilaku ini.

```typescript
// Nonaktifkan auto-download (untuk pengguna advanced)
const client = createCuimpHttp({
  descriptor: { browser: 'chrome' },
  autoDownload: false, // Akan throw error jika binary tidak ditemukan
})

try {
  await client.get('https://api.example.com/data')
} catch (error) {
  // Error: Binary not found for chrome on macos-arm64...
  // Set autoDownload: true to enable automatic download
}

// Download eksplisit tetap berfungsi
const cuimp = new Cuimp({ descriptor: { browser: 'chrome' }, autoDownload: false })
await cuimp.download() // Selalu download, abaikan setting autoDownload
```

**Use case untuk `autoDownload: false`:**

- Kontrol kapan binary di-download (metode instalasi custom)
- Fail fast jika binary hilang (environment production)
- Cegah download yang tidak diinginkan

### Custom Logging

**Penjelasan**: Anda bisa menyediakan custom logger untuk mengontrol cara cuimp mencatat pesan download dan verifikasi binary.

```typescript
interface Logger {
  info(...args: any[]): void
  warn(...args: any[]): void
  error(...args: any[]): void
  debug(...args: any[]): void
}
```

**Contoh: Logger dengan format custom**

```javascript
import { createCuimpHttp } from 'cuimp'

// Custom logger dengan output terformat
const customLogger = {
  info: (...args) => console.log('[INFO]', new Date().toISOString(), ...args),
  warn: (...args) => console.warn('[WARN]', new Date().toISOString(), ...args),
  error: (...args) => console.error('[ERROR]', new Date().toISOString(), ...args),
  debug: (...args) => console.debug('[DEBUG]', new Date().toISOString(), ...args),
}

const client = createCuimpHttp({
  descriptor: { browser: 'chrome', version: '123' },
  logger: customLogger,
})

// Sekarang semua pesan download/verifikasi binary akan gunakan format custom Anda
await client.get('https://api.example.com/data')
// Output: [INFO] 2024-01-15T10:30:00.000Z Verifying binary...
// Output: [INFO] 2024-01-15T10:30:01.000Z Binary verified successfully
```

**Contoh: Mengumpulkan log untuk analisis**

```javascript
const logEntries = []

const collectingLogger = {
  info: (...args) =>
    logEntries.push({ level: 'info', timestamp: Date.now(), message: args.join(' ') }),
  warn: (...args) =>
    logEntries.push({ level: 'warn', timestamp: Date.now(), message: args.join(' ') }),
  error: (...args) =>
    logEntries.push({ level: 'error', timestamp: Date.now(), message: args.join(' ') }),
  debug: (...args) =>
    logEntries.push({ level: 'debug', timestamp: Date.now(), message: args.join(' ') }),
}

const client = createCuimpHttp({
  descriptor: { browser: 'firefox' },
  logger: collectingLogger,
})

await client.get('https://api.example.com/data')

// Analisis log yang dikumpulkan
console.log('Log terkumpul:', logEntries)
// Bisa kirim ke layanan logging eksternal, simpan ke file, dll.
```

**Contoh: Debug logger kondisional (berbasis environment)**

```javascript
// Hanya tampilkan debug messages saat DEBUG=true di-set
const smartLogger = {
  info: (...args) => console.log('[INFO]', ...args),
  warn: (...args) => console.warn('[WARN]', ...args),
  error: (...args) => console.error('[ERROR]', ...args),
  debug: process.env.DEBUG === 'true' ? (...args) => console.debug('[DEBUG]', ...args) : () => {}, // Suppress debug secara default
}

const client = createCuimpHttp({
  descriptor: { browser: 'chrome' },
  logger: smartLogger,
})

await client.get('https://api.example.com/data')
// Dengan DEBUG=true: Tampilkan semua log termasuk debug
// Tanpa DEBUG: Hanya tampilkan info/warn/error, suppress debug
```

Secara default, cuimp menggunakan `console` untuk logging. Pesan diagnostik (seperti "Found existing binary", "Binary verified") dicatat di level `debug`, jadi hanya muncul jika logger Anda mengimplementasikan `debug()`.

## Browser yang Didukung

| Browser | Versi                                                                     | Platform                       |
| ------- | ------------------------------------------------------------------------- | ------------------------------ |
| Chrome  | 99, 100, 101, 104, 107, 110, 116, 119, 120, 123, 124, 131, 133a, 136, 142 | Linux, Windows, macOS, Android |
| Firefox | 133, 135, 145                                                             | Linux, Windows, macOS          |
| Edge    | 99, 101                                                                   | Linux, Windows, macOS          |
| Safari  | 153, 155, 170, 172, 180, 184, 260, 2601                                   | macOS, iOS                     |
| Tor     | 145                                                                       | Linux, Windows, macOS          |

## Format Response

**Penjelasan**: Semua method HTTP mengembalikan response yang terstandarisasi. **Penting**: Berbeda dengan perilaku curl tradisional, cuimp mengembalikan objek response untuk semua kode status HTTP (termasuk 4xx/5xx), memungkinkan Anda mengakses body, headers, dan informasi status error. Hanya network error (kegagalan koneksi, DNS error, dll.) yang throw exception.

```typescript
interface CuimpResponse<T = any> {
  status: number
  statusText: string
  headers: Record<string, string>
  data: T
  rawBody: Buffer
  request: {
    url: string
    method: string
    headers: Record<string, string>
    command: string
  }
}
```

## Contoh

> **📁 Contoh yang Bisa Dijalankan**: Lihat folder [`examples/`](./examples/) untuk contoh lengkap yang bisa langsung dijalankan, mendemonstrasikan semua fitur cuimp.

### Penggunaan Dasar

```javascript
import { get, post } from 'cuimp'

// Request GET
const users = await get('https://jsonplaceholder.typicode.com/users')
console.log(users.data)

// Request POST
const newUser = await post('https://jsonplaceholder.typicode.com/users', {
  name: 'John Doe',
  email: 'john@example.com',
})
```

### Menggunakan HTTP Client

```javascript
import { createCuimpHttp } from 'cuimp'

const client = createCuimpHttp({
  descriptor: { browser: 'chrome', version: '123' },
})

// Set default headers
client.defaults.headers['Authorization'] = 'Bearer your-token'

// Buat request
const response = await client.get('/api/users')
```

### Custom Binary Path

```javascript
import { Cuimp } from 'cuimp'

const cuimp = new Cuimp({
  path: '/usr/local/bin/curl-impersonate',
})

const info = await cuimp.verifyBinary()
```

### Menangani Response Error 4xx/5xx

**Penjelasan**: Berbeda dengan curl biasa, cuimp mengembalikan objek response lengkap untuk error HTTP, bukan throw exception.

```javascript
import { get, post } from 'cuimp'

// Response 4xx/5xx mengembalikan objek response (tidak di-throw)
const response = await get('https://httpbin.org/status/404')

if (response.status === 404) {
  console.log('Resource tidak ditemukan:', response.data)
} else if (response.status >= 500) {
  console.error('Server error:', response.statusText)
  console.error('Detail error:', response.data)
} else if (response.status >= 400) {
  console.warn('Client error:', response.status, response.statusText)
}

// Menangani JSON error response
const errorResponse = await post('https://api.example.com/users', {
  email: 'invalid-email',
})

if (errorResponse.status === 400) {
  // Akses body error (parsed JSON jika Content-Type adalah application/json)
  const errorData = errorResponse.data
  console.log('Validation errors:', errorData.errors)
  console.log('Error message:', errorData.message)
}
```

### Error Handling

**Response 4xx/5xx**: Berbeda dengan perilaku curl tradisional, cuimp mengembalikan objek response lengkap untuk kode status error HTTP (4xx/5xx), mirip dengan axios dan Postman. Ini memungkinkan Anda mengakses body, headers, dan informasi status error.

```javascript
import { get } from 'cuimp'

// Response 4xx/5xx mengembalikan objek response (tidak di-throw)
const response = await get('https://httpbin.org/status/404')
console.log('Status:', response.status) // 404
console.log('Status Text:', response.statusText) // 'Not Found'
console.log('Body:', response.data) // Response body (jika ada)
console.log('Headers:', response.headers) // Response headers

// Cek status dan handle sesuai kebutuhan
if (response.status >= 400) {
  console.error(`Error ${response.status}:`, response.data)
} else {
  console.log('Sukses:', response.data)
}
```

**Network Error**: Network error (kegagalan koneksi, DNS error, dll.) tetap di-throw sebagai exception:

```javascript
import { get } from 'cuimp'

try {
  const response = await get('https://api.example.com/data')
  console.log(response.data)
} catch (error) {
  if (error.code === 'ENOTFOUND') {
    console.log('Network error: Resolusi DNS gagal')
  } else if (error.code === 'ECONNREFUSED') {
    console.log('Network error: Koneksi ditolak')
  } else {
    console.log('Error tidak diketahui:', error.message)
  }
}
```

## Manajemen Binary

**Penjelasan**: Cuimp otomatis mengelola binary curl-impersonate dengan cara:

1. **Download Otomatis**: Download binary yang sesuai untuk platform Anda saat pertama kali digunakan
2. **Pencocokan Versi**: Gunakan ulang binary yang di-cache hanya jika versinya cocok dengan yang diminta
3. **Force Download**: Gunakan `forceDownload: true` untuk bypass cache dan selalu download binary fresh
4. **Verifikasi**: Cek integritas dan permission binary
5. **Penyimpanan Bersih**: Binary disimpan di `~/.cuimp/binaries/` (direktori home user) atau `/tmp/cuimp/binaries/` (untuk Vercel serverless)
6. **Cross-Platform**: Otomatis deteksi platform dan arsitektur Anda

### Perilaku Versi

- **Versi spesifik** (contoh: `'133'`): Gunakan binary yang di-cache jika versinya cocok, jika tidak download
- **'latest'** (default): Gunakan binary yang di-cache apapun, atau download jika tidak ada
- **forceDownload**: Selalu download, abaikan cache (berguna untuk selalu mendapat versi terbaru)

### Lokasi Penyimpanan Binary

- **Lokasi download**: `~/.cuimp/binaries/` (direktori home user) atau `/tmp/cuimp/binaries/` (Vercel serverless)
- **Lokasi pencarian**: Juga cek `node_modules/cuimp/binaries/` dan system paths sebagai fallback
- **Shared antar project**: Binary yang di-download digunakan ulang antar project
- **Tanpa Polusi Project**: Direktori project Anda tetap bersih

### Format Proxy yang Didukung

```javascript
// HTTP proxy
proxy: 'http://proxy.example.com:8080'

// HTTPS proxy
proxy: 'https://proxy.example.com:8080'

// SOCKS4 proxy
proxy: 'socks4://proxy.example.com:1080'

// SOCKS5 proxy
proxy: 'socks5://proxy.example.com:1080'

// Proxy dengan autentikasi
proxy: 'http://username:password@proxy.example.com:8080'
proxy: 'socks5://username:password@proxy.example.com:1080'

// Otomatis dari environment variables
// HTTP_PROXY, HTTPS_PROXY, ALL_PROXY, http_proxy, https_proxy, all_proxy
```

## Catatan Penting

### Perilaku Force Download

**Penjelasan**: Cuimp **selalu download binary fresh** saat pertama kali digunakan, terlepas dari apa yang sudah terinstal di sistem Anda. Ini memastikan:

- ✅ **Konsistensi**: Semua pengguna mendapat versi binary yang sama
- ✅ **Keandalan**: Tidak bergantung pada binary yang terinstal di sistem
- ✅ **Keamanan**: Download fresh dengan checksum yang terverifikasi
- ✅ **Kesederhanaan**: Tidak perlu kelola dependency sistem

### Environment Variables

**Penjelasan**: Cuimp otomatis deteksi dan gunakan environment variables proxy ini:

```bash
# Set proxy untuk semua request
export HTTP_PROXY=http://proxy.example.com:8080
export HTTPS_PROXY=https://proxy.example.com:8080
export ALL_PROXY=socks5://proxy.example.com:1080

# Atau gunakan varian lowercase
export http_proxy=http://proxy.example.com:8080
export https_proxy=https://proxy.example.com:8080
export all_proxy=socks5://proxy.example.com:1080
```

## Requirements

- Node.js >= 18.17
- Koneksi internet (untuk download binary)

## Troubleshooting

### Masalah Umum

**Q: Download binary gagal**

```bash
# Cek koneksi internet Anda dan coba lagi
# Binary akan di-download ke node_modules/cuimp/binaries/
```

**Q: Proxy tidak bekerja**

```javascript
// Pastikan URL proxy Anda benar
const response = await request({
  url: 'https://httpbin.org/ip',
  proxy: 'http://username:password@proxy.example.com:8080',
})

// Atau set environment variables
process.env.HTTP_PROXY = 'http://proxy.example.com:8080'
```

**Q: Error permission denied**

```bash
# Di sistem Unix, pastikan binary punya permission execute
chmod +x node_modules/cuimp/binaries/curl-impersonate
```

**Q: Binary tidak ditemukan**

```javascript
// Paksa re-download dengan menghapus direktori binaries
rm -rf node_modules/cuimp/binaries/
// Lalu jalankan kode Anda lagi - akan re-download
```

### Mode Debug

**Penjelasan**: Aktifkan debug logging untuk melihat apa yang terjadi:

```javascript
// Set debug environment variable
process.env.DEBUG = 'cuimp:*'

// Atau cek binary path
import { Cuimp } from 'cuimp'
const cuimp = new Cuimp()
const binaryPath = await cuimp.verifyBinary()
console.log('Binary path:', binaryPath)
```

### Docker

#### Error: "error setting certificate verify locations"

**Penjelasan**: Saat menjalankan cuimp di dalam container Docker, Anda mungkin menemui error berikut:

`error setting certificate verify locations: CAfile: /etc/ssl/certs/ca-certificates.crt CApath: /etc/ssl/certs`

Ini terjadi karena container tidak punya akses ke CA certificates yang diperlukan.  
Untuk memperbaikinya, mount direktori certificate dari host machine ke dalam container, contoh:

```yaml
volumes:
  - /etc/ssl/certs:/etc/ssl/certs:ro
```

Ini memastikan cuimp bisa memverifikasi sertifikat TLS dengan benar di dalam container.

## 📄 Lisensi

Project ini dilisensikan di bawah MIT License - lihat file [LICENSE](LICENSE) untuk detail.

## 🤝 Kontribusi

Kontribusi, issues, dan feature request sangat welcome!

Jangan ragu untuk cek [halaman issues](https://github.com/F4RAN/cuimp-ts/issues) jika Anda ingin berkontribusi.

1. Fork project
2. Buat feature branch Anda (`git checkout -b feature/AmazingFeature`)
3. Commit perubahan Anda (`git commit -m 'Add some AmazingFeature'`)
4. Push ke branch (`git push origin feature/AmazingFeature`)
5. Buka Pull Request

## 🔗 Links

- **NPM Package**: [npmjs.com/package/cuimp](https://www.npmjs.com/package/cuimp)
- **GitHub Repository**: [github.com/F4RAN/cuimp-ts](https://github.com/F4RAN/cuimp-ts)
- **curl-impersonate**: [github.com/lexiforest/curl-impersonate](https://github.com/lexiforest/curl-impersonate)
- **Issues & Bug Reports**: [github.com/F4RAN/cuimp-ts/issues](https://github.com/F4RAN/cuimp-ts/issues)

## 👥 Kontributor

Terima kasih kepada orang-orang luar biasa yang telah berkontribusi pada project ini:

<table>
  <tr>
    <td align="center">
      <a href="https://github.com/F4RAN">
        <img src="https://github.com/F4RAN.png" width="100px;" alt="F4RAN"/><br />
        <sub><b>F4RAN</b></sub>
      </a><br />
      <sub>Original Author & Maintainer</sub>
    </td>
    <td align="center">
      <a href="https://github.com/ma-joel">
        <img src="https://github.com/ma-joel.png" width="100px;" alt="ma-joel"/><br />
        <sub><b>ma-joel</b></sub>
      </a><br />
      <sub>CI, Encoding & Redirects</sub>
    </td>
    <td align="center">
      <a href="https://github.com/parigi-n">
        <img src="https://github.com/parigi-n.png" width="100px;" alt="parigi-n"/><br />
        <sub><b>parigi-n</b></sub>
      </a><br />
      <sub>Bug Fixes</sub>
    </td>
    <td align="center">
      <a href="https://github.com/niallo">
        <img src="https://github.com/niallo.png" width="100px;" alt="niallo"/><br />
        <sub><b>niallo</b></sub>
      </a><br />
      <sub>Streaming Request API</sub>
    </td>
    <td align="center">
      <a href="https://github.com/nvitaterna">
        <img src="https://github.com/nvitaterna.png" width="100px;" alt="nvitaterna"/><br />
        <sub><b>nvitaterna</b></sub>
      </a><br />
      <sub>Bug Fixes</sub>
    </td>
  </tr>
  <tr>
    <td align="center">
      <a href="https://github.com/tony13tv">
        <img src="https://github.com/tony13tv.png" width="100px;" alt="tony13tv"/><br />
        <sub><b>tony13tv</b></sub>
      </a><br />
      <sub>macOS Support</sub>
    </td>
    <td align="center">
      <a href="https://github.com/reyzzz">
        <img src="https://github.com/reyzzz.png" width="100px;" alt="reyzzz"/><br />
        <sub><b>reyzzz</b></sub>
      </a><br />
      <sub>HTTP/2 Headers Fix</sub>
    </td>
    <td align="center">
      <a href="https://github.com/kenmadev">
        <img src="https://github.com/kenmadev.png" width="100px;" alt="kenmadev"/><br />
        <sub><b>kenmadev</b></sub>
      </a><br />
      <sub>Windows Repro & Testing</sub>
    </td>
    <td align="center">
      <a href="https://github.com/pirasalbe">
        <img src="https://github.com/pirasalbe.png" width="100px;" alt="pirasalbe"/><br />
        <sub><b>pirasalbe</b></sub>
      </a><br />
      <sub>Raspberry Pi Support</sub>
    </td>
    <td align="center">
      <a href="https://github.com/vinikjkkj">
        <img src="https://github.com/vinikjkkj.png" width="100px;" alt="vinikjkkj"/><br />
        <sub><b>vinikjkkj</b></sub>
      </a><br />
      <sub>musl libc Detection (Linux)</sub>
    </td>
  </tr>
</table>

---

<div align="center">

**Jika Anda merasa project ini berguna, tolong beri ⭐️!**

Dibuat dengan ❤️ oleh komunitas Cuimp

</div>