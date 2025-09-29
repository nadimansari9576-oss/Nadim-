<!doctype html>

<html lang="hi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Caption & Description Downloader</title>
  <style>
    :root{--bg:#f7f8fb;--card:#ffffff;--muted:#6b7280;--accent:#0b5ed7}
    *{box-sizing:border-box;font-family:Inter,system-ui,Segoe UI,Roboto,'Noto Sans',Arial}
    body{margin:0;background:var(--bg);color:#0f172a;padding:28px}
    .wrap{max-width:980px;margin:0 auto}
    header{display:flex;align-items:center;gap:16px;margin-bottom:18px}
    h1{font-size:20px;margin:0}
    .card{background:var(--card);border-radius:12px;padding:18px;box-shadow:0 6px 18px rgba(12,18,31,0.06)}
    label{display:block;font-weight:600;margin:12px 0 6px}
    textarea{width:100%;min-height:120px;padding:12px;border:1px solid #e6e9ef;border-radius:8px;resize:vertical;font-size:14px}
    .row{display:flex;gap:12px;flex-wrap:wrap}
    .controls{display:flex;gap:10px;margin-top:12px}
    button{background:var(--accent);color:#fff;border:0;padding:10px 14px;border-radius:8px;cursor:pointer}
    button.secondary{background:#e6e9ef;color:#0f172a}
    .small{font-size:13px;color:var(--muted)}
    .options{display:flex;gap:8px;align-items:center;margin-top:8px}
    .option{display:flex;gap:6px;align-items:center}
    .preview{margin-top:14px;padding:12px;background:#fbfdff;border:1px solid #eef3ff;border-radius:8px;white-space:pre-wrap}
    footer{margin-top:14px;color:var(--muted);font-size:13px}
    @media(max-width:700px){.row{flex-direction:column}}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <h1>Caption & Description Downloader</h1>
      <div class="small">Yahan aap caption aur description likh kar unhe download kar sakte hain (TXT / JSON).</div>
    </header><div class="card">
  <label for="caption">Caption (ek short caption):</label>
  <textarea id="caption" placeholder="Yahan apna caption likhen..."></textarea>

  <label for="description">Description (lambi description):</label>
  <textarea id="description" placeholder="Yahan description likhen..."></textarea>

  <div class="options">
    <div class="option">
      <input type="checkbox" id="includeMeta" checked>
      <label for="includeMeta" class="small">Date & filename metadata include karen</label>
    </div>
    <div class="option">
      <label class="small">Format:</label>
      <select id="formatSelect">
        <option value="txt">.txt</option>
        <option value="json">.json</option>
      </select>
    </div>
  </div>

  <div class="controls">
    <button id="downloadCaption">Download Caption</button>
    <button id="downloadDescription">Download Description</button>
    <button id="downloadBoth">Download Both (Single File)</button>
    <button class="secondary" id="clear">Clear</button>
  </div>

  <div class="small" style="margin-top:12px">Preview:</div>
  <div id="preview" class="preview">Caption aur description ka preview yahan dikhai dega.</div>

  <footer>
    Tip: Agar aap social media ke liye alag-alag captions chahte hain, har ek ko nayi line me likh kar "Download Description" kar sakte hain aur baad me copy-paste karke upload karein.
  </footer>
</div>

  </div>  <script>
    const captionEl = document.getElementById('caption')
    const descriptionEl = document.getElementById('description')
    const preview = document.getElementById('preview')
    const includeMeta = document.getElementById('includeMeta')
    const formatSelect = document.getElementById('formatSelect')

    function updatePreview(){
      const c = captionEl.value.trim()
      const d = descriptionEl.value.trim()
      let text = ''
      if(c) text += 'Caption:\n' + c + '\n\n'
      if(d) text += 'Description:\n' + d
      if(!text) text = 'Caption aur description ka preview yahan dikhai dega.'
      preview.textContent = text
    }

    captionEl.addEventListener('input', updatePreview)
    descriptionEl.addEventListener('input', updatePreview)
    updatePreview()

    function makeFilename(base, ext){
      const now = new Date()
      const y = now.getFullYear()
      const m = String(now.getMonth()+1).padStart(2,'0')
      const d = String(now.getDate()).padStart(2,'0')
      return `${base}-${y}${m}${d}.${ext}`
    }

    function downloadFile(filename, content){
      const blob = new Blob([content], {type: 'text/plain;charset=utf-8'})
      const url = URL.createObjectURL(blob)
      const a = document.createElement('a')
      a.href = url
      a.download = filename
      document.body.appendChild(a)
      a.click()
      a.remove()
      URL.revokeObjectURL(url)
    }

    document.getElementById('downloadCaption').addEventListener('click', ()=>{
      const c = captionEl.value.trim()
      if(!c){alert('Caption khali hai — pehle kuch likhen.'); return}
      const fmt = formatSelect.value
      if(fmt === 'txt'){
        let content = c
        if(includeMeta.checked){ content = `Generated: ${new Date().toLocaleString()}\n\n` + content }
        const fna
