# **GusmaoLab**

Host Code of Eduardo G Gusmao's Lab Website Portfolio and Blogging

---
---

## **Building this Website:**

### Pelican Setup

- **Step 1**: Inside Your web Micromamba Environment and Install Pelican

Let's begin with the clean and correct Pelican setup inside your web micromamba environment.

___

_A. Activate Your Micromamba Web Env_

In your terminal:

```bash
micromamba create -n web python=3.12
micromamba install -c conda-forge XXXX XXXX XXXX XXXX XXXX XXXX
micromamba activate web
```

___

_B. Create a Project Folder (Optional but Recommended)_

Wherever you want your Pelican project to live (e.g., ~/Projects/gusmaolab_site):

```bash
mkdir -p ~/Projects/gusmaolab
cd ~/Projects/gusmaolab
```

If you prefer to work inside the eggduzao.github.io repo directly, that’s also fine — but I recommend keeping Pelican output and GitHub Pages cleanly separated (we’ll connect them later via GitHub actions).

___

_C. Install Pelican and Markdown_

While inside the activated web environment:

```bash
pip install pelican markdown
```

You can also pin versions later (pelican==4.9.1 is the current stable as of mid-2025).

---

- **Step 2**: Start Pelican

_A. Start Pelican Quickstart Wizard_

Now run:

```bash
pelican-quickstart
```

You’ll be asked a few questions. Here are some examples of recommended answers:

```
Where do you want to create your new web site? [.]
> .

What will be the title of this web site?
> Gusmao Lab

Who will be the author of this web site?
> Eduardo Gade Gusmao

What will be the default language of this web site? [en]
> en

Do you want to specify a URL prefix? (e.g., https://example.com) [Y/n]
> Y

What is your URL prefix?
> https://www.gusmaolab.org

Do you want to enable article pagination? [Y/n]
> Y

How many articles per page? [10]
> 10

> What is your time zone? [Europe/Rome]
> America/Bahia

> Do you want to generate a tasks.py/Makefile to automate generation and publishing? (Y/n)
> Y

> Do you want to generate a tasks.py/Makefile to automate generation and publishing? (Y/n)
> Y

> Do you want to upload your website using FTP? (y/N)
> N

> Do you want to upload your website using SSH? (y/N)
> N

> Do you want to upload your website using Dropbox? (y/N)
> N

> Do you want to upload your website using S3? (y/N)
> N

> Do you want to upload your website using Rackspace Cloud Files? (y/N)
> N

> Do you want to upload your website using GitHub Pages? (y/N)
> Y

> Is this your personal page (username.github.io)? (y/N)
> Y
```

___

_B. Folder Structure_

This creates a minimal Pelican structure, like:

```
├── content/         # where your blog posts go
├── output/          # where your HTML site will be built
├── pelicanconf.py   # your main config
├── publishconf.py   # optional production config
├── Makefile         # to build & publish
└── tasks.py 		 # ?
```

---

- **Step 3**: First Test Build_

_A. Making HTML Content_

Still inside that folder, make all HTML content:

```bash
make html
```

___

_B. Testing_

Then test locally:

```bash
make serve
```

Open http://localhost:8000 and admire your newborn Pelican site.

---
---

### Clean Python Tooling

**Goal:** Make your gusmaolab project reproducible, shareable, and robust

We’ll do this in a professional, version-controlled way.

---

**Step 1**: Save Your Environment

Since you’re using micromamba (smart move), run this:

```bash
micromamba list --explicit > environment.txt
```

This saves the exact package versions in your web env.

You can also create a more readable environment.yml later if needed.

---

**Step 2**: Generate Requirements File

Inside your gusmaolab project root:

```bash
pip freeze > requirements.txt
```

You’ll get lines like:

```
blinker==1.6.2
markdown==3.5.2
pelican==4.9.1
```

> [!TIP]
> You can later use pipreqs . --force to generate a minimal requirements.txt based only on actual imports (if you want to keep it clean).

---

**Step 3**: Add .gitignore

Create a file called .gitignore in the project root and add this:

```
__pycache__/
output/
cache/
*.pyc
*.pyo
*.pyd
*.egg-info/
*.log
env/
.env/
venv/
.micromamba/
```

If you’re inside a Git repo already, this will prevent unwanted junk from being tracked.

---

**Step 4**: Add Python Version Pin (Optional but Recommended)

Create a file:

```bash
echo "3.12" > .python-version
```

This helps tools like pyenv, VSCode, and GitHub Actions stick to the correct interpreter version.

---

**Step 5** (Optional): Poetry

If you’d prefer to manage dependencies with poetry, you could later do:

