/\* LoRaWANForge Pro \- Neon Cyber-Industrial Glassmorphism Theme \*/  
:root {  
  \--bg-primary: \#060812;  
  \--bg-card: rgba(13, 18, 36, 0.88);  
  \--border-color: rgba(168, 85, 247, 0.25);  
  \--border-glow: rgba(168, 85, 247, 0.5);  
  \--accent-lora: \#a855f7; /\* Electric Purple \*/  
  \--accent-cyan: \#06b6d4;  
  \--accent-emerald: \#10b981;  
  \--accent-amber: \#f59e0b;  
  \--accent-rose: \#f43f5e;  
  \--text-main: \#f8fafc;  
  \--text-muted: \#94a3b8;  
  \--font-mono: 'JetBrains Mono', 'Fira Code', 'Courier New', monospace;  
  \--font-sans: 'Inter', \-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;  
}

\* {  
  box-sizing: border-box;  
  margin: 0;  
  padding: 0;  
}

body {  
  background-color: var(--bg-primary);  
  background-image:   
    radial-gradient(circle at 10% 15%, rgba(168, 85, 247, 0.08) 0%, transparent 40%),  
    radial-gradient(circle at 90% 85%, rgba(6, 182, 212, 0.07) 0%, transparent 40%);  
  color: var(--text-main);  
  font-family: var(--font-sans);  
  line-height: 1.5;  
  min-height: 100vh;  
  padding: 24px;  
}

.container {  
  max-width: 1400px;  
  margin: 0 auto;  
}

/\* Header \*/  
header {  
  display: flex;  
  justify-content: space-between;  
  align-items: center;  
  padding: 20px 28px;  
  background: var(--bg-card);  
  border: 1px solid var(--border-color);  
  border-radius: 14px;  
  backdrop-filter: blur(12px);  
  margin-bottom: 24px;  
}

.brand {  
  display: flex;  
  align-items: center;  
  gap: 16px;  
}

