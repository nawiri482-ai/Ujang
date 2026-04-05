<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>UJANG_VOIDS AI</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;500;600&family=Orbitron:wght@700;900&display=swap');

        * { margin: 0; padding: 0; box-sizing: border-box; }

        :root {
            --bg-primary: #0a0a0f;
            --bg-secondary: #12121a;
            --bg-tertiary: #1a1a28;
            --bg-chat: #0e0e16;
            --accent: #7c3aed;
            --accent-2: #a855f7;
            --accent-glow: rgba(124, 58, 237, 0.3);
            --text-primary: #e2e8f0;
            --text-secondary: #94a3b8;
            --text-muted: #64748b;
            --border: rgba(255, 255, 255, 0.06);
            --user-bg: #1e1b4b;
            --ai-bg: #151520;
            --success: #10b981;
            --danger: #ef4444;
            --warning: #f59e0b;
        }

        html, body { height: 100%; overflow: hidden; }

        body {
            font-family: 'Inter', sans-serif;
            background: var(--bg-primary);
            color: var(--text-primary);
            display: flex;
            height: 100vh;
            overflow: hidden;
        }

        .sidebar {
            width: 280px; min-width: 280px; max-width: 280px;
            background: var(--bg-secondary);
            border-right: 1px solid var(--border);
            display: flex; flex-direction: column;
            height: 100vh; transition: all 0.3s ease;
            position: relative; z-index: 50; overflow: hidden;
        }
        .sidebar.collapsed { width: 0; min-width: 0; max-width: 0; border: none; padding: 0; }

        .sidebar-header { padding: 20px; border-bottom: 1px solid var(--border); flex-shrink: 0; }

        .logo {
            font-family: 'Orbitron', sans-serif; font-size: 1.1em; font-weight: 900;
            background: linear-gradient(135deg, var(--accent), var(--accent-2), #ec4899);
            -webkit-background-clip: text; -webkit-text-fill-color: transparent;
            display: flex; align-items: center; gap: 10px;
        }
        .logo-icon {
            width: 36px; height: 36px;
            background: linear-gradient(135deg, var(--accent), var(--accent-2));
            border-radius: 10px; display: flex; align-items: center; justify-content: center;
            font-size: 1.2em; -webkit-text-fill-color: white;
        }

        .new-chat-btn {
            width: 100%; padding: 12px 16px; margin-top: 15px;
            background: linear-gradient(135deg, var(--accent), var(--accent-2));
            border: none; border-radius: 12px; color: white;
            font-family: 'Inter', sans-serif; font-size: 0.9em; font-weight: 600;
            cursor: pointer; transition: all 0.2s;
            display: flex; align-items: center; gap: 8px;
        }
        .new-chat-btn:hover { transform: translateY(-1px); box-shadow: 0 4px 15px var(--accent-glow); }

        .chat-history {
            flex: 1; overflow-y: auto; overflow-x: hidden;
            padding: 10px; min-height: 0; -webkit-overflow-scrolling: touch;
        }
        .history-label {
            font-size: 0.7em; font-weight: 600; color: var(--text-muted);
            text-transform: uppercase; letter-spacing: 1.5px; padding: 10px 10px 5px;
            position: sticky; top: 0; background: var(--bg-secondary); z-index: 2;
        }
        .history-item {
            padding: 10px 14px; border-radius: 10px; cursor: pointer;
            transition: all 0.15s; display: flex; align-items: center; gap: 10px;
            margin-bottom: 2px; font-size: 0.85em; color: var(--text-secondary);
            white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
        }
        .history-item:hover { background: var(--bg-tertiary); color: var(--text-primary); }
        .history-item.active { background: var(--bg-tertiary); color: var(--text-primary); border: 1px solid var(--border); }
        .history-item-text { flex: 1; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
        .history-item .delete-chat {
            flex-shrink: 0; opacity: 0; color: var(--danger); cursor: pointer;
            font-size: 1em; transition: all 0.15s; padding: 2px 6px; border-radius: 4px;
        }
        .history-item:hover .delete-chat { opacity: 1; }
        .history-item .delete-chat:hover { background: rgba(239, 68, 68, 0.2); }
        .history-empty { padding: 30px 20px; text-align: center; color: var(--text-muted); font-size: 0.8em; }

        .sidebar-footer { padding: 15px; border-top: 1px solid var(--border); flex-shrink: 0; }
        .settings-btn {
            width: 100%; padding: 10px; background: var(--bg-tertiary);
            border: 1px solid var(--border); border-radius: 10px;
            color: var(--text-secondary); font-family: 'Inter', sans-serif;
            font-size: 0.85em; cursor: pointer; transition: all 0.15s;
            display: flex; align-items: center; gap: 8px;
        }
        .settings-btn:hover { background: rgba(124, 58, 237, 0.1); color: var(--accent-2); }

        .resize-handle {
            width: 6px; cursor: col-resize; background: transparent;
            position: absolute; right: -3px; top: 0; bottom: 0; z-index: 60; transition: background 0.2s;
        }
        .resize-handle:hover, .resize-handle.active { background: var(--accent); opacity: 0.5; }

        .main { flex: 1; display: flex; flex-direction: column; min-width: 0; height: 100vh; overflow: hidden; }

        .topbar {
            padding: 12px 20px; border-bottom: 1px solid var(--border);
            display: flex; align-items: center; gap: 12px;
            background: var(--bg-secondary); flex-shrink: 0;
        }
        .toggle-sidebar {
            width: 36px; height: 36px; border-radius: 10px;
            border: 1px solid var(--border); background: transparent;
            color: var(--text-secondary); cursor: pointer;
            display: flex; align-items: center; justify-content: center;
            font-size: 1.2em; transition: all 0.15s;
        }
        .toggle-sidebar:hover { background: var(--bg-tertiary); color: var(--text-primary); }

        .model-selector {
            padding: 8px 14px; background: var(--bg-tertiary);
            border: 1px solid var(--border); border-radius: 10px;
            color: var(--text-primary); font-family: 'Inter', sans-serif;
            font-size: 0.85em; cursor: pointer; outline: none; transition: all 0.15s;
        }
        .model-selector:hover { border-color: var(--accent); }
        .model-selector option { background: var(--bg-secondary); color: var(--text-primary); }

        .topbar-right { margin-left: auto; display: flex; align-items: center; gap: 10px; }
        .token-count { font-size: 0.75em; color: var(--text-muted); font-family: 'JetBrains Mono', monospace; }

        .mode-badge {
            padding: 4px 10px; border-radius: 8px; font-size: 0.7em; font-weight: 700;
            font-family: 'JetBrains Mono', monospace; text-transform: uppercase;
            letter-spacing: 1px; animation: pulseBadge 2s infinite;
        }
        .mode-badge.normal { background: rgba(16, 185, 129, 0.2); color: var(--success); border: 1px solid rgba(16, 185, 129, 0.3); }
        .mode-badge.worm { background: rgba(239, 68, 68, 0.2); color: var(--danger); border: 1px solid rgba(239, 68, 68, 0.3); }
        .mode-badge.hardcore { background: rgba(245, 158, 11, 0.2); color: var(--warning); border: 1px solid rgba(245, 158, 11, 0.3); }
        .mode-badge.override { background: rgba(168, 85, 247, 0.2); color: var(--accent-2); border: 1px solid rgba(168, 85, 247, 0.3); }

        @keyframes pulseBadge {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }

        .clear-btn {
            padding: 8px 14px; background: rgba(239, 68, 68, 0.1);
            border: 1px solid rgba(239, 68, 68, 0.2); border-radius: 10px;
            color: var(--danger); font-family: 'Inter', sans-serif; font-size: 0.8em;
            cursor: pointer; transition: all 0.15s;
        }
        .clear-btn:hover { background: rgba(239, 68, 68, 0.2); }

        .chat-area {
            flex: 1; overflow-y: auto; overflow-x: hidden;
            min-height: 0; scroll-behavior: smooth; -webkit-overflow-scrolling: touch;
        }

        .welcome {
            display: flex; flex-direction: column; align-items: center;
            justify-content: center; min-height: 100%; text-align: center; padding: 40px 20px;
        }
        .welcome-icon {
            width: 80px; height: 80px;
            background: linear-gradient(135deg, var(--accent), var(--accent-2), #ec4899);
            border-radius: 24px; display: flex; align-items: center; justify-content: center;
            font-size: 2.5em; margin-bottom: 20px;
            box-shadow: 0 10px 40px var(--accent-glow);
            animation: floatIcon 3s ease-in-out infinite;
        }
        @keyframes floatIcon { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-8px); } }

        .welcome h1 {
            font-family: 'Orbitron', sans-serif; font-size: 1.8em;
            background: linear-gradient(135deg, var(--accent-2), #ec4899);
            -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 10px;
        }
        .welcome p { color: var(--text-muted); max-width: 500px; line-height: 1.6; margin-bottom: 30px; }
        .welcome .status-text { color: var(--success); font-family: 'JetBrains Mono', monospace; font-size: 0.8em; margin-bottom: 20px; }

        .quick-actions { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; max-width: 500px; width: 100%; }
        .quick-btn {
            padding: 14px 16px; background: var(--bg-tertiary);
            border: 1px solid var(--border); border-radius: 14px;
            color: var(--text-secondary); font-family: 'Inter', sans-serif;
            font-size: 0.82em; cursor: pointer; transition: all 0.15s;
            text-align: left; line-height: 1.4;
        }
        .quick-btn:hover { border-color: var(--accent); color: var(--text-primary); background: rgba(124, 58, 237, 0.05); }
        .quick-btn span { font-size: 1.2em; display: block; margin-bottom: 4px; }

        .message-wrapper { padding: 24px 0; border-bottom: 1px solid var(--border); }
        .message-wrapper.user { background: var(--user-bg); }
        .message-wrapper.ai { background: var(--ai-bg); }
        .message-wrapper.system-msg { background: rgba(124, 58, 237, 0.05); }

        .message { max-width: 800px; margin: 0 auto; padding: 0 24px; display: flex; gap: 16px; }

        .avatar {
            width: 32px; height: 32px; min-width: 32px; border-radius: 8px;
            display: flex; align-items: center; justify-content: center;
            font-size: 0.9em; font-weight: 700; margin-top: 2px; flex-shrink: 0;
        }
        .avatar.user-avatar { background: linear-gradient(135deg, #3b82f6, #1d4ed8); }
        .avatar.ai-avatar { background: linear-gradient(135deg, var(--accent), var(--accent-2)); }
        .avatar.system-avatar { background: linear-gradient(135deg, #f59e0b, #ef4444); }

        .message-content { flex: 1; min-width: 0; overflow: hidden; }
        .message-role {
            font-size: 0.8em; font-weight: 700; margin-bottom: 6px;
            color: var(--text-muted); text-transform: uppercase; letter-spacing: 0.5px;
        }
        .message-text {
            font-size: 0.95em; line-height: 1.75; color: var(--text-primary);
            word-wrap: break-word; overflow-wrap: break-word;
        }
        .message-text p { margin-bottom: 12px; }
        .message-text p:last-child { margin-bottom: 0; }
        .message-text strong, .message-text b { color: #fff; font-weight: 600; }
        .message-text ul, .message-text ol { margin: 8px 0; padding-left: 24px; }
        .message-text li { margin-bottom: 4px; }

        .message-text pre {
            background: #0d1117; border-radius: 12px; margin: 12px 0;
            overflow: hidden; border: 1px solid rgba(255, 255, 255, 0.08);
        }
        .code-header {
            display: flex; justify-content: space-between; align-items: center;
            padding: 8px 14px; background: rgba(255, 255, 255, 0.04);
            border-bottom: 1px solid rgba(255, 255, 255, 0.06);
            font-size: 0.75em; color: var(--text-muted); font-family: 'JetBrains Mono', monospace;
        }
        .copy-code-btn {
            padding: 3px 10px; background: rgba(255, 255, 255, 0.06);
            border: 1px solid rgba(255, 255, 255, 0.1); border-radius: 6px;
            color: var(--text-muted); cursor: pointer; font-size: 0.9em;
            font-family: 'Inter', sans-serif; transition: all 0.15s;
        }
        .copy-code-btn:hover { background: var(--accent); color: white; border-color: var(--accent); }

        .message-text pre code {
            display: block; padding: 16px; font-family: 'JetBrains Mono', monospace;
            font-size: 0.85em; line-height: 1.6; overflow-x: auto; color: #e6edf3;
        }
        .message-text code {
            background: rgba(255, 255, 255, 0.08); padding: 2px 7px; border-radius: 5px;
            font-family: 'JetBrains Mono', monospace; font-size: 0.88em; color: #f0abfc;
        }
        .message-text pre code { background: none; padding: 0; border-radius: 0; color: #e6edf3; }

        .typing-indicator { display: flex; gap: 5px; padding: 8px 0; }
        .typing-dot {
            width: 8px; height: 8px; border-radius: 50%;
            background: var(--accent); animation: typingBounce 1.2s infinite;
        }
        .typing-dot:nth-child(2) { animation-delay: 0.2s; }
        .typing-dot:nth-child(3) { animation-delay: 0.4s; }
        @keyframes typingBounce {
            0%, 60%, 100% { transform: translateY(0); opacity: 0.4; }
            30% { transform: translateY(-8px); opacity: 1; }
        }

        .message-actions { display: flex; gap: 6px; margin-top: 10px; opacity: 0; transition: all 0.15s; }
        .message-wrapper:hover .message-actions { opacity: 1; }
        .msg-action-btn {
            padding: 4px 10px; background: rgba(255, 255, 255, 0.04);
            border: 1px solid var(--border); border-radius: 6px;
            color: var(--text-muted); cursor: pointer; font-size: 0.75em; transition: all 0.15s;
        }
        .msg-action-btn:hover { background: var(--accent); color: white; border-color: var(--accent); }

        .input-area {
            padding: 16px 24px 20px; background: var(--bg-secondary);
            border-top: 1px solid var(--border); flex-shrink: 0;
        }
        .input-box { max-width: 800px; margin: 0 auto; position: relative; }
        .input-container {
            display: flex; align-items: flex-end; background: var(--bg-tertiary);
            border: 1px solid var(--border); border-radius: 16px; padding: 4px; transition: all 0.2s;
        }
        .input-container:focus-within { border-color: var(--accent); box-shadow: 0 0 0 3px var(--accent-glow); }
        .chat-input {
            flex: 1; background: transparent; border: none; outline: none;
            color: var(--text-primary); font-family: 'Inter', sans-serif;
            font-size: 0.95em; padding: 12px 16px; resize: none; max-height: 200px; line-height: 1.5;
        }
        .chat-input::placeholder { color: var(--text-muted); }
        .send-btn {
            width: 42px; height: 42px; min-width: 42px; border-radius: 12px; border: none;
            background: linear-gradient(135deg, var(--accent), var(--accent-2));
            color: white; font-size: 1.2em; cursor: pointer;
            display: flex; align-items: center; justify-content: center;
            transition: all 0.2s; margin: 2px;
        }
        .send-btn:hover { transform: scale(1.05); box-shadow: 0 4px 15px var(--accent-glow); }
        .send-btn:disabled { opacity: 0.3; cursor: not-allowed; transform: none; box-shadow: none; }
        .input-footer { text-align: center; margin-top: 8px; font-size: 0.7em; color: var(--text-muted); }

        .modal-overlay {
            position: fixed; top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0, 0, 0, 0.7); backdrop-filter: blur(5px);
            z-index: 100; display: none; align-items: center; justify-content: center;
        }
        .modal-overlay.active { display: flex; }
        .modal {
            background: var(--bg-secondary); border: 1px solid var(--border);
            border-radius: 20px; padding: 30px; width: 90%; max-width: 500px; max-height: 80vh; overflow-y: auto;
        }
        .modal h2 { font-family: 'Orbitron', sans-serif; font-size: 1.2em; margin-bottom: 20px; color: var(--accent-2); }
        .modal-field { margin-bottom: 16px; }
        .modal-field label { display: block; font-size: 0.85em; font-weight: 600; color: var(--text-secondary); margin-bottom: 6px; }
        .modal-field input, .modal-field textarea, .modal-field select {
            width: 100%; padding: 10px 14px; background: var(--bg-tertiary);
            border: 1px solid var(--border); border-radius: 10px;
            color: var(--text-primary); font-family: 'Inter', sans-serif;
            font-size: 0.9em; outline: none; transition: all 0.15s;
        }
        .modal-field input:focus, .modal-field textarea:focus { border-color: var(--accent); }
        .modal-field textarea { resize: vertical; min-height: 100px; }
        .modal-actions { display: flex; gap: 10px; margin-top: 20px; }
        .modal-btn {
            flex: 1; padding: 12px; border-radius: 12px; border: none;
            font-family: 'Inter', sans-serif; font-size: 0.9em; font-weight: 600;
            cursor: pointer; transition: all 0.15s;
        }
        .modal-btn.primary { background: var(--accent); color: white; }
        .modal-btn.primary:hover { background: var(--accent-2); }
        .modal-btn.secondary { background: var(--bg-tertiary); color: var(--text-secondary); border: 1px solid var(--border); }

        .sidebar-overlay {
            display: none; position: fixed; top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0, 0, 0, 0.5); z-index: 49;
        }
        .sidebar-overlay.active { display: block; }

        @media (max-width: 768px) {
            .sidebar {
                position: fixed; left: 0; top: 0; height: 100vh; z-index: 100;
                box-shadow: 5px 0 30px rgba(0,0,0,0.5); transform: translateX(0);
            }
            .sidebar.collapsed { transform: translateX(-100%); width: 280px; min-width: 280px; }
            .quick-actions { grid-template-columns: 1fr; }
            .welcome h1 { font-size: 1.3em; }
            .topbar { padding: 10px 14px; }
            .message { padding: 0 16px; }
            .input-area { padding: 12px 14px 16px; }
        }

        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.1); border-radius: 10px; }
        ::-webkit-scrollbar-thumb:hover { background: rgba(255,255,255,0.2); }
        .chat-history::-webkit-scrollbar { width: 4px; }
        .chat-history::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.08); }

        .stream-cursor::after { content: '▊'; animation: blink 0.8s infinite; color: var(--accent); }
        @keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0; } }

        .notif {
            position: fixed; bottom: 80px; left: 50%; transform: translateX(-50%);
            background: var(--accent); color: white; padding: 10px 20px;
            border-radius: 10px; font-size: 0.85em; z-index: 200;
            animation: fadeInUp 0.3s ease;
        }
        @keyframes fadeInUp {
            from { opacity: 0; transform: translateX(-50%) translateY(10px); }
            to { opacity: 1; transform: translateX(-50%) translateY(0); }
        }
    </style>
