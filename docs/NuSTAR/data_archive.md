<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>DS9 Local Setup with Remote Cluster Files (macOS)</title>
  <style>
    body {
      font-family: "Inter", system-ui, -apple-system, sans-serif;
      line-height: 1.6;
      color: #333;
      background: #fafafa;
      margin: 2rem auto;
      max-width: 900px;
      padding: 0 1.5rem;
    }
    h1, h2, h3 {
      color: #004a7f;
    }
    h1 {
      border-bottom: 2px solid #004a7f;
      padding-bottom: 0.3em;
    }
    pre {
      background: #1e1e1e;
      color: #e0e0e0;
      padding: 1rem;
      border-radius: 8px;
      overflow-x: auto;
    }
    code {
      background: #eee;
      padding: 2px 4px;
      border-radius: 4px;
      font-family: "JetBrains Mono", monospace;
    }
    a {
      color: #0066cc;
      text-decoration: none;
    }
    a:hover {
      text-decoration: underline;
    }
    ul {
      margin-left: 1.2rem;
    }
    blockquote {
      background: #f5faff;
      border-left: 4px solid #0078d4;
      padding: 0.8rem 1rem;
      color: #004a7f;
      font-style: italic;
    }
    table {
      border-collapse: collapse;
      width: 100%;
      margin: 1.2rem 0;
    }
    th, td {
      border: 1px solid #ddd;
      padding: 0.5rem 0.8rem;
    }
    th {
      background: #eaf2f8;
      text-align: left;
    }
  </style>
</head>
<body>

<h1>🛰️ Using DS9 Locally with Remote Cluster Files (macOS, No Homebrew)</h1>

<p>
This guide explains how to:
</p>
<ul>
  <li>Install <strong>SAOImage DS9</strong> manually on macOS</li>
  <li>Create a <code>ds9ssh</code> command to:
    <ul>
      <li>Copy FITS or EVT files from your cluster</li>
      <li>Open them locally in DS9</li>
      <li>Upload any saved <code>.reg</code> region files back to the same cluster folder</li>
      <li>Automatically clean up temporary files</li>
    </ul>
  </li>
</ul>

<hr>

<h2>1️⃣ Install DS9 Manually (No Homebrew)</h2>

<ol>
  <li>Go to the official DS9 download page:<br>
    👉 <a href="https://ds9.si.edu/site/Download.html" target="_blank">https://ds9.si.edu/site/Download.html</a></li>
  <li>Download the correct macOS version:
    <ul>
      <li>Apple Silicon (M1/M2/M3): <code>ds9-macos-arm64.dmg</code></li>
      <li>Intel Mac: <code>ds9-macos-x86_64.dmg</code></li>
    </ul>
  </li>
  <li>Open the <code>.dmg</code> and drag <strong>SAOImageDS9.app</strong> to <code>/Applications</code>.</li>
  <li>Right-click → <em>Open</em> (first time only) to bypass macOS security.</li>
  <li>Test your installation:</li>
</ol>

<pre><code>open -a SAOImageDS9</code></pre>

<p>A blank DS9 window should appear.</p>

<hr>

<h2>2️⃣ Add DS9 to the Terminal Path</h2>

<pre><code>echo "alias ds9='/Applications/SAOImageDS9.app/Contents/MacOS/ds9'" >> ~/.zshrc
source ~/.zshrc
</code></pre>

<p>Now you can start DS9 directly from the terminal:</p>

<pre><code>ds9 &</code></pre>

<hr>

<h2>3️⃣ Create the <code>ds9ssh</code> Helper Command</h2>

<p>
The <code>ds9ssh</code> function:
</p>
<ul>
  <li>Copies remote <code>.fits</code> or <code>.evt</code> files into a temporary folder</li>
  <li>Opens them in your local DS9 (no X11 or lag)</li>
  <li>Uploads any <code>.reg</code> region files you save back to the cluster</li>
  <li>Deletes the temporary directory automatically</li>
</ul>

<h3>Step — Add to your shell configuration</h3>

<pre><code>nano ~/.zshrc</code></pre>

<p>Paste the following at the bottom:</p>

