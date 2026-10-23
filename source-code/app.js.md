/\*\*  
 \* LoRaWANForge Pro \- Web Application Controller  
 \*/

document.addEventListener('DOMContentLoaded', () \=\> {  
  const engine \= new LoRaWANEngine();  
  const auditor \= new LoRaWANSecurityAuditor();  
  const synthesizer \= new LoRaWANConfigSynthesizer();

  const DEFAULT\_KEY \= "2B7E151628AED2A6ABF7158809CF4F3C";

  // Presets  
  const PRESETS \= {  
    unconfirmed\_up: engine.buildUplink("01020304", 1, 1, "01670110", DEFAULT\_KEY, DEFAULT\_KEY, false).hexString,  
    confirmed\_up: engine.buildUplink("01020304", 5, 2, "DEADBEEF", DEFAULT\_KEY, DEFAULT\_KEY, true).hexString,  
    join\_req: "00 00 00 00 00 00 00 00 01 01 02 03 04 05 06 07 08 00 01 a1 b2 c3 d4"  
  };

  // Tab switching  
  const tabBtns \= document.querySelectorAll('.tab-btn');  
  const tabPanels \= document.querySelectorAll('.tab-panel');

  tabBtns.forEach(btn \=\> {  
    btn.addEventListener('click', () \=\> {  
      tabBtns.forEach(b \=\> b.classList.remove('active'));  
      tabPanels.forEach(p \=\> p.classList.remove('active'));  
      btn.classList.add('active');  
      const target \= document.getElementById(btn.getAttribute('data-tab'));  
      if (target) target.classList.add('active');  
    });  
  });

  // Preset selector  
  const presetSelector \= document.getElementById('preset-selector');  
  const hexInput \= document.getElementById('hex-input');

  presetSelector.addEventListener('change', () \=\> {  
    const val \= presetSelector.value;  
    if (PRESETS\[val\]) {  
      hexInput.value \= PRESETS\[val\];  
      runDissection();  
    }  
  });

  // Dissector button  
  document.getElementById('btn-dissect').addEventListener('click', runDissection);  
  document.getElementById('btn-clear-hex').addEventListener('click', () \=\> {  
    hexInput.value \= '';  
    document.getElementById('dissection-output').innerHTML \= '\<p style="color: var(--text-muted);"\>Cleared.\</p\>';  
  });

  function runDissection() {  
    const hex \= hexInput.value.trim();  
    if (\!hex) return;  
    const output \= document.getElementById('dissection-output');  
    try {  
      const bytes \= engine.hexToBytes(hex);  
      const res \= engine.dissect(bytes);

      let html \= \`  
        \<div class="tree-node"\>  
          \<span class="tree-key"\>MAC Header (MHDR):\</span\>\<br\>  
          \&nbsp;\&nbsp;MType: \<span class="tree-val" style="color:var(--accent-lora); font-weight:700;"\>\${res.mhdr.mtype}\</span\>\<br\>  
          \&nbsp;\&nbsp;Major Version: \<span class="tree-val"\>\${res.mhdr.major}\</span\> \| Raw: \<span class="tree-val"\>\${res.mhdr.raw}\</span\>  
        \</div\>  
      \`;

      if (res.joinRequest) {  
        html \+= \`  
          \<div class="tree-node"\>  
            \<span class="tree-key"\>Join-Request Parameters:\</span\>\<br\>  
            \&nbsp;\&nbsp;JoinEUI: \<span class="tree-val" style="color:\#06b6d4;"\>\${res.joinRequest.joinEui}\</span\>\<br\>  
            \&nbsp;\&nbsp;DevEUI: \<span class="tree-val" style="color:\#06b6d4;"\>\${res.joinRequest.devEui}\</span\>\<br\>  
            \&nbsp;\&nbsp;DevNonce: \<span class="tree-val"\>\${res.joinRequest.devNonce}\</span\>\<br\>  
            \&nbsp;\&nbsp;MIC: \<span class="tree-val" style="color:\#10b981; font-weight:700;"\>\${res.joinRequest.mic}\</span\>  
          \</div\>  
        \`;  
      }

      if (res.fhdr) {  
        html \+= \`  
          \<div class="tree-node"\>  
            \<span class="tree-key"\>Frame Header (FHDR):\</span\>\<br\>  
            \&nbsp;\&nbsp;DevAddr: \<span class="tree-val" style="color:\#06b6d4; font-weight:700;"\>\${res.fhdr.devAddr}\</span\>\<br\>  
            \&nbsp;\&nbsp;Frame Counter (FCnt): \<span class="tree-val"\>\${res.fhdr.fcnt}\</span\>\<br\>  
            \&nbsp;\&nbsp;Controls: ADR=\<span class="tree-val"\>\${res.fhdr.adr}\</span\>, ACK=\<span class="tree-val"\>\${res.fhdr.ack}\</span\>, FOptsLen=\<span class="tree-val"\>\${res.fhdr.foptsLen}\</span\>\<br\>  
            \&nbsp;\&nbsp;FOpts Hex: \<code\>\${res.fhdr.foptsHex \|\| 'None'}\</code\>  
          \</div\>  
        \`;  
      }

      if (res.macPayload) {  
        html \+= \`  
          \<div class="tree-node"\>  
            \<span class="tree-key"\>MAC Payload \&amp; Integrity:\</span\>\<br\>  
            \&nbsp;\&nbsp;FPort: \<span class="tree-val" style="font-weight:700;"\>\${res.macPayload.fport}\</span\>\<br\>  
            \&nbsp;\&nbsp;FRMPayload (Cipher): \<code\>\${res.macPayload.payloadCipherHex \|\| 'Empty'}\</code\> (\${res.macPayload.payloadLength} B)\<br\>  
            \&nbsp;\&nbsp;Message Integrity Code (MIC): \<span class="tree-val" style="color:\#10b981; font-weight:800;"\>\${res.macPayload.mic}\</span\>  
          \</div\>  
        \`;  
      }

      output.innerHTML \= html;  
    } catch (e) {  
      output.innerHTML \= \`\<p style="color: var(--accent-rose); font-weight:600;"\>Dissection Error: \${e.message}\</p\>\`;  
    }  
  }

  // Initial load  
  hexInput.value \= PRESETS.unconfirmed\_up;  
  runDissection();

  // Synthesizer Form  
  document.getElementById('btn-build-uplink').addEventListener('click', () \=\> {  
    const isConfirmed \= document.getElementById('synth-mtype').value \=== 'confirmed';  
    const devAddr \= document.getElementById('synth-devaddr').value;  
    const fcnt \= parseInt(document.getElementById('synth-fcnt').value, 10\) \|\| 0;  
    const fport \= parseInt(document.getElementById('synth-fport').value, 10\) \|\| 1;  
    const payload \= document.getElementById('synth-payload').value;  
    const nwkKey \= document.getElementById('synth-nwkskey').value;  
    const appKey \= document.getElementById('synth-appskey').value;

    const res \= engine.buildUplink(devAddr, fcnt, fport, payload, nwkKey, appKey, isConfirmed);  
    document.getElementById('synth-hex-output').innerText \= res.hexString;  
    document.getElementById('synth-breakdown').innerHTML \= \`  
      \<div\>\<strong\>Base64:\</strong\> \<code\>\${res.base64}\</code\>\</div\>  
      \<div\>\<strong\>Calculated MIC:\</strong\> \<span style="color:\#10b981; font-weight:700;"\>\${res.micHex}\</span\>\</div\>  
      \<div\>\<strong\>Total Frame Size:\</strong\> \${res.rawBytes.length} bytes\</div\>  
    \`;

    const semtech \= engine.generateSemtechJson(res.rawBytes);  
    document.getElementById('synth-json-output').innerText \= JSON.stringify(semtech.rxpk\[0\], null, 2);  
  });

  // Airtime Calculator  
  function runAirtime() {  
    const sf \= parseInt(document.getElementById('air-sf').value, 10);  
    const bw \= parseInt(document.getElementById('air-bw').value, 10);  
    const cr \= parseInt(document.getElementById('air-cr').value, 10);  
    const pl \= parseInt(document.getElementById('air-pl').value, 10);  
    const preamble \= parseInt(document.getElementById('air-preamble').value, 10);

    const res \= engine.calculateAirtime(pl, sf, bw, cr, preamble);  
    document.getElementById('res-airtime-ms').innerText \= res.totalAirtimeMs.toFixed(1);  
    document.getElementById('res-max-msgs').innerText \= \`\${res.dutyCycleMaxPacketsPerHour} msgs/hr\`;

    document.getElementById('airtime-breakdown').innerHTML \= \`  
      \<div\>\<strong\>Symbol Duration (Ts):\</strong\> \${res.tSymbolMs.toFixed(3)} ms\</div\>  
      \<div\>\<strong\>Preamble Airtime:\</strong\> \${res.tPreambleMs.toFixed(2)} ms (\${preamble} \+ 4.25 symbols)\</div\>  
      \<div\>\<strong\>Payload Airtime:\</strong\> \${res.tPayloadMs.toFixed(2)} ms (\${pl} bytes at SF\${sf})\</div\>  
      \<div\>\<strong\>Total Packet Airtime:\</strong\> \<span style="color:var(--accent-lora); font-weight:700;"\>\${res.totalAirtimeMs.toFixed(2)} ms\</span\>\</div\>  
      \<div style="margin-top:8px;"\>\<strong\>EU868 Regulatory Budget:\</strong\> 36,000 ms/hr available (1% cap)\</div\>  
    \`;  
  }

  document.getElementById('btn-calc-airtime').addEventListener('click', runAirtime);  
  runAirtime();

  // PCAP Export  
  document.getElementById('btn-export-pcap').addEventListener('click', () \=\> {  
    const hex \= hexInput.value.trim();  
    if (\!hex) return;  
    const bytes \= engine.hexToBytes(hex);  
    const pcapBytes \= engine.exportPcap(\[bytes\]);  
    downloadBlob(pcapBytes, 'lorawan-capture.pcap');  
  });

  function downloadBlob(bytes, filename) {  
    const blob \= new Blob(\[bytes\], { type: 'application/vnd.tcpdump.pcap' });  
    const url \= URL.createObjectURL(blob);  
    const a \= document.createElement('a');  
    a.href \= url;  
    a.download \= filename;  
    a.click();  
    URL.revokeObjectURL(url);  
  }

  // Security Auditor  
  function runAudit() {  
    const sampleConfig \= {  
      hasDefaultAppKey: false,  
      hasMonotonicDevNonce: true,  
      usesAbp: false,  
      allowFcntReset: false,  
      hasTlsBackhaul: false,  
      exceedsDutyCycle: false,  
      hasKeySeparation: true,  
      validatesFopts: true,  
      hasGatewayAuth: true,  
      isBatteryClassC: false  
    };

    const res \= auditor.auditConfiguration(sampleConfig);  
    document.getElementById('audit-score').innerText \= res.score;  
    document.getElementById('audit-posture').innerText \= \`\${res.posture} POSTURE\`;

    const tbody \= document.getElementById('audit-tbody');  
    tbody.innerHTML \= res.findings.map(f \=\> \`  
      \<tr\>  
        \<td\>\<code\>\${f.id}\</code\>\</td\>  
        \<td\>\<strong\>\${f.title}\</strong\>\</td\>  
        \<td\>\<small\>\${f.standard}\</small\>\</td\>  
        \<td\>\<span class="badge \${f.severity \=== 'CRITICAL' ? 'badge-rose' : f.severity \=== 'HIGH' ? 'badge-lora' : 'badge-cyan'}"\>\${f.severity}\</span\>\</td\>  
        \<td\>\<span class="badge \${f.status \=== 'PASS' ? 'badge-emerald' : 'badge-rose'}"\>\${f.status}\</span\>\</td\>  
        \<td\>\<small\>\${f.remediation}\</small\>\</td\>  
      \</tr\>  
    \`).join('');  
  }

  document.getElementById('btn-run-audit').addEventListener('click', runAudit);  
  runAudit();

  // Config Synthesizer  
  document.getElementById('nftables-output').innerText \= synthesizer.generateNftables();  
  document.getElementById('suricata-output').innerText \= synthesizer.generateSuricataRules();  
});  