```bash
pip install poetry
poetry init
```

But since you’re using micromamba, it’s fine to stay with .txt and environment.txt for now.

---

**Step 6** (Optional):  Add a README.md

You already have one in your GitHub repo — but here in gusmaolab, add a simple one too:

"""""""""""""""""""""""""""""""""""""""""""""""""""""""

# GusmaoLab Website (Pelican)

This is the source folder for https://www.gusmaolab.org

To build the site:

````
```bash
make html
make serve
```
````

To deploy to GitHub Pages:

````
```bash
make github
```
````
"""""""""""""""""""""""""""""""""""""""""""""""""""""""

With this we are keeping it tight, clean, and fully reproducible. Professorial-grade engineering.

Project Snapshot so far:

```
gusmaolab/
├── content/            ✅
├── output/             ✅
├── pelicanconf.py      ✅
├── publishconf.py      ✅
├── Makefile            ✅
├── tasks.py            ✅
├── .gitignore          ✅
├── requirements.txt    ✅
├── environment.txt     ✅
├── .python-version     ✅
└── README.md           ✅
```

---

### Pelican Blog + Portfolio Structure

Time to shape your digital lab into a professorial site with blog + portfolio structure, simple but elegant — like a handwritten LaTeX equation on a napkin.

**Goal:** Build the following sections:

|     Section   |   Type   | Folder                      | Purpose                                            |
| ------------- | -------- | --------------------------- | -------------------------------------------------- |
| 🧬 Blog       | Articles | content/blog/               | Posts from categories 1.A to 1.D                   |
| 🧾 About      |   Page   | content/pages/about.md      | Your academic + personal summary                   |
| 🧰 Projects   |   Page   | content/pages/projects.md   | Descriptions & links to tools you’ve made          |
| ✍️ Writings   |   Page   | content/pages/writing.md    | Excerpts from Mikrokosmos or safe fiction          |
| 🧠 Philosophy |   Page   | content/pages/philosophy.md | Optional — Marxism, Freire, open science manifesto |
| 🪪 CV         |  Static  | content/extra/CV.pdf        | Optional link to your CV (later)                   |

---

**Step 1**: Create Folder Structure

Inside your project root:

```bash
mkdir -p content/blog
mkdir -p content/pages
mkdir -p content/extra
```

---

**Step 2**: Configure pelicanconf.py

Open pelicanconf.py and set/add these lines:

```python
# Content organization
PATH = "content"
ARTICLE_PATHS = ["blog"]
PAGE_PATHS = ["pages"]
STATIC_PATHS = ["extra/CV.pdf"]

# Site structure
PAGE_URL = '{slug}/'
PAGE_SAVE_AS = '{slug}/index.html'
ARTICLE_URL = 'blog/{slug}/'
ARTICLE_SAVE_AS = 'blog/{slug}/index.html'

# Metadata
TIMEZONE = 'America/Recife'
DEFAULT_LANG = 'en'
DEFAULT_DATE_FORMAT = '%d %B %Y'

# Feed generation (disabled for dev)
FEED_ALL_ATOM = None
CATEGORY_FEED_ATOM = None

# Default pagination
DEFAULT_PAGINATION = 10

# Blogroll / Links (optional)
LINKS = (
    ('LinkedIn', 'https://www.linkedin.com/in/gusmao/'),
    ('GitHub', 'https://github.com/eggduzao'),
)

# Social widget (optional)
SOCIAL = (
    ('Email', 'mailto:contact@gusmaolab.org'),
    ('RSS', '/feeds/all.atom.xml'),
)
```

We’ll later tweak this depending on theme and features.

---

**Step 3**: Add Your First Blog Post (placeholder)

Create your first blog post:

```bash
touch content/blog/2025-08-02-first-post.md
```

Add some placeholder content to it:

```
Title: GusmaoLab Is Born
Date: 2025-08-02
Category: Meta
Tags: pelican, welcome
Slug: first-post
Author: Eduardo Gusmao

Welcome to the official GusmaoLab website.

This blog will cover bioinformatics, AI, personal projects, literary experiments, and occasional mathematical puns.

Stay tuned.
```

With later refinements this can be your first post.

---

**Step 4**: Add About Page (placeholder)

Include an "about" section by creating a markdown:

```bash
touch content/pages/about.md
```

Then, create the following:

```
Title: About

Eduardo Gade Gusmao is a Brazilian bioinformatician, professor, and writer.  

This site hosts his lab notes, blog posts, creative experiments, and tools for science, pedagogy, and beyond.

You can reach him at contact@gusmaolab.org.
```

---

**Step 5**: Add Projects Page (placeholder)

Kick-Start your project page:

