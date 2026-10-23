\# LoRaWANForge Pro (v1.0.0) — Technical Architecture

\#\# 1\. System Overview  
\*\*LoRaWANForge Pro\*\* is a zero-dependency protocol engineering platform, physical layer airtime and duty-cycle calculator, MAC layer frame synthesizer and dissector, AES-128 cryptographic engine (CMAC MIC and CTR payload cipher), and LoRa Alliance zero-trust cybersecurity auditor for \*\*LoRaWAN (v1.0.4 / v1.1)\*\* networks.

LoRaWAN is the dominant Low-Power Wide Area Network (LPWAN) standard connecting hundreds of millions of smart city utility meters (water, gas, electricity), agricultural telemetry nodes, environmental sensors, asset trackers, and industrial IoT devices across public and private networks (The Things Network, AWS IoT Core for LoRaWAN, ChirpStack, Senet, Actility).

LoRaWANForge Pro addresses critical development, certification, and cybersecurity challenges by providing clean offline-first tooling for exact PHY airtime modeling, message integrity calculation, payload decryption, Semtech UDP packet forwarder JSON generation, and automated gateway defense synthesis.

\---

\#\# 2\. Protocol Architecture & Layer Encapsulation

\`\`\`  
\+---------------------------------------------------------------+  
\| Layer 7: Application Data / Sensors (FRMPayload)             \|  
\| Encrypted via AES-128 CTR using AppSKey                       \|  
\+---------------------------------------------------------------+  
\| Layer 2b: LoRaWAN MAC Layer (MACPayload / FHDR / FOpts / MIC) \|  
\| MHDR \| DevAddr (4B) \| FCtrl (1B) \| FCnt (2B) \| FOpts \| FPort  \|  
\| 4-Byte MIC calculated via AES-128 CMAC using NwkSKey          \|  
\+---------------------------------------------------------------+  
\| Layer 2a: Gateway Protocol (Semtech UDP 1700 / Basic Station) \|  
\| JSON 'rxpk' Telemetry: Time, Freq, SF, BW, RSSI, SNR, Base64  \|  
\+---------------------------------------------------------------+  
\| Layer 1: LoRa Physical Layer (Chirp Spread Spectrum \- CSS)   \|  
\| Spreading Factor (SF7..SF12), Bandwidth (125/250/500 kHz)     \|  
\| Preamble (8-65535 symbols) \| Coding Rate (4/5..4/8)           \|  
\+---------------------------------------------------------------+  
\`\`\`

\---

\#\# 3\. Physical Layer (PHY) Airtime Formulation

LoRa modulation utilizes Chirp Spread Spectrum (CSS) where symbols are swept linearly across the configured bandwidth.

\#\#\# 3.1 Symbol Duration (\$T\_s\$)  
\$\$T\_s \= \\frac{2^{\\text{SF}}}{\\text{BW}}\$\$  
\- For SF7, BW \= 125 kHz: \$T\_s \= \\frac{128}{125000} \= 1.024\\text{ ms}\$.  
\- For SF12, BW \= 125 kHz: \$T\_s \= \\frac{4096}{125000} \= 32.768\\text{ ms}\$.

\#\#\# 3.2 Preamble Duration (\$T\_{\\text{preamble}}\$)  
\$\$T\_{\\text{preamble}} \= (N\_{\\text{preamble}} \+ 4.25) \\times T\_s\$\$  
Default \$N\_{\\text{preamble}} \= 8\$ symbols results in \$12.25 \\times T\_s\$ (\$12.544\\text{ ms}\$ at SF7).

\#\#\# 3.3 Payload Symbol Count (\$N\_{\\text{payload}}\$)  
\$\$N\_{\\text{payload}} \= 8 \+ \\max\\left( \\left\\lceil \\frac{8 \\times PL \- 4 \\times SF \+ 28 \+ 16 \\times CRC \- 20 \\times IH}{4 \\times (SF \- 2 \\times DE)} \\right\\rceil \\times (CR \+ 4), 0 \\right)\$\$  
Where:  
\- \$PL\$: PHY payload length in bytes.  
\- \$SF\$: Spreading factor (\$7 \\le SF \\le 12\$).  
\- \$CRC\$: 1 for uplinks with CRC enabled; 0 for downlinks.  
\- \$IH\$: 0 for explicit header mode.  
\- \$DE\$: Low Data Rate Optimization (1 for \$SF \\ge 11\$ at 125 kHz; else 0).  
\- \$CR\$: Coding rate index (1 for 4/5, 2 for 4/6, etc.).

\#\#\# 3.4 Regulatory Duty-Cycle Limits (EU868 & US915)  
\- \*\*EU868\*\*: Sub-bands impose a 1.0% statutory duty-cycle limit (\$36\\text{ seconds/hour}\$ airtime budget).  
\- \*\*US915\*\*: FCC Part 15.247 imposes a maximum 400 ms channel dwell time limit.

\---

\#\# 4\. MAC Layer Cryptography & Integrity

\#\#\# 4.1 Message Integrity Code (MIC) via AES-128 CMAC  
The 4-byte MIC is computed per RFC 4493 using the Network Session Key (\$\\text{NwkSKey}\$) over block \$B\_0\$ concatenated with the MAC message:  
\$\$B\_0 \= \[0x49, 0x00, 0x00, 0x00, 0x00, \\text{Dir}, \\text{DevAddr}\_{0..3}, \\text{FCnt}\_{0..3}, 0x00, \\text{len}(\\text{msg})\]\$\$  
\$\$\\text{FullCMAC} \= \\text{AES-128-CMAC}(\\text{NwkSKey}, B\_0 \\,\\\|\\, \\text{msg})\$\$  
\$\$\\text{MIC} \= \\text{FullCMAC}\[0..3\]\$\$

\#\#\# 4.2 Application Payload Encryption via AES-128 CTR  
Payload confidentiality is achieved by encrypting \$\\text{FRMPayload}\$ with \$\\text{AppSKey}\$ in Counter (CTR) mode with blocks \$A\_i\$:  
\$\$A\_i \= \[0x01, 0x00, 0x00, 0x00, 0x00, \\text{Dir}, \\text{DevAddr}\_{0..3}, \\text{FCnt}\_{0..3}, 0x00, i\]\$\$  
\$\$S\_i \= \\text{AES-128}(\\text{AppSKey}, A\_i)\$\$  
\$\$\\text{CipherBlock}\_i \= \\text{DataBlock}\_i \\oplus S\_i\$\$

\---

\#\# 5\. Security Architecture (LoRa Alliance & Zero-Trust)  
LoRaWANForge Pro evaluates deployments against 10 critical zero-trust criteria:  
1\. \*\*Root Key Entropy\*\*: Detection of default/sequential 128-bit AppKeys.  
2\. \*\*DevNonce Tracking\*\*: Monotonic sequence enforcement preventing Join-Request replay attacks.  
3\. \*\*Activation Model\*\*: Enforcing dynamic OTAA over static ABP.  
4\. \*\*Frame Counter Check\*\*: Monotonic 32-bit FCnt rollover auditing.  
5\. \*\*Gateway Backhaul Security\*\*: Mandating Basic Station TLS 1.3 over cleartext Semtech UDP 1700\.  
6\. \*\*Airtime Compliance\*\*: Auditing against EU868 1% and US915 FCC limits.  
7\. \*\*Key Separation\*\*: Cryptographic independence of \$\\text{NwkSKey}\$ and \$\\text{AppSKey}\$.  
8\. \*\*FOpts Integrity\*\*: Validation of downlink MAC commands.  
9\. \*\*Gateway Authentication\*\*: Token and client TLS validation on LNS interfaces.  
10\. \*\*Class Profile Validation\*\*: Preventing accidental Class C configuration on battery endpoints.  