.logo-badge {  
  background: linear-gradient(135deg, var(--accent-lora), \#7e22ce);  
  color: \#ffffff;  
  font-weight: 800;  
  font-size: 1.3rem;  
  padding: 10px 16px;  
  border-radius: 10px;  
  letter-spacing: 0.5px;  
}

.brand-info h1 {  
  font-size: 1.4rem;  
  font-weight: 700;  
  letter-spacing: \-0.5px;  
}

.brand-info p {  
  color: var(--text-muted);  
  font-size: 0.85rem;  
}

.header-status {  
  display: flex;  
  gap: 12px;  
}

.badge {  
  display: inline-flex;  
  align-items: center;  
  gap: 6px;  
  padding: 6px 12px;  
  border-radius: 20px;  
  font-size: 0.75rem;  
  font-weight: 600;  
  text-transform: uppercase;  
  letter-spacing: 0.5px;  
}

.badge-lora { background: rgba(168, 85, 247, 0.15); color: var(--accent-lora); border: 1px solid rgba(168, 85, 247, 0.35); }  
.badge-emerald { background: rgba(16, 185, 129, 0.15); color: \#10b981; border: 1px solid rgba(16, 185, 129, 0.3); }  
.badge-cyan { background: rgba(6, 182, 212, 0.15); color: var(--accent-cyan); border: 1px solid rgba(6, 182, 212, 0.3); }  
.badge-rose { background: rgba(244, 63, 94, 0.15); color: var(--accent-rose); border: 1px solid rgba(244, 63, 94, 0.3); }

/\* Navigation Tabs \*/  
.nav-tabs {  
  display: flex;  
  gap: 8px;  
  margin-bottom: 24px;  
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);  
  padding-bottom: 8px;  
  overflow-x: auto;  
}

.tab-btn {  
  background: transparent;  
  border: 1px solid transparent;  
  color: var(--text-muted);  
  padding: 10px 18px;  
  border-radius: 8px;  
  font-size: 0.9rem;  
  font-weight: 600;  
  cursor: pointer;  
  transition: all 0.2s ease;  
  white-space: nowrap;  
}

.tab-btn:hover {  
  color: var(--text-main);  
  background: rgba(255, 255, 255, 0.05);  
}

.tab-btn.active {  
  background: rgba(168, 85, 247, 0.15);  
  border-color: var(--accent-lora);  
  color: var(--accent-lora);  
}

/\* Tab Panels \*/  
.tab-panel {  
  display: none;  
}  
.tab-panel.active {  
  display: block;  
}

/\* Grid Layouts \*/  
.grid-2 {  
  display: grid;  
  grid-template-columns: repeat(auto-fit, minmax(480px, 1fr));  
  gap: 24px;  
}

.card {  
  background: var(--bg-card);  
  border: 1px solid var(--border-color);  
  border-radius: 12px;  
  padding: 24px;  
  backdrop-filter: blur(10px);  
}

.card-title {  
  font-size: 1.1rem;  
  font-weight: 700;  
  margin-bottom: 16px;  
  display: flex;  
  justify-content: space-between;  
  align-items: center;  
  color: var(--accent-lora);  
}

/\* Form Controls \*/  
.form-group {  
  margin-bottom: 16px;  
}

label {  
  display: block;  
  font-size: 0.825rem;  
  font-weight: 600;  
  color: var(--text-muted);  
  margin-bottom: 6px;  
  text-transform: uppercase;  
  letter-spacing: 0.5px;  
}

input, select, textarea {  
  width: 100%;  
  background: rgba(6, 10, 22, 0.95);  
  border: 1px solid rgba(255, 255, 255, 0.12);  
  border-radius: 8px;  
  padding: 10px 14px;  
  color: var(--text-main);  
  font-family: var(--font-sans);  
  font-size: 0.9rem;  
  transition: border-color 0.2s;  
}

input:focus, select:focus, textarea:focus {  
  outline: none;  
  border-color: var(--accent-lora);  
  box-shadow: 0 0 0 2px rgba(168, 85, 247, 0.2);  
}

textarea.mono, input.mono {  
  font-family: var(--font-mono);  
  font-size: 0.85rem;  
}

.btn-group {  
  display: flex;  
  gap: 10px;  
  margin-top: 18px;  
  flex-wrap: wrap;  
}

.btn {  
  background: var(--accent-lora);  
  color: \#ffffff;  
  border: none;  
  border-radius: 8px;  
  padding: 10px 18px;  
  font-size: 0.875rem;  
  font-weight: 700;  
  cursor: pointer;  
  transition: all 0.2s ease;  
  display: inline-flex;  
  align-items: center;  
  gap: 8px;  
}

.btn:hover {  
  background: \#9333ea;  
}

.btn-secondary {  
  background: rgba(255, 255, 255, 0.08);  
  color: var(--text-main);  
  border: 1px solid rgba(255, 255, 255, 0.15);  
}

.btn-secondary:hover {  
  background: rgba(255, 255, 255, 0.15);  
  color: \#fff;  
}

/\* Hex & Dissection Displays \*/  
.hex-view {  
  background: \#03050c;  
  border: 1px solid rgba(255, 255, 255, 0.1);  
  border-radius: 8px;  
  padding: 14px;  
  font-family: var(--font-mono);  
  font-size: 0.85rem;  
  line-height: 1.6;  
  color: \#c084fc;  
  white-space: pre-wrap;  
  word-break: break-all;  
  max-height: 280px;  
  overflow-y: auto;  
}

.tree-view {  
  background: \#03050c;  
  border: 1px solid rgba(255, 255, 255, 0.1);  
  border-radius: 8px;  
  padding: 16px;  
  font-family: var(--font-mono);  
  font-size: 0.85rem;  
  max-height: 380px;  
  overflow-y: auto;  
}

.tree-node {  
  margin-bottom: 8px;  
  padding-left: 12px;  
  border-left: 2px solid rgba(168, 85, 247, 0.35);  
}

.tree-key {  
  color: var(--accent-lora);  
  font-weight: 600;  
}

.tree-val {  
  color: \#e2e8f0;  
}

/\* Auditor Table \*/  
.audit-table {  
  width: 100%;  
  border-collapse: collapse;  
  font-size: 0.875rem;  
  margin-top: 14px;  
}

.audit-table th, .audit-table td {  
  padding: 12px;  
  text-align: left;  
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);  
}

.audit-table th {  
  color: var(--text-muted);  
  font-weight: 600;  
  text-transform: uppercase;  
  font-size: 0.75rem;  
}

.score-display {  
  display: flex;  
  align-items: center;  
  gap: 20px;  
  padding: 18px;  
  background: rgba(168, 85, 247, 0.08);  
  border-radius: 10px;  
  margin-bottom: 20px;  
}

.score-num {  
  font-size: 2.4rem;  
  font-weight: 800;  
  color: var(--accent-lora);  
}  
