/\*\*  
 \* LoRaWANForge Pro \- Industrial & Smart City LoRaWAN (v1.0.4 / v1.1) Protocol Engine  
 \* Standards: LoRaWAN 1.0.4 / 1.1 Specification, Regional Parameters RP002, RFC 4493 (AES-CMAC)  
 \* Author: Autonomous Digital Product Factory (Product \#170)  
 \* Version: 1.0.0  
 \*/

// Pure-JS AES-128 Core for Zero-Dependency Web Studio & Embedded Usage  
const AES128 \= (() \=\> {  
  const SBOX \= new Uint8Array(\[  
    0x63, 0x7c, 0x77, 0x7b, 0xf2, 0x6b, 0x6f, 0xc5, 0x30, 0x01, 0x67, 0x2b, 0xfe, 0xd7, 0xab, 0x76,  
    0xca, 0x82, 0xc9, 0x7d, 0xfa, 0x59, 0x47, 0xf0, 0xad, 0xd4, 0xa2, 0xaf, 0x9c, 0xa4, 0x72, 0xc0,  
    0xb7, 0xfd, 0x93, 0x26, 0x36, 0x3f, 0xf7, 0xcc, 0x34, 0xa5, 0xe5, 0xf1, 0x71, 0xd8, 0x31, 0x15,  
    0x04, 0xc7, 0x23, 0xc3, 0x18, 0x96, 0x05, 0x9a, 0x07, 0x12, 0x80, 0xe2, 0xeb, 0x27, 0xb2, 0x75,  
    0x09, 0x83, 0x2c, 0x1a, 0x1b, 0x6e, 0x5a, 0xa0, 0x52, 0x3b, 0xd6, 0xb3, 0x29, 0xe3, 0x2f, 0x84,  
    0x53, 0xd1, 0x00, 0xed, 0x20, 0xfc, 0xb1, 0x5b, 0x6a, 0xcb, 0xbe, 0x39, 0x4a, 0x4c, 0x58, 0xcf,  
    0xd0, 0xef, 0xaa, 0xfb, 0x43, 0x4d, 0x33, 0x85, 0x45, 0xf9, 0x02, 0x7f, 0x50, 0x3c, 0x9f, 0xa8,  
    0x51, 0xa3, 0x40, 0x8f, 0x92, 0x9d, 0x38, 0xf5, 0xbc, 0xb6, 0xda, 0x21, 0x10, 0xff, 0xf3, 0xd2,  
    0xcd, 0x0c, 0x13, 0xec, 0x5f, 0x97, 0x44, 0x17, 0xc4, 0xa7, 0x7e, 0x3d, 0x64, 0x5d, 0x19, 0x73,  
    0x60, 0x81, 0x4f, 0xdc, 0x22, 0x2a, 0x90, 0x88, 0x46, 0xee, 0xb8, 0x14, 0xde, 0x5e, 0x0b, 0xdb,  
    0xe0, 0x32, 0x3a, 0x0a, 0x49, 0x06, 0x24, 0x5e, 0xc2, 0xd3, 0xac, 0x62, 0x91, 0x95, 0xe4, 0x79,  
    0xe7, 0xc8, 0x37, 0x6d, 0x8d, 0xd5, 0x4e, 0xa9, 0x6c, 0x56, 0xf4, 0xea, 0x65, 0x7a, 0xae, 0x08,  
    0xba, 0x78, 0x25, 0x2e, 0x1c, 0xa6, 0xb4, 0xc6, 0xe8, 0xdd, 0x74, 0x1f, 0x4b, 0xbd, 0x8b, 0x8a,  
    0x70, 0x3e, 0xb5, 0x66, 0x48, 0x03, 0xf6, 0x0e, 0x61, 0x35, 0x57, 0xb9, 0x86, 0xc1, 0x1d, 0x9e,  
    0xe1, 0xf8, 0x98, 0x11, 0x69, 0xd9, 0x8e, 0x94, 0x9b, 0x1e, 0x87, 0xe9, 0xce, 0x55, 0x28, 0xdf,  
    0x8c, 0xa1, 0x89, 0x0d, 0xbf, 0xe6, 0x42, 0x68, 0x41, 0x99, 0x2d, 0x0f, 0xb0, 0x54, 0xbb, 0x16  
  \]);

  const RCON \= \[0x01, 0x02, 0x04, 0x08, 0x10, 0x20, 0x40, 0x80, 0x1b, 0x36\];

  function keyExpansion(key) {  
    const w \= new Uint32Array(44);  
    for (let i \= 0; i \< 4; i++) {  
      w\[i\] \= (key\[4 \* i\] \<\< 24\) \| (key\[4 \* i \+ 1\] \<\< 16\) \| (key\[4 \* i \+ 2\] \<\< 8\) \| key\[4 \* i \+ 3\];  
    }  
    for (let i \= 4; i \< 44; i++) {  
      let temp \= w\[i \- 1\];  
      if (i % 4 \=== 0\) {  
        temp \= ((temp \<\< 8\) \| (temp \>\>\> 24)) \>\>\> 0;  
        temp \= ((SBOX\[(temp \>\>\> 24\) & 0xff\] \<\< 24\) \|  
                (SBOX\[(temp \>\>\> 16\) & 0xff\] \<\< 16\) \|  
                (SBOX\[(temp \>\>\> 8\) & 0xff\] \<\< 8\) \|  
                SBOX\[temp & 0xff\]) \>\>\> 0;  
        temp ^= (RCON\[(i / 4\) \- 1\] \<\< 24\) \>\>\> 0;  
      }  
      w\[i\] \= (w\[i \- 4\] ^ temp) \>\>\> 0;  
    }  
    return w;  
  }

  function subBytes(s) { for (let i \= 0; i \< 16; i++) s\[i\] \= SBOX\[s\[i\]\]; }  
  function shiftRows(s) {  
    let t \= s\[1\]; s\[1\] \= s\[5\]; s\[5\] \= s\[9\]; s\[9\] \= s\[13\]; s\[13\] \= t;  
    t \= s\[2\]; s\[2\] \= s\[10\]; s\[10\] \= t; t \= s\[6\]; s\[6\] \= s\[14\]; s\[14\] \= t;  
    t \= s\[15\]; s\[15\] \= s\[11\]; s\[11\] \= s\[7\]; s\[7\] \= s\[3\]; s\[3\] \= t;  
  }  
  function gmul(a, b) {  
    let p \= 0;  
    for (let c \= 0; c \< 8; c++) {  
      if (b & 1\) p ^= a;  
      const hi \= a & 0x80;  
      a \= (a \<\< 1\) & 0xff;  
      if (hi) a ^= 0x1b;  
      b \>\>= 1;  
    }  
    return p;  
  }  
  function mixColumns(s) {  
    for (let c \= 0; c \< 4; c++) {  
      const idx \= c \* 4;  
      const a0 \= s\[idx\], a1 \= s\[idx+1\], a2 \= s\[idx+2\], a3 \= s\[idx+3\];  
      s\[idx\]   \= gmul(a0, 2\) ^ gmul(a1, 3\) ^ a2 ^ a3;  
      s\[idx+1\] \= a0 ^ gmul(a1, 2\) ^ gmul(a2, 3\) ^ a3;  
      s\[idx+2\] \= a0 ^ a1 ^ gmul(a2, 2\) ^ gmul(a3, 3);  
      s\[idx+3\] \= gmul(a0, 3\) ^ a1 ^ a2 ^ gmul(a3, 2);  
    }  
  }  
  function addRoundKey(s, w, rnd) {  
    for (let c \= 0; c \< 4; c++) {  
      const kw \= w\[rnd \* 4 \+ c\];  
      s\[c \* 4\]     ^= (kw \>\>\> 24\) & 0xff;  
      s\[c \* 4 \+ 1\] ^= (kw \>\>\> 16\) & 0xff;  
      s\[c \* 4 \+ 2\] ^= (kw \>\>\> 8\) & 0xff;  
      s\[c \* 4 \+ 3\] ^= kw & 0xff;  
    }  
  }

  function encryptBlock(input, key) {  
    const w \= keyExpansion(key);  
    const s \= new Uint8Array(16);  
    // State is column-major  
    for (let r \= 0; r \< 4; r++) {  
      for (let c \= 0; c \< 4; c++) {  
        s\[c \* 4 \+ r\] \= input\[r \* 4 \+ c\];  
      }  
    }  
    addRoundKey(s, w, 0);  
    for (let rnd \= 1; rnd \< 10; rnd++) {  
      subBytes(s);  
      shiftRows(s);  
      mixColumns(s);  
      addRoundKey(s, w, rnd);  
    }  
    subBytes(s);  
    shiftRows(s);  
    addRoundKey(s, w, 10);

    const out \= new Uint8Array(16);  
    for (let r \= 0; r \< 4; r++) {  
      for (let c \= 0; c \< 4; c++) {  
        out\[r \* 4 \+ c\] \= s\[c \* 4 \+ r\];  
      }  
    }  
    return out;  
  }

  return { encryptBlock };  
})();

class LoRaWANEngine {  
  constructor() {  
    this.MTYPES \= {  
      0: 'Join-Request',  
      1: 'Join-Accept',  
      2: 'Unconfirmed Data Up',  
      3: 'Unconfirmed Data Down',  
      4: 'Confirmed Data Up',  
      5: 'Confirmed Data Down',  
      6: 'Rejoin-Request',  
      7: 'Proprietary'  
    };

    this.REGIONAL\_BANDS \= {  
      EU868: { name: 'EU868 (Europe)', defaultFreq: 868.1, dutyCycle: 0.01, maxEirp: 16 },  
      US915: { name: 'US915 (USA)', defaultFreq: 902.3, dutyCycle: 1.0, maxEirp: 30 },  
      AS923: { name: 'AS923 (Asia)', defaultFreq: 923.2, dutyCycle: 0.01, maxEirp: 16 },  
      AU915: { name: 'AU915 (Australia)', defaultFreq: 915.2, dutyCycle: 1.0, maxEirp: 30 }  
    };  
  }

  // \--- LoRa PHY Airtime Math (Semtech SX1276/SX1262 Formulations) \---  
  calculateAirtime(payloadBytes, sf \= 7, bwKHz \= 125, cr \= 1, preambleLen \= 8, crc \= true, ih \= false) {  
    const bw \= bwKHz \* 1000;  
    const tSymbol \= (Math.pow(2, sf) / bw) \* 1000; // ms  
    const tPreamble \= (preambleLen \+ 4.25) \* tSymbol;

    const de \= (sf \>= 11 && bwKHz \=== 125\) ? 1 : 0; // Low Data Rate Optimization  
    const crcVal \= crc ? 1 : 0;  
    const ihVal \= ih ? 1 : 0;

    const num \= 8 \* payloadBytes \- 4 \* sf \+ 28 \+ 16 \* crcVal \- 20 \* ihVal;  
    const den \= 4 \* (sf \- 2 \* de);  
    const ceilTerm \= Math.max(0, Math.ceil(num / den));  
    const nPayload \= 8 \+ ceilTerm \* (cr \+ 4);  
    const tPayload \= nPayload \* tSymbol;  
    const totalAirtime \= tPreamble \+ tPayload;

    return {  
      tSymbolMs: tSymbol,  
      tPreambleMs: tPreamble,  
      tPayloadMs: tPayload,  
      totalAirtimeMs: totalAirtime,  
      sf,  
      bwKHz,  
      dutyCycleMaxPacketsPerHour: Math.floor((3600 \* 1000 \* 0.01) / totalAirtime) // For 1% duty cycle  
    };  
  }

  // \--- AES-128 CMAC (RFC 4493\) for LoRaWAN Message Integrity Code (MIC) \---  
  aesCmac(key, message) {  
    const const\_Zero \= new Uint8Array(16);  
    const L \= AES128.encryptBlock(const\_Zero, key);

    function shiftLeft(b) {  
      const out \= new Uint8Array(16);  
      let overflow \= 0;  
      for (let i \= 15; i \>= 0; i--) {  
        out\[i\] \= ((b\[i\] \<\< 1\) \| overflow) & 0xff;  
        overflow \= (b\[i\] & 0x80) ? 1 : 0;  
      }  
      return { out, overflow };  
    }

    // Subkey K1  
    let s1 \= shiftLeft(L);  
    let K1 \= s1.out;  
    if (s1.overflow) K1\[15\] ^= 0x87;

    // Subkey K2  
    let s2 \= shiftLeft(K1);  
    let K2 \= s2.out;  
    if (s2.overflow) K2\[15\] ^= 0x87;

    const n \= Math.ceil(message.length / 16\) \|\| 1;  
    const flag \= (message.length \> 0 && message.length % 16 \=== 0);

    const M\_last \= new Uint8Array(16);  
    if (flag) {  
      const start \= (n \- 1\) \* 16;  
      for (let i \= 0; i \< 16; i++) M\_last\[i\] \= message\[start \+ i\] ^ K1\[i\];  
    } else {  
      const start \= (n \- 1\) \* 16;  
      const rem \= message.length \- start;  
      for (let i \= 0; i \< rem; i++) M\_last\[i\] \= message\[start \+ i\];  
      M\_last\[rem\] \= 0x80; // Padding  
      for (let i \= 0; i \< 16; i++) M\_last\[i\] ^= K2\[i\];  
    }

    let X \= new Uint8Array(16);  
    let Y \= new Uint8Array(16);  
    for (let i \= 0; i \< n \- 1; i++) {  
      const blk \= message.slice(i \* 16, (i \+ 1\) \* 16);  
      for (let j \= 0; j \< 16; j++) Y\[j\] \= X\[j\] ^ blk\[j\];  
      X \= AES128.encryptBlock(Y, key);  
    }  
    for (let j \= 0; j \< 16; j++) Y\[j\] \= X\[j\] ^ M\_last\[j\];  
    return AES128.encryptBlock(Y, key);  
  }

  // \--- Calculate LoRaWAN Data Message MIC (4 Bytes) \---  
  calculateMic(key, devAddr, fcnt, dir, payloadWithoutMic) {  
    // B0 block (16 bytes)  
    const b0 \= new Uint8Array(16);  
    b0\[0\] \= 0x49;  
    b0\[5\] \= dir & 0x01; // 0 for uplink, 1 for downlink  
    // DevAddr (4 bytes Little Endian)  
    b0\[6\] \= devAddr\[0\];  
    b0\[7\] \= devAddr\[1\];  
    b0\[8\] \= devAddr\[2\];  
    b0\[9\] \= devAddr\[3\];  
    // FCnt (4 bytes Little Endian)  
    b0\[10\] \= fcnt & 0xff;  
    b0\[11\] \= (fcnt \>\> 8\) & 0xff;  
    b0\[12\] \= (fcnt \>\> 16\) & 0xff;  
    b0\[13\] \= (fcnt \>\> 24\) & 0xff;  
    b0\[15\] \= payloadWithoutMic.length;

    const cmacInput \= new Uint8Array(16 \+ payloadWithoutMic.length);  
    cmacInput.set(b0, 0);  
    cmacInput.set(payloadWithoutMic, 16);

    const fullCmac \= this.aesCmac(key, cmacInput);  
    return fullCmac.slice(0, 4); // First 4 bytes  
  }

  // \--- AES-128 CTR Payload Cipher (LoRaWAN FRMPayload encryption) \---  
  cipherPayload(appSKey, devAddr, fcnt, dir, dataBytes) {  
    if (\!dataBytes \|\| dataBytes.length \=== 0\) return new Uint8Array(0);

    const k \= Math.ceil(dataBytes.length / 16);  
    const cipherText \= new Uint8Array(dataBytes.length);

    for (let i \= 1; i \<= k; i++) {  
      const ai \= new Uint8Array(16);  
      ai\[0\] \= 0x01;  
      ai\[5\] \= dir & 0x01;  
      ai\[6\] \= devAddr\[0\];  
      ai\[7\] \= devAddr\[1\];  
      ai\[8\] \= devAddr\[2\];  
      ai\[9\] \= devAddr\[3\];  
      ai\[10\] \= fcnt & 0xff;  
      ai\[11\] \= (fcnt \>\> 8\) & 0xff;  
      ai\[12\] \= (fcnt \>\> 16\) & 0xff;  
      ai\[13\] \= (fcnt \>\> 24\) & 0xff;  
      ai\[15\] \= i & 0xff;

      const sBlock \= AES128.encryptBlock(ai, appSKey);  
      const start \= (i \- 1\) \* 16;  
      const end \= Math.min(start \+ 16, dataBytes.length);  
      for (let j \= start; j \< end; j++) {  
        cipherText\[j\] \= dataBytes\[j\] ^ sBlock\[j \- start\];  
      }  
    }  
    return cipherText;  
  }

  // \--- Frame Builder: Synthesizes valid LoRaWAN Data Uplink \---  
  buildUplink(devAddrHex, fcnt, fport, payloadHex, nwkSKeyHex, appSKeyHex, isConfirmed \= false) {  
    const devAddr \= this.hexToBytes(devAddrHex);  
    const nwkSKey \= this.hexToBytes(nwkSKeyHex);  
    const appSKey \= this.hexToBytes(appSKeyHex);  
    const clearPayload \= this.hexToBytes(payloadHex);

    // Encrypt payload  
    const encPayload \= this.cipherPayload(appSKey, devAddr, fcnt, 0, clearPayload);

    // MHDR: MType (4 for Confirmed Up, 2 for Unconfirmed Up)  
    const mtype \= isConfirmed ? 4 : 2;  
    const mhdr \= (mtype \<\< 5\) & 0xe0;

    // FHDR  
    const fctrl \= 0x00; // ADR=0, ACK=0, FOptsLen=0  
    const fcntLsb \= fcnt & 0xffff;  
    const fhdr \= \[  
      devAddr\[0\], devAddr\[1\], devAddr\[2\], devAddr\[3\],  
      fctrl,  
      fcntLsb & 0xff, (fcntLsb \>\> 8\) & 0xff  
    \];

    const macPayload \= \[mhdr, ...fhdr, fport, ...encPayload\];  
    const mic \= this.calculateMic(nwkSKey, devAddr, fcnt, 0, new Uint8Array(macPayload));

    const fullFrame \= new Uint8Array(\[...macPayload, ...mic\]);  
    return {  
      rawBytes: fullFrame,  
      hexString: this.bytesToHex(fullFrame),  
      base64: this.bytesToBase64(fullFrame),  
      micHex: this.bytesToHex(mic)  
    };  
  }

  // \--- Dissector \---  
  dissect(rawBytes) {  
    if (rawBytes.length \< 12\) {  
      throw new Error('Frame too short for valid LoRaWAN PHYPayload (min 12 bytes)');  
    }

    const mhdr \= rawBytes\[0\];  
    const mtype \= (mhdr \>\> 5\) & 0x07;  
    const major \= mhdr & 0x03;

    const res \= {  
      mhdr: {  
        raw: \`0x\${mhdr.toString(16).padStart(2, '0')}\`,  
        mtype: \`\${this.MTYPES\[mtype\]} (\${mtype})\`,  
        mtypeCode: mtype,  
        major: \`LoRaWAN R\${major \+ 1}\`  
      }  
    };

    if (mtype \=== 0\) { // Join-Request  
      const joinEui \= rawBytes.slice(1, 9);  
      const devEui \= rawBytes.slice(9, 17);  
      const devNonce \= (rawBytes\[17\] \| (rawBytes\[18\] \<\< 8));  
      const mic \= rawBytes.slice(19, 23);

      res.joinRequest \= {  
        joinEui: this.bytesToHex(joinEui),  
        devEui: this.bytesToHex(devEui),  
        devNonce: \`0x\${devNonce.toString(16).padStart(4, '0')} (\${devNonce})\`,  
        mic: this.bytesToHex(mic)  
      };  
    } else if (mtype \>= 2 && mtype \<= 5\) { // Data frames  
      const devAddr \= rawBytes.slice(1, 5);  
      const fctrl \= rawBytes\[5\];  
      const adr \= Boolean(fctrl & 0x80);  
      const adrAckReq \= Boolean(fctrl & 0x40);  
      const ack \= Boolean(fctrl & 0x20);  
      const foptsLen \= fctrl & 0x0f;  
      const fcnt \= rawBytes\[6\] \| (rawBytes\[7\] \<\< 8);

      const fopts \= rawBytes.slice(8, 8 \+ foptsLen);  
      let offset \= 8 \+ foptsLen;

      let fport \= null;  
      let frmPayload \= new Uint8Array(0);  
      if (rawBytes.length \- 4 \> offset) {  
        fport \= rawBytes\[offset\];  
        offset \+= 1;  
        frmPayload \= rawBytes.slice(offset, rawBytes.length \- 4);  
      }  
      const mic \= rawBytes.slice(rawBytes.length \- 4);

      res.fhdr \= {  
        devAddr: this.bytesToHex(devAddr),  
        fcnt,  
        adr,  
        adrAckReq,  
        ack,  
        foptsLen,  
        foptsHex: this.bytesToHex(fopts)  
      };

      res.macPayload \= {  
        fport: fport \!== null ? fport : 'None',  
        payloadLength: frmPayload.length,  
        payloadCipherHex: this.bytesToHex(frmPayload),  
        mic: this.bytesToHex(mic)  
      };  
    }

    return res;  
  }

  // \--- Semtech UDP Packet Forwarder JSON Generator \---  
  generateSemtechJson(phyBytes, freq \= 868.1, sf \= 7, bw \= 125\) {  
    const airtime \= this.calculateAirtime(phyBytes.length, sf, bw);  
    const rxpk \= {  
      tmst: Math.floor(Date.now() \* 1000\) & 0xffffffff,  
      time: new Date().toISOString(),  
      chan: 0,  
      rfch: 0,  
      freq: freq,  
      stat: 1,  
      modu: "LORA",  
      datr: \`SF\${sf}BW\${bw}\`,  
      codr: "4/5",  
      lsnr: 9.5,  
      rssi: \-72,  
      size: phyBytes.length,  
      data: this.bytesToBase64(phyBytes)  
    };  
    return { rxpk: \[rxpk\], airtimeMs: airtime.totalAirtimeMs };  
  }

  // \--- Wireshark Libpcap 2.4 Exporter (UDP Port 1700 \- Semtech Forwarder) \---  
  exportPcap(packets) {  
    const PCAP\_MAGIC \= 0xA1B2C3D4;  
    const PCAP\_SNAPLEN \= 65535;  
    const LINKTYPE\_ETHERNET \= 1;

    let totalSize \= 24;  
    for (const pkt of packets) {  
      const frameLen \= 14 \+ 20 \+ 8 \+ pkt.length;  
      totalSize \+= 16 \+ frameLen;  
    }

    const buffer \= new ArrayBuffer(totalSize);  
    const view \= new DataView(buffer);  
    const bytes \= new Uint8Array(buffer);

    view.setUint32(0, PCAP\_MAGIC, true);  
    view.setUint16(4, 2, true);  
    view.setUint16(6, 4, true);  
    view.setInt32(8, 0, true);  
    view.setUint32(12, 0, true);  
    view.setUint32(16, PCAP\_SNAPLEN, true);  
    view.setUint32(20, LINKTYPE\_ETHERNET, true);

    let offset \= 24;  
    const baseTime \= Math.floor(Date.now() / 1000);

    packets.forEach((pkt, idx) \=\> {  
      const frameLen \= 14 \+ 20 \+ 8 \+ pkt.length;  
      view.setUint32(offset, baseTime \+ idx, true);  
      view.setUint32(offset \+ 4, idx \* 50000, true);  
      view.setUint32(offset \+ 8, frameLen, true);  
      view.setUint32(offset \+ 12, frameLen, true);  
      offset \+= 16;

      const eth \= \[0x00, 0x16, 0xc0, 0x12, 0x34, 0x56, 0x00, 0x0c, 0x29, 0xab, 0xcd, 0xef, 0x08, 0x00\];  
      bytes.set(eth, offset);  
      offset \+= 14;

      const ipLen \= 20 \+ 8 \+ pkt.length;  
      const ip \= \[  
        0x45, 0x00, (ipLen \>\> 8\) & 0xff, ipLen & 0xff,  
        0x12, 0x34, 0x40, 0x00,  
        0x40, 0x11, 0x00, 0x00,  
        192, 168, 1, 50, // Gateway  
        192, 168, 1, 10  // LNS Server  
      \];  
      bytes.set(ip, offset);  
      offset \+= 20;

      const udpLen \= 8 \+ pkt.length;  
      const udp \= \[  
        (1700 \>\> 8\) & 0xff, 1700 & 0xff,  
        (1700 \>\> 8\) & 0xff, 1700 & 0xff,  
        (udpLen \>\> 8\) & 0xff, udpLen & 0xff,  
        0x00, 0x00  
      \];  
      bytes.set(udp, offset);  
      offset \+= 8;

      bytes.set(pkt, offset);  
      offset \+= pkt.length;  
    });

    return bytes;  
  }

  // \--- Utilities \---  
  hexToBytes(hex) {  
    const clean \= hex.replace(/\[\\s\\-\_:\\n\\r\]/g, '');  
    if (clean.length % 2 \!== 0\) throw new Error('Invalid hex string length');  
    const bytes \= new Uint8Array(clean.length / 2);  
    for (let i \= 0; i \< clean.length; i \+= 2\) {  
      bytes\[i / 2\] \= parseInt(clean.substr(i, 2), 16);  
    }  
    return bytes;  
  }

  bytesToHex(bytes) {  
    return Array.from(bytes).map(b \=\> b.toString(16).padStart(2, '0')).join(' ');  
  }

  bytesToBase64(bytes) {  
    let binary \= '';  
    for (let i \= 0; i \< bytes.byteLength; i++) {  
      binary \+= String.fromCharCode(bytes\[i\]);  
    }  
    return btoa(binary);  
  }  
}

if (typeof module \!== 'undefined' && module.exports) {  
  module.exports \= { LoRaWANEngine };  
}  