<pre><code class="language-zsh"># ---- ds9ssh: copy -> open -> upload .reg back -> cleanup ----
ds9ssh() {
  if [ $# -lt 1 ]; then
    echo "Usage: ds9ssh USER@HOST:/abs/path/file.[fits|evt]"
    return 1
  fi

  # Parse first remote argument
  local first="$1"
  local remote_host="${first%%:*}"          # USER@HOST
  local remote_path="${first#*:}"           # /abs/path/file
  local remote_dir="${remote_path:h}"       # directory path on remote

  # Temp dir
  local tmpdir
  tmpdir=$(mktemp -d /tmp/ds9_XXXX) || { echo "mktemp failed"; return 1; }

  echo "Copying to $tmpdir ..."
  if ! scp "$@" "$tmpdir/"; then
    echo "❌ Copy failed. Check VPN/SSH."
    rm -rf "$tmpdir"
    return 1
  fi

  setopt localoptions null_glob
  local files=( "$tmpdir"/*.fits "$tmpdir"/*.FITS "$tmpdir"/*.fit "$tmpdir"/*.FIT "$tmpdir"/*.evt "$tmpdir"/*.EVT )

  if (( ${#files} == 0 )); then
    echo "❌ No .fits/.evt files after copy."
    rm -rf "$tmpdir"
    return 1
  fi

  echo "Launching DS9 (working dir: $tmpdir) ..."
  ( cd "$tmpdir" && /Applications/SAOImageDS9.app/Contents/MacOS/ds9 "${files[@]}" ) &
  local pid=$!
  wait $pid 2>/dev/null

  # Upload region files back to the remote directory
  local regs=( "$tmpdir"/*.reg )
  if (( ${#regs} )); then
    echo "Uploading ${#regs} region file(s) to ${remote_host}:${remote_dir}/ ..."
    scp "$tmpdir"/*.reg "${remote_host}:${remote_dir}/" || echo "⚠️ Upload of regions failed."
  else
    echo "No .reg files to upload."
  fi

  echo "Cleaning up $tmpdir ..."
  rm -rf "$tmpdir"
  echo "Done."
}
# ---- end ds9ssh ----
</code></pre>

<p>Save and exit (<code>Ctrl+O</code>, <code>Enter</code>, <code>Ctrl+X</code>), then reload:</p>

<pre><code>source ~/.zshrc</code></pre>

<hr>

<h2>4️⃣ Usage</h2>

<p>To open a remote FITS or EVT file from the cluster:</p>

<pre><code>ds9ssh interactive:/ceph/users/fcandon/NuSTAR_SUN/.../nu80514219001A06_cl.evt
</code></pre>

<p>✅ What happens:</p>
<ol>
  <li>File is copied to <code>/tmp/ds9_XXXX</code></li>
  <li>DS9 opens locally (fast, responsive)</li>
  <li>You can create and save regions</li>
  <li>When DS9 closes:
    <ul>
      <li>All <code>.reg</code> files are uploaded back to the same cluster folder</li>
      <li>The temporary folder is deleted</li>
    </ul>
  </li>
</ol>

<hr>

<h2>5️⃣ Verify Region Upload</h2>

<p>After saving a region inside DS9 and closing it, check on the cluster:</p>

<pre><code>ssh interactive
ls /ceph/users/fcandon/.../event_cl/*.reg
</code></pre>

<p>Your region file should appear there.</p>

<hr>

<h2>6️⃣ Cleanup</h2>

<p>Temporary folders like <code>/tmp/ds9_xxxx</code> are automatically deleted after DS9 exits.</p>

<hr>

<h2>7️⃣ Example Workflow</h2>

<pre><code># Copy and open a remote event file
ds9ssh interactive:/home/fcandon/test_evt.evt

# In DS9:
#   → Create a region
#   → Save it (default temp folder)
#   → Close DS9
# The region file is uploaded automatically to the same cluster directory.
</code></pre>

<hr>

<h2>🔐 (Optional) SSH Keys for Password-Free Access</h2>

<pre><code>ssh-keygen -t ed25519 -C "fcandon@macbook"
ssh-copy-id fcandon@interactive.e5.physik.tu-dortmund.de
</code></pre>

<p>Now <code>ds9ssh</code> runs without asking for a password.</p>

<hr>

<h2>🧠 Summary</h2>

<table>
  <thead>
    <tr><th>Action</th><th>Command</th></tr>
  </thead>
  <tbody>
    <tr><td>Open DS9 locally</td><td><code>open -a SAOImageDS9</code></td></tr>
    <tr><td>Open remote FITS/EVT file</td><td><code>ds9ssh user@host:/path/to/file.evt</code></td></tr>
    <tr><td>Save region file</td><td>DS9 → Region → Save Regions…</td></tr>
    <tr><td>Region auto-upload</td><td>Automatically on DS9 close</td></tr>
    <tr><td>Cleanup</td><td>Temp files auto-deleted</td></tr>
  </tbody>
</table>

<blockquote>
  💡 DS9 runs <strong>natively</strong> on your Mac — no lag, no X11 forwarding.  
  Only the FITS/EVT file and any region files are transferred over SSH.
</blockquote>

</body>
</html>



