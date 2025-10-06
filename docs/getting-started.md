# Getting started

!!! info "You’ll need"
    - **Access to the cluster** granted by one of the admins (ask @francandon).
    - **If you are working locally** on your own computer, you must install each telescope environment separately. More information can be found in each telescope’s section.
    - **To modify or add content** to this documentation site, you’ll need:
        - Conda (Miniforge or Mambaforge)
        - Python 3.11
        - JupyterLab *(optional, for running notebooks locally)*


```bash
conda env create -f environment.yml
conda activate docs
mkdocs serve   # To preview your changes locally
```


---

## Accessing the Cluster

Once your account has been created, you can log in with:

```bash
ssh interactive
```

You’ll then be inside the cluster, in your home directory (`/home/user`).
Activate the **XTuDo** environment by running:

```bash
conda activate XTuDo
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