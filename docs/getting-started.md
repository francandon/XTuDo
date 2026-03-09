# Getting started

!!! info "You’ll need"
    - **Access to the cluster** granted by one of the admins (ask @francandon).
    - **If you are working locally** on your own computer, you must install each telescope environment separately. More information can be found in each telescope’s section.
---

## Accessing the Cluster

Once your account has been created, you can log in with:

```bash
ssh interactive
```

You’ll then be inside the cluster, in your home directory (`/home/user`). Now, you should modify the file .bashrc to initialize conda and the NuSTAR Caldb every time you enter the cluster. Copy and paste this into your .bashrc: 

```bash
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/opt/miniconda/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/opt/miniconda/etc/profile.d/conda.sh" ]; then
        . "/opt/miniconda/etc/profile.d/conda.sh"
    else
        export PATH="/opt/miniconda/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<

# --- NuSTAR CALDB setup ---
export CALDB=/ceph/users/fcandon/caldb
export CALDBCONFIG=$CALDB/software/tools/caldb.config
export CALDBALIAS=$CALDB/software/tools/alias_config.fits

# Initialize CALDB helpers (sets CALDBPATH etc.) if available
if [ -f "$CALDB/software/tools/caldbinit.sh" ]; then
  . "$CALDB/software/tools/caldbinit.sh"
fi
```
Now, close the terminal and open it again.

Activate the NuSTAR enviroment **henv_forge** by running:

```bash
conda activate henv_forge
```

Now you should be able to run all the commands discussed later in the tutorials.

---

## Editing and Previewing the Documentation

If you’re contributing to this documentation locally:

```bash
conda env create -f environment.yml
conda activate docs
mkdocs serve
```

Then open the link shown in your terminal (usually `http://127.0.0.1:8000/`) to view the site.

> 💡 **Tip:**
> If you see an “Address already in use” error, run MkDocs on another port:
>
> ```bash
> mkdocs serve -a 127.0.0.1:8010
> ```

---

## Contributing

* All documentation files are stored inside the `docs/` folder.
* You can edit existing `.md` files or add new Markdown or Jupyter Notebook files (`.ipynb`).
* If you’re developing Python modules that are imported in tutorials, install them in editable mode:

  ```bash
  pip install -e .
  ```

When you finish, commit and push your changes — GitHub Pages will rebuild and update the site automatically (if configured).

---