```bash
touch content/pages/projects.md
```

With a placeholder like:

```
Title: Projects

### Bloom  
A deep-learning framework for scHi-C chromatin architecture discovery.

### Stainalyzer  
A lightweight, open-source pipeline for histopathological image segmentation and staining quantification.

### TryDInn  
A three-tiered architecture for intelligent missing data imputation in EHRs and omics.

[More coming soon...]
```

---

**Step 6**: Rebuild and Preview

Type the following commands to make all new html:

```bash
make html
make serve
```

And then, open your "local" website at: http://localhost:8000 (it will say where after the serve command).

And then check your:
- Blog at /blog/first-post/
- About at /about/
- Projects at /projects/

> [!NOTE]
> Do not forget to kill the job later!

---

### GitHub the Project and Activate Flex Theme

Time to shape your digital lab into a professorial site with blog + portfolio structure, simple but elegant — like a handwritten LaTeX equation on a napkin.

**Goal:** Set the GitHub Foundations and choose a better theme and beautify the website.

**Step 1**: Put the project in GitHub

_A. Initialize Your Project as a Git Repo_

Go to your project folder:

```bash
cd ~/Projects/gusmaolab
```

Then initialize Git:

```bash
git init
git status
```

Make sure every "trash/eraseable file" types are in `.gitingore`. If you are on MAC OS X (like me), you might want to add the following (or create a `./clean.sh` to remove these files):

```bash
echo -e ".DS_Store\n.Trashes\n.AppleDouble\n.LSOverride\n._*" >> .gitignore
```

And, create a first commit:

```bash
git add .
git commit -m "Initial commit: Eduardo’s beaututiful site begins"
```

___

_B. Push to GitHub_

Since we want to have GitHub Pages auto-deploy later, let's create a repo called gusmaolab on GitHub.

Then:

```bash
git remote add origin git@github.com:<yourusername>/gusmaolab.git
git branch -M main
git push -u origin main
```

And replace <yourusername> with your GitHub UserName handle.

___

**Step 2**: Install the Flex theme

Inside your Pelican project root:

```bash
git submodule add https://github.com/alexandrevicenzi/Flex themes/Flex
```

Or if themes/ already exists but empty:

```bash
git clone https://github.com/alexandrevicenzi/Flex.git themes/Flex
```

---

**Step 3**: Update pelicanconf.py

Inside the `pelicanconf.py` file. Set the theme:

```
THEME = 'themes/Flex'
```

And add/override some elegant Flex settings:

```
# Basic Identity
SITENAME = 'Eduardo Gusmão'
SITESUBTITLE = 'Computational Biology • AI • Writing'
SITEURL = ''

# Bio / Sidebar
AUTHOR = 'Eduardo Gusmão'
TAGLINE = 'Bioinformatician, Marxist, and Microscopic Dreamer'
SITELOGO = '/images/avatar.png'  # optional
FAVICON = '/images/favicon.ico'  # optional

# Social links (use your links or placeholders)
SOCIAL = (
    ('GitHub', 'https://github.com/your-username'),
    ('Twitter', 'https://twitter.com/your-handle'),
    ('ORCID', 'https://orcid.org/0000-0000-0000-0000'),
)

# Menu items
MAIN_MENU = True
MENUITEMS = (
    ('About', '/about.html'),
    ('Projects', '/projects.html'),
    ('Writings', '/writing.html'),
    ('Blog', '/blog.html'),
)

# Enable Flex features
USE_FOLDER_AS_CATEGORY = True
SHOW_ARTICLE_AUTHOR = True
SHOW_ARTICLE_CATEGORY = True
SHOW_ARTICLE_TAGS = True
SHOW_DATE_MODIFIED = True

# Pagination
DEFAULT_PAGINATION = 10

# Static paths
STATIC_PATHS = ['images', 'extra/CV.pdf']
EXTRA_PATH_METADATA = {
    'extra/CV.pdf': {'path': 'CV.pdf'},
}

# Flex-specific options
DISQUS_SITENAME = ''
GOOGLE_ANALYTICS = ''
```

---

**Step 4**: Build and preview

Then repeat the following commands to make all new html:

```bash
make html
make serve
```

And then, check the new website at your "local" server at: http://localhost:8000.

___

_Optional Extra-Glam_

✨ Optional: Add some glam assets

Put your avatar (avatar.png) and favicon (favicon.ico) in content/images/.

---





⸻

Once you’re happy with the look, we’ll move to Step 5: GitHub auto-deploy via GitHub Pages using a GitHub Action that builds the site on every commit. Let me know when you’re ready.

You’re making a site so glam it should wear heels. 👠



