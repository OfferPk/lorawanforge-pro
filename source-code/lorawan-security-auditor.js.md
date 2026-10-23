/\*\*  
 \* LoRaWANForge Pro \- LoRa Alliance & Zero-Trust IoT Cybersecurity Auditor  
 \* Standards: LoRaWAN 1.0.4 / 1.1 Security Principles, NIST SP 800-82, ENISA IoT Guidelines  
 \* Author: Autonomous Digital Product Factory (Product \#170)  
 \* Version: 1.0.0  
 \*/

class LoRaWANSecurityAuditor {  
  constructor() {  
    this.RULES \= \[  
      {  
        id: 'LORA-SEC-01',  
        title: 'Hardcoded or Low-Entropy Root AppKey in Firmware',  
        standard: 'LoRaWAN 1.0.4 / 1.1 Spec Section 6.2 (Key Management)',  
        severity: 'CRITICAL',  
        weight: 20,  
        description: 'Using factory-default, sequential, or hardcoded 128-bit AppKeys allows adversaries to extract the root key and compromise all session keys (NwkSKey, AppSKey).',  
        remediation: 'Provision cryptographically random, unique 128-bit AppKeys per device stored in hardware Secure Elements (e.g. ATECC608A / Microchip).'  
      },  
      {  
        id: 'LORA-SEC-02',  
        title: 'DevNonce Monotonic Increment Enforcement (Replay Protection)',  
        standard: 'LoRaWAN 1.0.4 Section 6.2.2 / LoRaWAN 1.1 Section 6.2.2',  
        severity: 'CRITICAL',  
        weight: 20,  
        description: 'Random or unvalidated DevNonces permit attackers to replay recorded Join-Requests, knocking active sensors offline and triggering denial of service.',  
        remediation: 'Ensure the Network Server (LNS) tracks all previously used DevNonces and enforces strict monotonic incrementing per LoRaWAN 1.0.4+ specifications.'  
      },  
      {  
        id: 'LORA-SEC-03',  
        title: 'Activation by Personalization (ABP) vs. Dynamic OTAA',  
        standard: 'LoRa Alliance Security Whitepaper Section 3 (Session Lifecycle)',  
        severity: 'HIGH',  
        weight: 15,  
        description: 'ABP nodes use static NwkSKey/AppSKey that never rotate over the device lifetime, exposing telemetry to long-term key compromise if extracted.',  
        remediation: 'Migrate production nodes to Over-The-Air Activation (OTAA) to generate ephemeral session keys on each device boot.'  
      },  
      {  
        id: 'LORA-SEC-04',  
        title: 'Frame Counter (FCnt) Check Bypass & Reset Acceptance',  
        standard: 'LoRaWAN 1.0.4 Section 4.3.1.5 (Replay Protection)',  
        severity: 'HIGH',  
        weight: 15,  
        description: 'Disabling frame counter validation in LNS allows replay of recorded uplinks and sensor state spoofing.',  
        remediation: 'Enforce strict 32-bit monotonic FCnt validation; never disable "Resets Frame-Counters" on production Network Server profiles.'  
      },  
      {  
        id: 'LORA-SEC-05',  
        title: 'Unencrypted Gateway Backhaul (Semtech UDP 1700 Exposure)',  
        standard: 'NIST SP 800-82 / ETSI EN 303 645 (Secure Communications)',  
        severity: 'HIGH',  
        weight: 10,  
        description: 'Legacy Semtech UDP Packet Forwarder transmits radio frames, GPS coordinates, and metadata over cleartext UDP port 1700 without authentication or TLS.',  
        remediation: 'Upgrade gateways to Semtech Basic Station protocol utilizing TLS 1.3 mutual authentication (WSS port 8887\) or IPSec/VPN tunnels.'  
      },  
      {  
        id: 'LORA-SEC-06',  
        title: 'Airtime & Duty-Cycle Compliance (EU868 1% / US915 FCC)',  
        standard: 'ETSI EN 300 220 / FCC Title 47 Part 15.247',  
        severity: 'MEDIUM',  
        weight: 5,  
        description: 'Excessive uplink frequency or long payloads at high spreading factors (SF11/SF12) violate statutory 1% duty-cycle limits and jam local spectrum.',  
        remediation: 'Implement adaptive data rating (ADR), compress payloads, and throttle transmission intervals to remain below statutory airtime caps.'  
      },  
      {  
        id: 'LORA-SEC-07',  
        title: 'NwkSKey & AppSKey Cryptographic Separation',  
        standard: 'LoRaWAN 1.1 Specification Section 6.1 (Key Derivation)',  
        severity: 'MEDIUM',  
        weight: 5,  
        description: 'Reusing the same 128-bit key for both network integrity (NwkSKey) and application payload encryption (AppSKey) violates separation of concerns.',  
        remediation: 'Derive cryptographically distinct keys using KDF mechanisms and maintain AppSKey exclusively in the application server.'  
      },  
      {  
        id: 'LORA-SEC-08',  
        title: 'Unauthenticated FOpts MAC Command Injection',  
        standard: 'LoRaWAN 1.0.4 Section 5 (MAC Commands)',  
        severity: 'MEDIUM',  
        weight: 4,  
        description: 'Failure to verify downlink MAC command signatures allows rogue RF transmissions to manipulate transmitter power (LinkADRReq) or duty cycles.',  
        remediation: 'Validate that all downlinks carrying FOpts commands have valid AES-128 CMAC MIC signatures verified against NwkSKey.'  
      },  
      {  
        id: 'LORA-SEC-09',  
        title: 'Gateway EUI Impersonation & Lack of API Authentication',  
        standard: 'LoRa Alliance Gateway Architecture Guidelines',  
        severity: 'LOW',  
        weight: 3,  
        description: 'Allowing gateways to register on LNS using only an unauthenticated 64-bit Gateway EUI enables rogue gateway telemetry spoofing.',  
        remediation: 'Require secure gateway API tokens or client TLS certificates for all gateway connections to the Network Server.'  
      },  
      {  
        id: 'LORA-SEC-10',  
        title: 'Inadvertent Class C Configuration on Battery Endpoints',  
        standard: 'LoRaWAN Specification Section 17 (Class C Operation)',  
        severity: 'LOW',  
        weight: 3,  
        description: 'Configuring battery-operated sensors in Class C continuous listening mode exhausts battery reserves within days, causing node failure.',  
        remediation: 'Enforce Class A operation for all battery-powered sensor endpoints; reserve Class C strictly for mains-powered actuators.'  
      }  
    \];  
  }

  auditConfiguration(config \= {}) {  
    const findings \= \[\];  
    let deduction \= 0;

    // 1\. Root Key Entropy  
    if (config.hasDefaultAppKey) {  
      findings.push({ ...this.RULES\[0\], status: 'FAIL', current: 'Default / Predictable AppKey Found' });  
      deduction \+= this.RULES\[0\].weight;  
    } else {  
      findings.push({ ...this.RULES\[0\], status: 'PASS', current: 'Unique Hardware-Provisioned 128-bit AppKey' });  
    }

    // 2\. DevNonce Validation  
    if (\!config.hasMonotonicDevNonce) {  
      findings.push({ ...this.RULES\[1\], status: 'FAIL', current: 'DevNonce Reuse Not Blocked by LNS' });  
      deduction \+= this.RULES\[1\].weight;  
    } else {  
      findings.push({ ...this.RULES\[1\], status: 'PASS', current: 'Monotonic DevNonce Tracking Enforced' });  
    }

    // 3\. OTAA vs ABP  
    if (config.usesAbp) {  
      findings.push({ ...this.RULES\[2\], status: 'FAIL', current: 'Static ABP Keys in Production Firmware' });  
      deduction \+= this.RULES\[2\].weight;  
    } else {  
      findings.push({ ...this.RULES\[2\], status: 'PASS', current: 'Dynamic OTAA Activation Active' });  
    }

    // 4\. FCnt Validation  
    if (config.allowFcntReset) {  
      findings.push({ ...this.RULES\[3\], status: 'FAIL', current: 'FCnt Reset Bypass Allowed on Server' });  
      deduction \+= this.RULES\[3\].weight;  
    } else {  
      findings.push({ ...this.RULES\[3\], status: 'PASS', current: 'Strict 32-bit Monotonic FCnt Enforced' });  
    }

    // 5\. Backhaul Security  
    if (\!config.hasTlsBackhaul) {  
      findings.push({ ...this.RULES\[4\], status: 'FAIL', current: 'Cleartext Semtech UDP 1700 in Use' });  
      deduction \+= this.RULES\[4\].weight;  
    } else {  
      findings.push({ ...this.RULES\[4\], status: 'PASS', current: 'Basic Station TLS 1.3 / WSS Backhaul Active' });  
    }

    // 6\. Airtime Duty Cycle  
    if (config.exceedsDutyCycle) {  
      findings.push({ ...this.RULES\[5\], status: 'FAIL', current: 'Airtime Exceeds Statutory 1% Band Cap' });  
      deduction \+= this.RULES\[5\].weight;  
    } else {  
      findings.push({ ...this.RULES\[5\], status: 'PASS', current: 'Within Regional Duty Cycle Budget' });  
    }

    // 7\. Key Separation  
    if (\!config.hasKeySeparation) {  
      findings.push({ ...this.RULES\[6\], status: 'FAIL', current: 'NwkSKey and AppSKey Identical' });  
      deduction \+= this.RULES\[6\].weight;  
    } else {  
      findings.push({ ...this.RULES\[6\], status: 'PASS', current: 'Independent Network & Application Keys' });  
    }

    // 8\. FOpts Validation  
    if (\!config.validatesFopts) {  
      findings.push({ ...this.RULES\[7\], status: 'FAIL', current: 'Downlink MAC Commands Unchecked' });  
      deduction \+= this.RULES\[7\].weight;  
    } else {  
      findings.push({ ...this.RULES\[7\], status: 'PASS', current: 'AES-128 CMAC Signed Downlink FOpts Only' });  
    }

    // 9\. Gateway Authentication  
    if (\!config.hasGatewayAuth) {  
      findings.push({ ...this.RULES\[8\], status: 'FAIL', current: 'Unauthenticated Gateway Registration' });  
      deduction \+= this.RULES\[8\].weight;  
    } else {  
      findings.push({ ...this.RULES\[8\], status: 'PASS', current: 'Gateway Client TLS / API Token Active' });  
    }

    // 10\. Class Configuration  
    if (config.isBatteryClassC) {  
      findings.push({ ...this.RULES\[9\], status: 'FAIL', current: 'Battery Node Operating in Class C' });  
      deduction \+= this.RULES\[9\].weight;  
    } else {  
      findings.push({ ...this.RULES\[9\], status: 'PASS', current: 'Class A Low-Power Battery Profile' });  
    }

    const score \= Math.max(0, 100 \- deduction);  
    let posture \= 'POOR';  
    if (score \>= 90\) posture \= 'EXCELLENT';  
    else if (score \>= 75\) posture \= 'GOOD';  
    else if (score \>= 50\) posture \= 'MODERATE';

    return {  
      score,  
      posture,  
      findings,  
      timestamp: new Date().toISOString()  
    };  
  }  
}

if (typeof module \!== 'undefined' && module.exports) {  
  module.exports \= { LoRaWANSecurityAuditor };  
}  