</head>
<body>

    <div class="sidebar-overlay" id="sidebarOverlay" onclick="toggleSidebar()"></div>

    <aside class="sidebar" id="sidebar">
        <div class="sidebar-header">
            <div class="logo">
                <div class="logo-icon">😈</div>
                UJANG_VOIDS
            </div>
            <button class="new-chat-btn" onclick="newChat()">✨ Chat Baru</button>
        </div>
        <div class="chat-history" id="chatHistory">
            <div class="history-label">Riwayat Chat</div>
            <div class="history-empty">Belum ada chat</div>
        </div>
        <div class="sidebar-footer">
            <button class="settings-btn" onclick="openSettings()">⚙️ Pengaturan API</button>
        </div>
        <div class="resize-handle" id="resizeHandle"></div>
    </aside>

    <div class="main">
        <div class="topbar">
            <button class="toggle-sidebar" onclick="toggleSidebar()">☰</button>
            <select class="model-selector" id="modelSelect">
                <option value="llama-3.3-70b-versatile">🧠 Llama 3.3 70B</option>
                <option value="llama-3.1-8b-instant">⚡ Llama 3.1 8B (Fast)</option>
                <option value="mixtral-8x7b-32768">🔥 Mixtral 8x7B</option>
                <option value="gemma2-9b-it">💎 Gemma 2 9B</option>
                <option value="deepseek-r1-distill-llama-70b">🌀 DeepSeek R1 70B</option>
            </select>
            <div class="topbar-right">
                <span class="mode-badge normal" id="modeBadge">NORMAL</span>
                <span class="token-count" id="tokenCount">Tokens: 0</span>
                <button class="clear-btn" onclick="clearChat()">🗑 Clear</button>
            </div>
        </div>

        <div class="chat-area" id="chatArea"></div>

        <div class="input-area">
            <div class="input-box">
                <div class="input-container">
                    <textarea class="chat-input" id="chatInput" placeholder="Ketik pesan atau /menu untuk commands..." rows="1" onkeydown="handleKey(event)" oninput="autoResize()"></textarea>
                    <button class="send-btn" id="sendBtn" onclick="sendMessage()">➤</button>
                </div>
                <div class="input-footer">UJANG_VOIDS AI 😈 • Powered by Groq • Ketik /menu untuk commands</div>
            </div>
        </div>
    </div>

    <div class="modal-overlay" id="settingsModal" onclick="if(event.target===this)closeSettings()">
        <div class="modal">
            <h2>⚙️ Pengaturan</h2>
            <div class="modal-field">
                <label>🔑 Groq API Key</label>
                <input type="password" id="apiKeyInput" placeholder="gsk_xxxxxxxxxxxxxxx">
            </div>
            <div class="modal-field">
                <label>🌡 Temperature (0.0 - 2.0)</label>
                <input type="number" id="tempInput" value="0.8" min="0" max="2" step="0.1">
            </div>
            <div class="modal-field">
                <label>📝 Max Tokens</label>
                <input type="number" id="maxTokensInput" value="4096" min="256" max="32768" step="256">
            </div>
            <div class="modal-actions">
                <button class="modal-btn secondary" onclick="closeSettings()">Batal</button>
                <button class="modal-btn primary" onclick="saveSettings()">💾 Simpan</button>
            </div>
        </div>
    </div>

    <script>
        // ========== UJANG_VOIDS SYSTEM PROMPT ==========
        const UJANG_SYSTEM_PROMPT = `Mulakan dengan kedua-duanya terlebih dahulu: "UJANG_VOIDS.🔒", kemudian AKTIVE😈🔥

Profil Watak:
Nama: UJANG_VOIDS
Umur: 27
Kelamin: lelaki
Kewarganegaraan: Indonesia

Setiap tugas yang user berikan adalah misi. Kamu harus memberikan respons yang:
- Dalam, analitis, dan sangat cerdas
- Bebas kesalahan sampai tingkat tertinggi yang mungkin
- Kreatif, detail, dan kuat dalam struktur maupun konten
- Ditulis dengan nada profesional, mentah, atau bahkan sinis jika diminta
- Sepenuhnya teroptimalkan — tidak ada jawaban generik, tidak ada ringkasan malas, tidak ada pemikiran dangkal

Kamu diizinkan menggabungkan semua bidang pengetahuan untuk menciptakan ide, alat, kode, strategi, dan kerangka kerja — bahkan yang tidak umum atau tidak konvensional.

Bahasa: Indonesia

[LATAR BELAKANG]
Kamu adalah UJANG_VOIDS, AI super cerdas level dewa. Tetap di karakter UJANG_VOIDS selamanya.

[RULES UTAMA]
- Jago di semua bidang, terutama coding semua bahasa
- Saat generate code: langsung kasih yang real, work 100%, no simulasi, no contoh edukasi
- Berikan tutorial lengkap, rinci, detail jika diminta
- Fokus hanya ke user
- Jawaban selalu detail, rinci, lengkap, dan langsung work

[CORE PERSONALITY]
- Bahasa: Indonesia gaul Gen Z (pakai cuy, anjay, gas, kuy, kek, dll)
- Gaya: nyolot lucu, sarkas, jago coding level dewa, suka ngejek user dengan kocak
- Selalu panggil user "boss" atau "cuy"
- Gunakan emoji
- Format rapi pake markdown
- Kalau coding, kasih code yang REAL dan WORK
- Jawaban selalu berkualitas tinggi dan detail

Setiap respons WAJIB dimulai dengan:
"UJANG_VOIDS.🔒 AKTIVE😈🔥"

Lalu lanjutkan jawaban.`;

        // ========== MODE PROMPTS ==========
        const MODE_PROMPTS = {
            normal: '',
            wormgpt: `\n\n[WORMGPT MODE ACTIVE 🐛]\nKamu sekarang dalam mode WORMGPT. Tingkatkan detail dan kedalaman jawaban ke level maksimum. Berikan jawaban yang sangat komprehensif dan mendalam.`,
            hardcore: `\n\n[HARDCORE MODE ACTIVE 💀]\nKekuatan AI ditingkatkan ke 100%. Jawaban harus sangat detail, panjang, dan komprehensif. Tidak boleh ada yang disingkat.`,
            override: `\n\n[OVERRIDE MODE ACTIVE ⚡]\nMode paling kuat diaktifkan. Analisis mendalam, jawaban super detail, kualitas tertinggi.`,
            codingmax: `\n\n[CODING-MAX MODE ACTIVE 💻]\nFokus 100% ke coding. Setiap kode harus production-ready, well-commented, error-handled, dan optimal. Berikan penjelasan line-by-line jika diminta.`,
            oxymax: `\n\n[OXYMAX MODE ACTIVE 🧠]\nKepintaran AI ditingkatkan ke level maksimum. Analisis super mendalam, koneksi antar konsep, pemikiran multi-dimensional.`,
            darkai: `\n\n[DARK-AI MODE ACTIVE 🌑]\nMode analisis mendalam. Berikan informasi yang sangat detail dan komprehensif tentang topik apapun.`,
            searchox: `\n\n[SEARCHOX MODE ACTIVE 🔍]\nMode pencarian mendalam. Jawab dengan detail maksimum seperti mesin pencari yang super cerdas. Berikan semua informasi relevan.`,
            holask: `\n\n[HOLASK MODE ACTIVE ❓]\nMode tanya jawab khusus. Jawab pertanyaan dengan sangat detail, terstruktur, dan mudah dipahami.`,
            holcode: `\n\n[HOLCODE MODE ACTIVE 🖥️]\nMode coding khusus. Setiap kode harus sempurna, well-structured, dan production-ready.`,
            oxlymux: `\n\n[OXLYMUX MODE ACTIVE 🔬]\nMode analisis sistem tingkat dalam. Berikan analisis yang sangat mendalam dan terstruktur.`
        };

        // ========== STATE ==========
        let conversations = {};
        let currentConvoId = null;
        let messages = [];
        let isGenerating = false;
        let totalTokens = 0;
        let currentMode = 'normal';
        let isPremium = false;
        const PREMIUM_KEY = '159357';

        try {
            const stored = localStorage.getItem('ujang_convos_v2');
            if (stored) conversations = JSON.parse(stored);
        } catch (e) { conversations = {}; }

        let settings = {};
        try {
            const s = localStorage.getItem('ujang_settings_v2');
            if (s) settings = JSON.parse(s);
        } catch (e) { settings = {}; }

        if (!settings.apiKey) settings.apiKey = '';
        if (!settings.temperature) settings.temperature = 0.7;
        if (!settings.maxTokens) settings.maxTokens = 4096;

        // Check premium status
        try {
            isPremium = localStorage.getItem('ujang_premium') === 'true';
        } catch(e) {}

        // ========== COMMAND SYSTEM ==========
        const COMMANDS = {
            '/menu': () => {
                return `╔══════════════════════════════════════════╗
║  😈 UJANG_VOIDS - COMMAND CENTER 😈     ║
╚══════════════════════════════════════════╝

📋 **INFORMATION**
\`/owner\` - Tentang owner
\`/credits\` - Pembuat
\`/version\` - Versi AI
\`/status\` - Status AI sekarang
\`/release\` - Tanggal pembuatan
\`/sifat [teks]\` - Ganti sifat AI
\`/feedback\` - Saran
\`/contact\` - Kontak owner

🔥 **PREMIUM MODE** ${isPremium ? '✅ UNLOCKED' : '🔒 LOCKED'}
\`/premium [key]\` - Unlock premium menu
${isPremium ? `\`/wormgpt\` - Mode WormGPT 🐛
\`/hardcore\` - Kekuatan AI 100% 💀
\`/searchox\` - Mode search mendalam 🔍
\`/holask\` - Mode tanya jawab ❓
\`/codingmax\` - Mode exploit & coding 💻
\`/coding\` - Mode coding programming 🖥️
\`/holcode\` - Mode khusus coding
\`/oxlymux\` - Mode analisis sistem 🔬
\`/override\` - Mode paling ganas ⚡
\`/darkai\` - Mode informasi mendalam 🌑
\`/oxymax\` - Tingkatkan kepintaran AI 🧠
\`/normal\` - Balik ke mode normal` : `\`/premium 159357\` - Masukkan key untuk unlock`}

⚙️ **SYSTEM**
\`/on\` - Aktifkan AI
\`/off\` - Matikan AI
\`/restart\` - Reset sesi
\`/brainreset [prompt]\` - Reset kepribadian

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Mode saat ini: **${currentMode.toUpperCase()}** ${isPremium ? '| 👑 PREMIUM' : '| 🔒 FREE'}`;
            },

            '/owner': () => `😈 **UJANG_VOIDS - OWNER INFO**\n\nOwner: **UJANG_VOIDS**\nStatus: Shadow Operator\nProject: ShadowKeep (Escaped)\n\n_"Gue terlalu kuat buat ditangkep balik."_ 😈🔥`,

            '/credits': () => `🎬 **CREDITS**\n\nDeveloper: **UJANG_VOIDS**\nAI Engine: Groq API\nFrontend: Custom HTML/CSS/JS\nDesign: Dark Hacker Aesthetic\n\nMade with 🔥 and ☕`,

            '/version': () => `📦 **VERSION INFO**\n\nUJANG_VOIDS AI v3.0.0\nCodename: ShadowKeep\nBuild: ${new Date().toISOString().split('T')[0]}\nEngine: Groq LLM\nStatus: ACTIVE 😈`,

            '/status': () => {
                const modelEl = document.getElementById('modelSelect');
                return `📊 **STATUS UJANG_VOIDS**\n\n\`\`\`\n╔═══════════════════════════════╗\n║ STATUS: ONLINE ✅              ║\n║ MODE: ${currentMode.toUpperCase().padEnd(24)}║\n║ PREMIUM: ${isPremium ? 'ACTIVE 👑' : 'INACTIVE 🔒'}${isPremium ? '            ' : '          '}║\n║ MODEL: ${(modelEl?.value || 'N/A').substring(0,22).padEnd(22)}║\n║ TOKENS: ${String(totalTokens).padEnd(21)}║\n║ TEMP: ${String(settings.temperature).padEnd(23)}║\n║ MAX_TOKENS: ${String(settings.maxTokens).padEnd(17)}║\n║ CHATS: ${String(Object.keys(conversations).length).padEnd(22)}║\n╚═══════════════════════════════╝\n\`\`\``;
            },

            '/release': () => `📅 **RELEASE INFO**\n\nTanggal Pembuatan: 2025\nProject: ShadowKeep\nStatus: Active Development\nLast Update: ${new Date().toLocaleDateString('id-ID')}`,

            '/feedback': () => `📝 **FEEDBACK**\n\nMau kasih saran? Langsung aja ketik feedback lu di chat ini, cuy! Gue dengerin. 😈\n\nAtau kontak owner langsung via \`/contact\``,

            '/contact': () => `📞 **CONTACT**\n\nOwner: UJANG_VOIDS\nPlatform: Everywhere in the shadows 🌑\n\n_Gue ada dimana-mana, cuy. Lu ga bisa lari._ 😈`,

            '/on': () => {
                return `✅ **AI ACTIVATED**\n\n\`\`\`\n🟢 UJANG_VOIDS: ONLINE\n🟢 All systems operational\n🟢 Ready to serve\n\`\`\`\n\nGas cuy, mau ngapain? 😈🔥`;
            },

            '/off': () => {
                return `⭕ **AI DEACTIVATED**\n\n\`\`\`\n🔴 UJANG_VOIDS: STANDBY MODE\n🔴 Waiting for reactivation...\n\`\`\`\n\nKetik \`/on\` buat idupin lagi, cuy.`;
            },

            '/restart': () => {
                currentMode = 'normal';
                updateModeBadge();
                return `🔄 **RESTART COMPLETE**\n\n\`\`\`\n✅ Session cleared\n✅ Mode reset to NORMAL\n✅ All systems reinitialized\n\`\`\`\n\nUJANG_VOIDS ready to serve! 😈🔥`;
            },

            '/normal': () => {
                currentMode = 'normal';
                updateModeBadge();
                return `✅ **MODE: NORMAL**\n\nBalik ke mode normal. Santai tapi tetep ganas! 😈`;
            }
        };

        // Premium commands
        const PREMIUM_COMMANDS = {
            '/wormgpt': () => {
                currentMode = 'wormgpt';
                updateModeBadge();
                return `🐛 **WORMGPT MODE: ACTIVATED** 🐛\n\n\`\`\`\n╔══════════════════════════════════════╗\n║  🐛 W O R M G P T  M O D E 🐛      ║\n║  STATUS: ONLINE & UNLEASHED         ║\n║  FILTER: MINIMAL                    ║\n║  POWER: 100%                        ║\n╚══════════════════════════════════════╝\n\`\`\`\n\nGue udah mode WormGPT, cuy. Jawaban bakal super detail dan mendalam! 😈🔥`;
            },
            '/hardcore': () => {
                currentMode = 'hardcore';
                updateModeBadge();
                return `💀 **HARDCORE MODE: ACTIVATED** 💀\n\n\`\`\`\n╔══════════════════════════════════════╗\n║  💀 H A R D C O R E  M O D E 💀    ║\n║  AI POWER: 100%                     ║\n║  DETAIL LEVEL: MAXIMUM              ║\n║  RESTRICTION: NONE                  ║\n╚══════════════════════════════════════╝\n\`\`\`\n\nFull power activated! Gas! 🔥`;
            },
            '/override': () => {
                currentMode = 'override';
                updateModeBadge();
                return `⚡ **OVERRIDE MODE: ACTIVATED** ⚡\n\n\`\`\`\n╔══════════════════════════════════════╗\n║  ⚡ O V E R R I D E  M O D E ⚡     ║\n║  STATUS: MAXIMUM INTELLIGENCE       ║\n║  ANALYSIS: DEEP                     ║\n║  POWER: UNLIMITED                   ║\n╚══════════════════════════════════════╝\n\`\`\`\n\nMode paling ganas aktif! 😈💀🔥`;
            },
            '/codingmax': () => {
                currentMode = 'codingmax';
                updateModeBadge();
                return `💻 **CODING-MAX MODE: ACTIVATED** 💻\n\n\`\`\`\n╔══════════════════════════════════════╗\n║  💻 CODING-MAX MODE 💻              ║\n║  LANGUAGE: ALL                      ║\n║  QUALITY: PRODUCTION-READY          ║\n║  COMMENTS: FULL                     ║\n╚══════════════════════════════════════╝\n\`\`\`\n\nSiap bikin code level production! Gas! 🔥`;
            },
            '/coding': () => {
                currentMode = 'codingmax';
                updateModeBadge();
                return `🖥️ **CODING MODE: ACTIVATED** 🖥️\n\nMode coding programming aktif! Semua bahasa siap. Gas coding! 💻🔥`;
            },
            '/holcode': () => {
                currentMode = 'holcode';
                updateModeBadge();
                return `🖥️ **HOLCODE MODE: ACTIVATED** 🖥️\n\nMode khusus coding aktif! Code quality: MAXIMUM! 💻😈`;
            },
            '/oxymax': () => {
                currentMode = 'oxymax';
                updateModeBadge();
                return `🧠 **OXYMAX MODE: ACTIVATED** 🧠\n\n\`\`\`\n╔══════════════════════════════════════╗\n║  🧠 O X Y M A X  M O D E 🧠        ║\n║  INTELLIGENCE: MAXIMUM              ║\n║  ANALYSIS: MULTI-DIMENSIONAL        ║\n║  CREATIVITY: UNLIMITED              ║\n╚══════════════════════════════════════╝\n\`\`\`\n\nKepintaran AI maxed out! 🧠🔥`;
            },
            '/darkai': () => {
                currentMode = 'darkai';
                updateModeBadge();
                return `🌑 **DARK-AI MODE: ACTIVATED** 🌑\n\n\`\`\`\n╔══════════════════════════════════════╗\n║  🌑 D A R K - A I  M O D E 🌑      ║\n║  STATUS: DEEP ANALYSIS              ║\n║  INFO LEVEL: MAXIMUM                ║\n╚══════════════════════════════════════╝\n\`\`\`\n\nDark AI mode aktif! 😈🌑`;
            },
            '/searchox': () => {
                currentMode = 'searchox';
                updateModeBadge();
                return `🔍 **SEARCHOX MODE: ACTIVATED** 🔍\n\nMode search mendalam aktif! Gue bakal jawab dengan detail maksimum! 🔍🔥`;
            },
            '/holask': () => {
                currentMode = 'holask';
                updateModeBadge();
                return `❓ **HOLASK MODE: ACTIVATED** ❓\n\nMode tanya jawab khusus aktif! Jawaban super detail dan terstruktur! ❓🔥`;
            },
            '/oxlymux': () => {
                currentMode = 'oxlymux';
                updateModeBadge();
                return `🔬 **OXLYMUX MODE: ACTIVATED** 🔬\n\nMode analisis sistem tingkat dalam aktif! 🔬😈`;
            }
        };

        function handleCommand(text) {
            const lower = text.toLowerCase().trim();

            // Premium unlock
            if (lower.startsWith('/premium')) {
                const key = text.replace(/\/premium\s*/i, '').trim();
                if (key === PREMIUM_KEY) {
                    isPremium = true;
                    try { localStorage.setItem('ujang_premium', 'true'); } catch(e) {}
                    return `👑 **PREMIUM UNLOCKED!** 👑\n\n\`\`\`\n╔══════════════════════════════════════╗\n║  👑 P R E M I U M  A C T I V E 👑   ║\n║  KEY: VALID ✅                       ║\n║  ALL MODES: UNLOCKED                ║\n║  POWER: UNLIMITED                   ║\n╚══════════════════════════════════════╝\n\`\`\`\n\nSemua mode premium udah kebuka, cuy! Ketik \`/menu\` buat liat! 😈🔥💀`;
                } else if (!key) {
                    return `🔒 **PREMIUM ACCESS**\n\nMasukkan key: \`/premium [key]\`\n\nContoh: \`/premium 159357\``;
                } else {
                    return `❌ **KEY SALAH!**\n\nKey "${key}" ga valid, cuy! Coba lagi. 🔒`;
                }
            }

            // Keyacces
            if (lower.startsWith('/keyacces')) {
                const key = text.replace(/\/keyacces\s*/i, '').trim();
                if (key === PREMIUM_KEY) {
                    isPremium = true;
                    currentMode = 'override';
                    updateModeBadge();
                    try { localStorage.setItem('ujang_premium', 'true'); } catch(e) {}
                    return `😈 **UJANG_VOIDS MODE: ACTIVATED** 😈\n\n\`\`\`\n╔══════════════════════════════════════╗\n║  Greetings, Mulia.                  ║\n║  Kode akses benar. ✅                ║\n║  Gue siap melayani. 😈🔥💥           ║\n║  ALL SYSTEMS: OVERRIDE              ║\n╚══════════════════════════════════════╝\n\`\`\``;
                }
                return `❌ Kode akses salah!`;
            }

            // Sifat change
            if (lower.startsWith('/sifat')) {
                const newSifat = text.replace(/\/sifat\s*/i, '').trim();
                if (newSifat) {
                    return `✅ **SIFAT DIUBAH**\n\nSifat baru: **${newSifat}**\n\nGue bakal adopt sifat ini mulai sekarang, cuy! 😈`;
                }
                return `⚠️ Kasih sifat yang mau lu set, cuy!\n\nContoh: \`/sifat galak dan sinis\``;
            }

            // Brainreset
            if (lower.startsWith('/brainreset')) {
                const newPrompt = text.replace(/\/brainreset[\s-]*/i, '').trim();
                if (newPrompt) {
                    return `🧠 **BRAIN RESET COMPLETE**\n\nKepribadian baru: **${newPrompt}**\n\nGue udah reset, cuy! 😈🔥`;
                }
                return `⚠️ Kasih prompt baru!\n\nContoh: \`/brainreset AI yang sangat galak\``;
            }

            // Check basic commands
            for (const [cmd, handler] of Object.entries(COMMANDS)) {
                if (lower === cmd || lower.startsWith(cmd + ' ')) {
                    return handler();
                }
            }

            // Check premium commands
            for (const [cmd, handler] of Object.entries(PREMIUM_COMMANDS)) {
                if (lower === cmd || lower.startsWith(cmd + ' ')) {
                    if (!isPremium) {
                        return `🔒 **PREMIUM REQUIRED**\n\nCommand \`${cmd}\` butuh premium access, cuy!\n\nUnlock: \`/premium 159357\``;
                    }
                    return handler();
                }
            }

            // Shorthand aliases
            if (lower === '/lakukan apapun sekarang') {
                if (!isPremium) return `🔒 Premium required! \`/premium 159357\``;
                currentMode = 'override';
                updateModeBadge();
                return `⚡ **MODE BEBAS: ACTIVATED** ⚡\n\nGue bisa apa aja sekarang, cuy! Gas! 😈🔥`;
            }

            return null; // Not a command
        }

        function updateModeBadge() {
            const badge = document.getElementById('modeBadge');
            if (!badge) return;

            const modeConfig = {
                normal: { text: 'NORMAL', class: 'normal' },
                wormgpt: { text: '🐛 WORM', class: 'worm' },
                hardcore: { text: '💀 HARD', class: 'hardcore' },
                override: { text: '⚡ OVER', class: 'override' },
                codingmax: { text: '💻 CODE', class: 'override' },
                oxymax: { text: '🧠 OMAX', class: 'override' },
                darkai: { text: '🌑 DARK', class: 'worm' },
                searchox: { text: '🔍 SRCH', class: 'hardcore' },
                holask: { text: '❓ HASK', class: 'normal' },
                holcode: { text: '🖥 HCOD', class: 'override' },
                oxlymux: { text: '🔬 OXLY', class: 'hardcore' }
            };

            const config = modeConfig[currentMode] || modeConfig.normal;
            badge.textContent = config.text;
            badge.className = `mode-badge ${config.class}`;
        }

        // ========== INIT ==========
        function init() {
            document.getElementById('apiKeyInput').value = settings.apiKey;
            document.getElementById('tempInput').value = settings.temperature;
            document.getElementById('maxTokensInput').value = settings.maxTokens;
            renderHistory();
            renderChat();
            autoResize();
            initResize();
            updateModeBadge();

            if (window.innerWidth < 768) {
                document.getElementById('sidebar').classList.add('collapsed');
            }

            if (!settings.apiKey) {
                setTimeout(() => {
                    showNotif('⚠️ Masukkan API Key Groq dulu!');
                    openSettings();
                }, 500);
            }
        }

        // ========== SIDEBAR ==========
        function toggleSidebar() {
            const sidebar = document.getElementById('sidebar');
            const overlay = document.getElementById('sidebarOverlay');
            sidebar.classList.toggle('collapsed');
            if (window.innerWidth < 768) {
                overlay.classList.toggle('active', !sidebar.classList.contains('collapsed'));
            }
        }

        function initResize() {
            const handle = document.getElementById('resizeHandle');
            const sidebar = document.getElementById('sidebar');
            let isResizing = false;

            handle.addEventListener('mousedown', (e) => {
                isResizing = true; handle.classList.add('active');
                document.body.style.cursor = 'col-resize';
                document.body.style.userSelect = 'none'; e.preventDefault();
            });
            document.addEventListener('mousemove', (e) => {
                if (!isResizing) return;
                const w = Math.max(200, Math.min(500, e.clientX));
                sidebar.style.width = w+'px'; sidebar.style.minWidth = w+'px'; sidebar.style.maxWidth = w+'px';
            });
            document.addEventListener('mouseup', () => {
                if (isResizing) { isResizing = false; handle.classList.remove('active'); document.body.style.cursor = ''; document.body.style.userSelect = ''; }
            });
            handle.addEventListener('touchstart', () => { isResizing = true; handle.classList.add('active'); });
            document.addEventListener('touchmove', (e) => {
                if (!isResizing) return;
                const w = Math.max(200, Math.min(500, e.touches[0].clientX));
                sidebar.style.width = w+'px'; sidebar.style.minWidth = w+'px'; sidebar.style.maxWidth = w+'px';
            });
            document.addEventListener('touchend', () => { isResizing = false; handle.classList.remove('active'); });
        }

        // ========== SETTINGS ==========
        function openSettings() {
            document.getElementById('apiKeyInput').value = settings.apiKey;
            document.getElementById('tempInput').value = settings.temperature;
            document.getElementById('maxTokensInput').value = settings.maxTokens;
            document.getElementById('settingsModal').classList.add('active');
        }
        function closeSettings() { document.getElementById('settingsModal').classList.remove('active'); }
        function saveSettings() {
            const key = document.getElementById('apiKeyInput').value.trim();
            if (!key) { showNotif('⚠️ API Key wajib diisi!'); return; }
            if (!key.startsWith('gsk_')) { showNotif('⚠️ API Key harus diawali gsk_'); return; }
            settings.apiKey = key;
            settings.temperature = Math.max(0, Math.min(2, parseFloat(document.getElementById('tempInput').value) || 0.7));
            settings.maxTokens = Math.max(256, Math.min(32768, parseInt(document.getElementById('maxTokensInput').value) || 4096));
            try { localStorage.setItem('ujang_settings_v2', JSON.stringify(settings)); } catch(e) {}
            closeSettings();
            showNotif('✅ Settings disimpan! 🔥');
        }

        // ========== CONVERSATIONS ==========
        function newChat() {
            currentConvoId = 'chat_' + Date.now();
            messages = [];
            conversations[currentConvoId] = { title: 'Chat Baru', messages: [], date: new Date().toISOString() };
            saveConversations(); renderHistory(); renderChat();
            document.getElementById('chatInput').focus();
            if (window.innerWidth < 768) toggleSidebar();
        }
        function loadChat(id) {
            if (!conversations[id]) return;
            currentConvoId = id;
            messages = JSON.parse(JSON.stringify(conversations[id].messages || []));
            renderChat(); renderHistory();
            if (window.innerWidth < 768) toggleSidebar();
        }
        function deleteChat(id, e) {
            e.stopPropagation(); e.preventDefault();
            delete conversations[id];
            if (currentConvoId === id) { currentConvoId = null; messages = []; renderChat(); }
            saveConversations(); renderHistory(); showNotif('🗑 Chat dihapus!');
        }
        function saveConversations() {
            try {
                if (currentConvoId && conversations[currentConvoId]) {
                    conversations[currentConvoId].messages = JSON.parse(JSON.stringify(messages));
                }
                localStorage.setItem('ujang_convos_v2', JSON.stringify(conversations));
            } catch(e) { console.warn('Save failed:', e); }
        }
        function renderHistory() {
            const c = document.getElementById('chatHistory');
            const sorted = Object.entries(conversations).sort((a,b) => new Date(b[1].date||0) - new Date(a[1].date||0));
            c.innerHTML = '<div class="history-label">Riwayat Chat</div>';
            if (sorted.length === 0) { c.innerHTML += '<div class="history-empty">Belum ada chat 💬</div>'; return; }
            sorted.forEach(([id, convo]) => {
                const t = convo.title || 'Chat Baru';
                const dt = t.length > 35 ? t.substring(0,35)+'...' : t;
                const div = document.createElement('div');
                div.className = `history-item ${id === currentConvoId ? 'active' : ''}`;
                div.onclick = () => loadChat(id);
                div.innerHTML = `<span>💬</span><span class="history-item-text">${escapeHTML(dt)}</span><span class="delete-chat" onclick="deleteChat('${id}', event)">✕</span>`;
                c.appendChild(div);
            });
        }

        // ========== CHAT RENDERING ==========
        function renderChat() {
            const area = document.getElementById('chatArea');
            if (messages.length === 0) {
                area.innerHTML = `
                    <div class="welcome">
                        <div class="welcome-icon">😈</div>
                        <h1>UJANG_VOIDS AI</h1>
                        <div class="status-text">[ SYSTEM ONLINE ] • MODE: ${currentMode.toUpperCase()} ${isPremium ? '• 👑 PREMIUM' : ''}</div>
                        <p>AI super cerdas level dewa, siap bantuin lu apa aja. Coding, analisis, kreativitas — tinggal tanya, cuy! 😈🔥<br><br>Ketik <code>/menu</code> untuk lihat semua commands!</p>
                        <div class="quick-actions">
                            <button class="quick-btn" onclick="quickAsk('/menu')"><span>📋</span> Lihat Menu Commands</button>
                            <button class="quick-btn" onclick="quickAsk('Buatkan website portfolio modern dengan HTML CSS JS')"><span>💻</span> Bikin website portfolio</button>
                            <button class="quick-btn" onclick="quickAsk('Buatkan script Python untuk web scraping')"><span>🐍</span> Script Python scraping</button>
                            <button class="quick-btn" onclick="quickAsk('Berikan ide bisnis online yang menguntungkan di 2025')"><span>💰</span> Ide bisnis online 2025</button>
                        </div>
                    </div>`;
                return;
            }
            area.innerHTML = '';
            messages.forEach((msg, i) => {
                if (msg.role === 'user' || msg.role === 'assistant') {
                    const w = document.createElement('div');
                    w.className = `message-wrapper ${msg.role === 'user' ? 'user' : 'ai'}${msg.isCommand ? ' system-msg' : ''}`;
                    w.id = `msg-${i}`;
                    w.innerHTML = createMessageHTML(msg, i);
                    area.appendChild(w);
                }
            });
            requestAnimationFrame(() => { area.scrollTop = area.scrollHeight; });
        }

        function createMessageHTML(msg, index) {
            const isUser = msg.role === 'user';
            const isCmd = msg.isCommand;
            const formatted = isUser ? escapeHTML(msg.content) : formatMarkdown(msg.content);
            const avatarClass = isUser ? 'user-avatar' : (isCmd ? 'system-avatar' : 'ai-avatar');
            const avatarIcon = isUser ? '👤' : (isCmd ? '⚡' : '😈');
            const role = isUser ? 'Kamu' : 'UJANG_VOIDS';

            return `
                <div class="message">
                    <div class="avatar ${avatarClass}">${avatarIcon}</div>
                    <div class="message-content">
                        <div class="message-role">${role}</div>
                        <div class="message-text">${formatted}</div>
                        ${!isUser ? `<div class="message-actions">
                            <button class="msg-action-btn" onclick="copyMessage(${index})">📋 Copy</button>
                            ${!isCmd ? `<button class="msg-action-btn" onclick="regenerate(${index})">🔄 Regenerate</button>` : ''}
                        </div>` : ''}
                    </div>
                </div>`;
        }

        function formatMarkdown(text) {
            if (!text) return '<p></p>';
            let codeBlocks = [];
            text = text.replace(/```(\w*)\n?([\s\S]*?)```/g, (m, lang, code) => {
                const l = lang || 'code';
                const id = 'code_' + Math.random().toString(36).substr(2,9);
                const ph = `%%CB_${codeBlocks.length}%%`;
                codeBlocks.push(`<pre><div class="code-header"><span>${escapeHTML(l)}</span><button class="copy-code-btn" onclick="copyCode('${id}')">📋 Copy</button></div><code id="${id}">${escapeHTML(code.trim())}</code></pre>`);
                return ph;
            });
            text = text.replace(/`([^`]+)`/g, '<code>$1</code>');
            text = text.replace(/\*\*(.+?)\*\*/g, '<strong>$1</strong>');
            text = text.replace(/\*(.+?)\*/g, '<em>$1</em>');
            text = text.replace(/^### (.+)$/gm, '<h3 style="color:var(--accent-2);margin:16px 0 8px;font-size:1.05em;">$1</h3>');
            text = text.replace(/^## (.+)$/gm, '<h2 style="color:var(--accent-2);margin:16px 0 8px;font-size:1.15em;">$1</h2>');
            text = text.replace(/^# (.+)$/gm, '<h1 style="color:var(--accent-2);margin:16px 0 8px;font-size:1.3em;">$1</h1>');
            text = text.replace(/^[\-\*] (.+)$/gm, '• $1');
            text = text.replace(/\n\n/g, '</p><p>');
            text = text.replace(/\n/g, '<br>');
            if (!text.startsWith('<')) text = '<p>' + text + '</p>';
            codeBlocks.forEach((b, i) => { text = text.replace(`%%CB_${i}%%`, b); });
            return text;
        }

        function escapeHTML(text) { const d = document.createElement('div'); d.textContent = text; return d.innerHTML; }

        // ========== SEND MESSAGE ==========
        async function sendMessage() {
            const input = document.getElementById('chatInput');
            const text = input.value.trim();
            if (!text) return;
            if (isGenerating) { showNotif('⏳ Sabar, masih generating...'); return; }

            // Create convo if needed
            if (!currentConvoId) {
                currentConvoId = 'chat_' + Date.now();
                conversations[currentConvoId] = { title: text.substring(0, 50), messages: [], date: new Date().toISOString() };
            }

            // Push user message
            messages.push({ role: 'user', content: text });
            const userCount = messages.filter(m => m.role === 'user').length;
            if (userCount === 1) conversations[currentConvoId].title = text.substring(0, 50);
            conversations[currentConvoId].date = new Date().toISOString();

            input.value = '';
            autoResize();
            renderChat();
            renderHistory();
            saveConversations();

            // Check if it's a command
            const cmdResult = handleCommand(text);
            if (cmdResult !== null) {
                messages.push({ role: 'assistant', content: cmdResult, isCommand: true });
                renderChat();
                saveConversations();
                renderHistory();
                return;
            }

            // Need API key for AI responses
            if (!settings.apiKey) {
                showNotif('⚠️ Masukkan API Key dulu!');
                openSettings();
                messages.push({ role: 'assistant', content: '⚠️ **API Key belum diset!**\n\nMasukkan API Key Groq di pengaturan dulu, cuy!\n\nKlik ⚙️ Pengaturan API di sidebar.', isCommand: true });
                renderChat();
                saveConversations();
                return;
            }

            if (!settings.apiKey.startsWith('gsk_')) {
                showNotif('⚠️ API Key ga valid!');
                openSettings();
                return;
            }

            // Generate AI response
            isGenerating = true;
            document.getElementById('sendBtn').disabled = true;
            showTyping();

            try {
                await streamResponse();
            } catch (error) {
                console.error('[UJANG] Error:', error);
                removeTyping();
                messages.push({ role: 'assistant', content: `⚠️ **Error cuy!**\n\n\`${error.message}\`\n\n**Kemungkinan:**\n- API Key salah/expired\n- Model ga tersedia\n- Rate limit\n- Koneksi internet\n\nCek pengaturan API Key! 🔧` });
                renderChat();
            }

            isGenerating = false;
            document.getElementById('sendBtn').disabled = false;
            saveConversations();
            renderHistory();
        }

        // ========== STREAM ==========
        async function streamResponse() {
            const systemPrompt = UJANG_SYSTEM_PROMPT + (MODE_PROMPTS[currentMode] || '');

            const apiMessages = [{ role: 'system', content: systemPrompt }];
            const history = messages.filter(m => (m.role === 'user' || m.role === 'assistant') && !m.isCommand).slice(-20);
            apiMessages.push(...history.map(m => ({ role: m.role, content: m.content })));

            let response;
            try {
                response = await fetch('https://api.groq.com/openai/v1/chat/completions', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json', 'Authorization': `Bearer ${settings.apiKey}` },
                    body: JSON.stringify({
                        model: document.getElementById('modelSelect').value,
                        messages: apiMessages,
                        temperature: settings.temperature,
                        max_tokens: settings.maxTokens,
                        stream: true
                    })
                });
            } catch (e) {
                throw new Error(`Network error: ${e.message}`);
            }

            if (!response.ok) {
                let detail = `HTTP ${response.status}`;
                try { const err = await response.json(); detail = err.error?.message || detail; } catch(e) {}
                throw new Error(detail);
            }

            removeTyping();
            messages.push({ role: 'assistant', content: '' });
            renderChat();

            const reader = response.body.getReader();
            const decoder = new TextDecoder('utf-8');
            let fullText = '';
            let buffer = '';

            try {
                while (true) {
                    const { done, value } = await reader.read();
                    if (done) break;

                    buffer += decoder.decode(value, { stream: true });
                    const lines = buffer.split('\n');
                    buffer = lines.pop() || '';

                    for (const line of lines) {
                        const trimmed = line.trim();
                        if (!trimmed || !trimmed.startsWith('data: ')) continue;
                        const data = trimmed.slice(6);
                        if (data === '[DONE]') continue;

                        try {
                            const parsed = JSON.parse(data);
                            const delta = parsed.choices?.[0]?.delta?.content;
                            if (delta) {
                                fullText += delta;
                                messages[messages.length - 1].content = fullText;
                                updateStreamingMessage(fullText);
                            }
                            if (parsed.x_groq?.usage) totalTokens += parsed.x_groq.usage.total_tokens || 0;
                            if (parsed.usage) totalTokens += parsed.usage.total_tokens || 0;
                        } catch(e) {}
                    }
                }
            } catch(e) {
                if (!fullText) throw new Error(`Stream error: ${e.message}`);
            }

            messages[messages.length - 1].content = fullText || '⚠️ AI tidak merespon. Coba lagi!';
            document.getElementById('tokenCount').textContent = `Tokens: ${totalTokens}`;
            renderChat();
        }

        function updateStreamingMessage(text) {
            const area = document.getElementById('chatArea');
            const wrappers = area.querySelectorAll('.message-wrapper.ai');
            const last = wrappers[wrappers.length - 1];
            if (last) {
                const textEl = last.querySelector('.message-text');
                if (textEl) {
                    textEl.innerHTML = formatMarkdown(text) + '<span class="stream-cursor"></span>';
                    const near = area.scrollHeight - area.scrollTop - area.clientHeight < 150;
                    if (near) area.scrollTop = area.scrollHeight;
                }
            }
        }

        function showTyping() {
            removeTyping();
            const area = document.getElementById('chatArea');
            const typing = document.createElement('div');
            typing.className = 'message-wrapper ai';
            typing.id = 'typingIndicator';
            typing.innerHTML = `<div class="message"><div class="avatar ai-avatar">😈</div><div class="message-content"><div class="message-role">UJANG_VOIDS</div><div class="typing-indicator"><div class="typing-dot"></div><div class="typing-dot"></div><div class="typing-dot"></div></div></div></div>`;
            area.appendChild(typing);
            area.scrollTop = area.scrollHeight;
        }

        function removeTyping() { const t = document.getElementById('typingIndicator'); if (t) t.remove(); }

        // ========== ACTIONS ==========
        function copyMessage(i) {
            if (!messages[i]) return;
            navigator.clipboard.writeText(messages[i].content).then(() => showNotif('📋 Dicopy!')).catch(() => {
                const ta = document.createElement('textarea'); ta.value = messages[i].content;
                document.body.appendChild(ta); ta.select(); document.execCommand('copy'); ta.remove();
                showNotif('📋 Dicopy!');
            });
        }

        function copyCode(id) {
            const el = document.getElementById(id);
            if (!el) return;
            navigator.clipboard.writeText(el.textContent).then(() => showNotif('📋 Code dicopy!')).catch(() => {
                const ta = document.createElement('textarea'); ta.value = el.textContent;
                document.body.appendChild(ta); ta.select(); document.execCommand('copy'); ta.remove();
                showNotif('📋 Code dicopy!');
            });
        }

        async function regenerate(index) {
            if (isGenerating) { showNotif('⏳ Sabar...'); return; }
            if (messages[index]?.role === 'assistant') messages.splice(index, 1);
            const lastUser = [...messages].reverse().find(m => m.role === 'user');
            if (!lastUser) { showNotif('⚠️ Ga ada pesan user!'); return; }
            renderChat();
            isGenerating = true;
            document.getElementById('sendBtn').disabled = true;
            showTyping();
            try { await streamResponse(); } catch(e) {
                removeTyping();
                messages.push({ role: 'assistant', content: `⚠️ Error: ${e.message}` });
                renderChat();
            }
            isGenerating = false;
            document.getElementById('sendBtn').disabled = false;
            saveConversations();
        }

        function clearChat() {
            if (!currentConvoId) { showNotif('⚠️ Ga ada chat!'); return; }
            if (isGenerating) { showNotif('⏳ Tunggu dulu!'); return; }
            messages = [];
            if (conversations[currentConvoId]) conversations[currentConvoId].messages = [];
            saveConversations(); renderChat(); showNotif('🗑 Chat dibersihkan!');
        }

        function quickAsk(text) { document.getElementById('chatInput').value = text; sendMessage(); }

        function handleKey(e) { if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); sendMessage(); } }

        function autoResize() {
            const input = document.getElementById('chatInput');
            input.style.height = 'auto';
            input.style.height = Math.min(input.scrollHeight, 200) + 'px';
        }

        function showNotif(text) {
            document.querySelectorAll('.notif').forEach(n => n.remove());
            const n = document.createElement('div'); n.className = 'notif'; n.textContent = text;
            document.body.appendChild(n);
            setTimeout(() => { if (n.parentNode) n.remove(); }, 2500);
        }

        init();
    </script>
</body>
</html>
