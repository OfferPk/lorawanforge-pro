\<\!DOCTYPE html\>  
\<html lang="en"\>  
\<head\>  
  \<meta charset="UTF-8"\>  
  \<meta name="viewport" content="width=device-width, initial-scale=1.0"\>  
  \<title\>LoRaWANForge Pro \| Industrial \&amp; Smart City LoRaWAN Protocol Studio\</title\>  
  \<link rel="stylesheet" href="lorawan-forge.css"\>  
  \<link rel="preconnect" href="https\://fonts.googleapis.com"\>  
  \<link rel="preconnect" href="https\://fonts.gstatic.com" crossorigin\>  
  \<link href="https\://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800\&family=JetBrains+Mono:wght@400;500;600;700\&display=swap" rel="stylesheet"\>  
\</head\>  
\<body\>  
  \<div class="container"\>  
    \<header\>  
      \<div class="brand"\>  
        \<div class="logo-badge"\>LoRa\</div\>  
        \<div class="brand-info"\>  
          \<h1\>LoRaWANForge Pro\</h1\>  
          \<p\>Industrial \&amp; Smart City LoRaWAN (v1.0.4 / v1.1) Protocol Studio, PHY Airtime \&amp; Zero-Trust Auditor\</p\>  
        \</div\>  
      \</div\>  
      \<div class="header-status"\>  
        \<span class="badge badge-lora"\>LoRaWAN 1.0.4 / 1.1\</span\>  
        \<span class="badge badge-emerald"\>AES-128 CMAC MIC\</span\>  
        \<span class="badge badge-cyan"\>Semtech UDP / LNS\</span\>  
      \</div\>  
    \</header\>

    \<div class="nav-tabs"\>  
      \<button class="tab-btn active" data-tab="tab-dissector"\>Frame Dissector\</button\>  
      \<button class="tab-btn" data-tab="tab-synthesizer"\>Uplink / Downlink Synthesizer\</button\>  
      \<button class="tab-btn" data-tab="tab-airtime"\>PHY Airtime \&amp; Duty-Cycle\</button\>  
      \<button class="tab-btn" data-tab="tab-auditor"\>LoRa Alliance Security Auditor\</button\>  
      \<button class="tab-btn" data-tab="tab-config"\>Gateway Firewall \&amp; IDS Rules\</button\>  
    \</div\>

    \<\!-- TAB 1: DISSECTOR \--\>  
    \<div id="tab-dissector" class="tab-panel active"\>  
      \<div class="grid-2"\>  
        \<div class="card"\>  
          \<div class="card-title"\>  
            \<span\>Raw LoRaWAN PHYPayload Input\</span\>  
            \<select id="preset-selector" style="width: auto; padding: 4px 8px; font-size: 0.8rem;"\>  
              \<option value="unconfirmed\_up"\>Preset: Unconfirmed Uplink (DevAddr 01020304, FCnt 1)\</option\>  
              \<option value="confirmed\_up"\>Preset: Confirmed Uplink (DevAddr 01020304, FCnt 5)\</option\>  
              \<option value="join\_req"\>Preset: OTAA Join-Request (JoinEUI \+ DevEUI \+ DevNonce)\</option\>  
            \</select\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>Raw PHYPayload Hex Stream\</label\>  
            \<textarea id="hex-input" class="mono" rows="8" placeholder="Enter hex stream (e.g. 40 04 03 02 01 00 01 00 01 12 34 56 78)..."\>\</textarea\>  
          \</div\>  
          \<div class="btn-group"\>  
            \<button id="btn-dissect" class="btn"\>Dissect Frame\</button\>  
            \<button id="btn-export-pcap" class="btn btn-secondary"\>Export to PCAP\</button\>  
            \<button id="btn-clear-hex" class="btn btn-secondary"\>Clear\</button\>  
          \</div\>  
        \</div\>

        \<div class="card"\>  
          \<div class="card-title"\>Protocol Dissection Tree\</div\>  
          \<div id="dissection-output" class="tree-view"\>  
            \<p style="color: var(--text-muted);"\>Select a preset or enter raw hex and click "Dissect Frame".\</p\>  
          \</div\>  
        \</div\>  
      \</div\>  
    \</div\>

    \<\!-- TAB 2: SYNTHESIZER \--\>  
    \<div id="tab-synthesizer" class="tab-panel"\>  
      \<div class="grid-2"\>  
        \<div class="card"\>  
          \<div class="card-title"\>MAC Payload \&amp; MIC Synthesizer\</div\>  
          \<div class="form-group"\>  
            \<label\>Message Type (MType)\</label\>  
            \<select id="synth-mtype"\>  
              \<option value="unconfirmed"\>Unconfirmed Data Up (0x40)\</option\>  
              \<option value="confirmed"\>Confirmed Data Up (0x80)\</option\>  
            \</select\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>Device Address (DevAddr \- 4 Bytes Hex)\</label\>  
            \<input type="text" id="synth-devaddr" value="01 02 03 04" class="mono"\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>Frame Counter (FCnt)\</label\>  
            \<input type="number" id="synth-fcnt" value="1" min="0"\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>Application Port (FPort 1 to 223)\</label\>  
            \<input type="number" id="synth-fport" value="1" min="1" max="223"\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>Cleartext Payload (Hex string)\</label\>  
            \<input type="text" id="synth-payload" value="01 67 01 10" class="mono"\>  
            \<small style="color: var(--text-muted);"\>Example: Cayenne LPP Temperature (01 67 01 10 \= 27.2°C)\</small\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>Network Session Key (NwkSKey \- 16 Bytes Hex)\</label\>  
            \<input type="text" id="synth-nwkskey" value="2B 7E 15 16 28 AE D2 A6 AB F7 15 88 09 CF 4F 3C" class="mono"\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>Application Session Key (AppSKey \- 16 Bytes Hex)\</label\>  
            \<input type="text" id="synth-appskey" value="2B 7E 15 16 28 AE D2 A6 AB F7 15 88 09 CF 4F 3C" class="mono"\>  
          \</div\>  
          \<div class="btn-group"\>  
            \<button id="btn-build-uplink" class="btn"\>Synthesize Uplink Frame\</button\>  
          \</div\>  
        \</div\>

        \<div class="card"\>  
          \<div class="card-title"\>Synthesized Wire Stream \&amp; Semtech JSON\</div\>  
          \<label\>PHYPayload Wire Hex\</label\>  
          \<div id="synth-hex-output" class="hex-view"\>Click "Synthesize Uplink Frame" to generate.\</div\>  
          \<div style="margin-top: 14px;"\>  
            \<label\>Base64 \&amp; Message Integrity Code (MIC)\</label\>  
            \<div id="synth-breakdown" class="tree-view" style="max-height: 120px;"\>-\</div\>  
          \</div\>  
          \<div style="margin-top: 14px;"\>  
            \<label\>Semtech Packet Forwarder JSON (rxpk)\</label\>  
            \<div id="synth-json-output" class="hex-view" style="max-height: 160px;"\>-\</div\>  
          \</div\>  
        \</div\>  
      \</div\>  
    \</div\>

    \<\!-- TAB 3: AIRTIME CALCULATOR \--\>  
    \<div id="tab-airtime" class="tab-panel"\>  
      \<div class="grid-2"\>  
        \<div class="card"\>  
          \<div class="card-title"\>LoRa Physical Layer Parameters\</div\>  
          \<div class="form-group"\>  
            \<label\>Spreading Factor (SF)\</label\>  
            \<select id="air-sf"\>  
              \<option value="7"\>SF7 (Fastest, High Capacity)\</option\>  
              \<option value="8"\>SF8\</option\>  
              \<option value="9"\>SF9\</option\>  
              \<option value="10"\>SF10\</option\>  
              \<option value="11"\>SF11 (Long Range, High Airtime)\</option\>  
              \<option value="12"\>SF12 (Maximum Sensitivity / Lowest Range)\</option\>  
            \</select\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>Bandwidth (BW)\</label\>  
            \<select id="air-bw"\>  
              \<option value="125"\>125 kHz (Standard EU868 / US915 Uplink)\</option\>  
              \<option value="250"\>250 kHz\</option\>  
              \<option value="500"\>500 kHz (US915 High-Speed)\</option\>  
            \</select\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>Coding Rate (CR)\</label\>  
            \<select id="air-cr"\>  
              \<option value="1"\>4/5 (Standard)\</option\>  
              \<option value="2"\>4/6\</option\>  
              \<option value="3"\>4/7\</option\>  
              \<option value="4"\>4/8\</option\>  
            \</select\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>PHY Payload Size (Bytes)\</label\>  
            \<input type="number" id="air-pl" value="23" min="1" max="250"\>  
          \</div\>  
          \<div class="form-group"\>  
            \<label\>Preamble Length (Symbols)\</label\>  
            \<input type="number" id="air-preamble" value="8" min="6" max="65535"\>  
          \</div\>  
          \<div class="btn-group"\>  
            \<button id="btn-calc-airtime" class="btn"\>Calculate Airtime \&amp; Duty Cycle\</button\>  
          \</div\>  
        \</div\>

        \<div class="card"\>  
          \<div class="card-title"\>Airtime \&amp; Regulatory Budget Breakdown\</div\>  
          \<div class="score-display"\>  
            \<div\>  
              \<div class="score-num" id="res-airtime-ms"\>61.7\</div\>  
              \<div style="font-size: 0.8rem; color: var(--text-muted);"\>AIRTIME (MS)\</div\>  
            \</div\>  
            \<div\>  
              \<div style="font-size: 1.1rem; font-weight: 700;" id="res-max-msgs"\>583 msgs/hr\</div\>  
              \<p style="color: var(--text-muted); font-size: 0.85rem;"\>Maximum allowed transmission rate under EU868 1% duty-cycle regulation.\</p\>  
            \</div\>  
          \</div\>  
          \<div id="airtime-breakdown" class="tree-view" style="max-height: 240px;"\>-\</div\>  
        \</div\>  
      \</div\>  
    \</div\>

    \<\!-- TAB 4: AUDITOR \--\>  
    \<div id="tab-auditor" class="tab-panel"\>  
      \<div class="card"\>  
        \<div class="card-title"\>  
          \<span\>LoRa Alliance Zero-Trust Security Audit\</span\>  
          \<button id="btn-run-audit" class="btn" style="padding: 6px 14px;"\>Run Security Audit\</button\>  
        \</div\>  
        \<div class="score-display"\>  
          \<div\>  
            \<div class="score-num" id="audit-score"\>72\</div\>  
            \<div style="font-size: 0.8rem; color: var(--text-muted);"\>SECURITY SCORE\</div\>  
          \</div\>  
          \<div\>  
            \<div style="font-size: 1.2rem; font-weight: 700;" id="audit-posture"\>MODERATE POSTURE\</div\>  
            \<p style="color: var(--text-muted); font-size: 0.85rem;" id="audit-summary"\>7 of 10 LoRaWAN cybersecurity controls currently pass zero-trust criteria.\</p\>  
          \</div\>  
        \</div\>

        \<div style="overflow-x: auto;"\>  
          \<table class="audit-table"\>  
            \<thead\>  
              \<tr\>  
                \<th\>Rule ID\</th\>  
                \<th\>Security Control Title\</th\>  
                \<th\>Standard\</th\>  
                \<th\>Severity\</th\>  
                \<th\>Status\</th\>  
                \<th\>Remediation Strategy\</th\>  
              \</tr\>  
            \</thead\>  
            \<tbody id="audit-tbody"\>  
              \<\!-- Rendered via JS \--\>  
            \</tbody\>  
          \</table\>  
        \</div\>  
      \</div\>  
    \</div\>

    \<\!-- TAB 5: CONFIG SYNTHESIZER \--\>  
    \<div id="tab-config" class="tab-panel"\>  
      \<div class="grid-2"\>  
        \<div class="card"\>  
          \<div class="card-title"\>Linux nftables Gateway Firewall\</div\>  
          \<p style="color: var(--text-muted); font-size: 0.85rem; margin-bottom: 12px;"\>Conduit filter protecting Semtech UDP (1700) and Basic Station WSS (8887).\</p\>  
          \<div id="nftables-output" class="hex-view" style="max-height: 380px;"\>\</div\>  
        \</div\>  
        \<div class="card"\>  
          \<div class="card-title"\>Suricata L7 Industrial IDS Rules\</div\>  
          \<p style="color: var(--text-muted); font-size: 0.85rem; margin-bottom: 12px;"\>Signatures monitoring gateway telemetry, downlink injection, and DoS probes.\</p\>  
          \<div id="suricata-output" class="hex-view" style="max-height: 380px;"\>\</div\>  
        \</div\>  
      \</div\>  
    \</div\>

  \</div\>

  \<script src="lorawan-protocol-engine.js"\>\</script\>  
  \<script src="lorawan-security-auditor.js"\>\</script\>  
  \<script src="lorawan-config-synthesizer.js"\>\</script\>  
  \<script src="app.js"\>\</script\>  
\</body\>  
\</html\>  
