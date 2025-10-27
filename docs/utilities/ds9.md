# DS9 Local Setup for Remote Cluster Data (macOS, No Homebrew)

This guide explains how to configure **SAOImage DS9** on macOS to open FITS or EVT files stored on a remote cluster.  
The setup allows you to:

1. Copy remote files to a temporary local folder.
2. Open them in your local DS9 (no X11 forwarding required).
3. Automatically upload any region files (`.reg`) you save back to the original cluster directory.
4. Automatically clean up temporary files when DS9 closes.

---

# Step-by-Step

## 1) Install DS9 (manually, without Homebrew)

1. Visit the official DS9 download page:  
   👉 [https://ds9.si.edu/site/Download.html](https://ds9.si.edu/site/Download.html)

2. Download the correct macOS version:
   - **Apple Silicon (M1/M2/M3)** → `ds9-macos-arm64.dmg`  
   - **Intel Mac** → `ds9-macos-x86_64.dmg`

3. Open the `.dmg` file and drag **SAOImageDS9.app** into your `/Applications` folder.

4. The first time you open it, macOS may block it.  
   Right-click → **Open** → confirm to allow it.

5. Test the installation:
   ```bash
   open -a SAOImageDS9
````

You should see the DS9 window open.

---

## 2) Add DS9 to Your Terminal Path

Add a shortcut so you can open DS9 directly from the command line.

```bash
echo "alias ds9='/Applications/SAOImageDS9.app/Contents/MacOS/ds9'" >> ~/.zshrc
source ~/.zshrc
```

Now you can launch DS9 with:

```bash
ds9 &
```

---

## 3) Create the `ds9ssh` Command

This helper function allows you to:

* Copy `.fits` or `.evt` files from your cluster to a temporary folder.
* Open them in DS9 locally on your Mac.
* Automatically upload `.reg` region files back to the same directory on the cluster.
* Delete all temporary files after DS9 closes.

### 3.1 Edit your shell configuration

Open your shell configuration file:

```bash
nano ~/.zshrc
```

Scroll to the bottom and paste:

```bash
# ---- ds9ssh: copy -> open -> upload .reg back -> cleanup ----
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
    echo "❌ Copy failed. Check VPN/SSH connection."
    rm -rf "$tmpdir"
    return 1
  fi

  setopt localoptions null_glob
  local files=( "$tmpdir"/*.fits "$tmpdir"/*.FITS "$tmpdir"/*.fit "$tmpdir"/*.FIT "$tmpdir"/*.evt "$tmpdir"/*.EVT )

  if (( ${#files} == 0 )); then
    echo "❌ No .fits/.evt files found after copy."
    rm -rf "$tmpdir"
    return 1
  fi

  echo "Launching DS9 (working dir: $tmpdir) ..."
  ( cd "$tmpdir" && /Applications/SAOImageDS9.app/Contents/MacOS/ds9 "${files[@]}" ) &
  local pid=$!
  wait $pid 2>/dev/null

  # Upload any .reg region files saved during the session
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
```

Save (`Ctrl + O`, `Enter`, `Ctrl + X`) and reload:

```bash
source ~/.zshrc
```

---

## 4) Test the Command

To open a FITS or EVT file from the cluster in your local DS9:

```bash
ds9ssh interactive:/ceph/users/fcandon/NuSTAR_SUN/NuSTAR_SUN_MOSAIC_Feb_2020/80514219001/event_cl/nu80514219001A06_cl.evt
```

### What Happens

1. The remote file is copied to `/tmp/ds9_XXXX`
2. DS9 opens locally on your Mac (fast, GUI-based, no X forwarding required)
3. You can create and save region files normally
4. When you close DS9:

   * All `.reg` files are uploaded to the same cluster directory as the event file
   * The temporary folder is deleted automatically

---

## 5) Verify Region Upload

After closing DS9, check the cluster to confirm your region file was saved:

```bash
ssh interactive
ls /ceph/users/fcandon/.../event_cl/*.reg
```

You should see your `.reg` file in the same directory as the EVT file you opened.

---

## 6) Cleanup

All temporary data is stored under `/tmp/ds9_XXXX` on your Mac.
These folders are automatically deleted once DS9 is closed.

---

## 7) Example Workflow

```bash
# Copy and open a remote event file
ds9ssh interactive:/home/fcandon/test_evt.evt

# Inside DS9:
#   → Create region(s)
#   → Save Regions (default folder)
#   → Close DS9
# The region file is uploaded automatically to the cluster.
```

---

## 8) Optional: Password-Free Access with SSH Keys

To avoid typing your password every time:

```bash
ssh-keygen -t ed25519 -C "fcandon@macbook"
ssh-copy-id fcandon@interactive.e5.physik.tu-dortmund.de
```

Now `ds9ssh` will run without prompting for credentials.

---

## Key Reminders

* DS9 runs **locally** — no X11 or remote desktop required.
* The `ds9ssh` command handles file transfer and cleanup automatically.
* Always save DS9 regions in **WCS (FK5)** coordinates for correct alignment.
* Use consistent region names (e.g., `srcA.reg`, `bgdA.reg`) for your analysis scripts.

---

### ✅ Summary

| Task                      | Command                              |
| ------------------------- | ------------------------------------ |
| Open DS9 locally          | `open -a SAOImageDS9`                |
| Open remote FITS/EVT file | `ds9ssh user@host:/path/to/file.evt` |
| Save region in DS9        | *Region → Save Regions…*             |
| Region upload             | Automatic on DS9 close               |
| Temp cleanup              | Automatic on DS9 close               |
| Passwordless access       | `ssh-keygen` + `ssh-copy-id`         |

---
