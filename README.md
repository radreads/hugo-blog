################################################################################
# 0. Prereqs (run once on your machine) ---------------------------------------
################################################################################
brew install hugo               # or winget / choco / scoop on Windows
git --version || brew install git
# log in to GitHub and create an empty repo, e.g.   khehy/blog

################################################################################
# 1. Create the Hugo site skeleton --------------------------------------------
################################################################################
hugo new site khehy-blog && cd khehy-blog
git init
mkdir -p static/css static/images content/blog \ 
         layouts/_default layouts/contact .github/workflows

################################################################################
# 2. config.toml ---------------------------------------------------------------
################################################################################
cat > config.toml <<'EOF'
baseURL   = "https://YOUR_GH_USERNAME.github.io/REPO_NAME/"
languageCode = "en-us"
title     = "Meet Khe Hy"
theme     = ""                 # we’re rolling our own minimal theme
paginate  = 7
enableRobotsTXT = true

[params]
  description = "Entrepreneur, Coach & Writer"
EOF

################################################################################
# 3. CSS that evokes Stripe.com (static/css/main.css) --------------------------
################################################################################
cat > static/css/main.css <<'EOF'
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
:root{--primary:#0a2540;--accent:#635bff;--bg:#f6f9fc}*{box-sizing:border-box}
body{margin:0;font-family:'Inter',system-ui,-apple-system,'Segoe UI',Roboto;background:var(--bg);color:var(--primary);line-height:1.6}
header{background:#fff;padding:1rem 2rem;display:flex;justify-content:space-between;align-items:center;box-shadow:0 1px 3px rgba(0,0,0,0.05)}
nav a{margin-left:1rem;text-decoration:none;color:var(--primary);font-weight:600}
nav a:hover{color:var(--accent)}
.hero{background:linear-gradient(135deg,#a1c4fd 0%,#c2e9fb 100%);padding:100px 20px;text-align:center;color:var(--primary)}
.container{max-width:840px;margin:0 auto;padding:2rem 1rem}
footer{text-align:center;padding:2rem 0;font-size:.875rem;background:#fff;margin-top:2rem}
EOF

################################################################################
# 4. Base & list/single templates (layouts/_default) --------------------------
################################################################################
cat > layouts/_default/baseof.html <<'EOF'
<!DOCTYPE html><html lang="en"><head>
  <meta charset="utf-8"><title>{{ .Title }} | {{ .Site.Title }}</title>
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <link rel="stylesheet" href="/css/main.css">
  {{ if .IsHome }}<meta name="description" content="{{ .Site.Params.description }}">{{ end }}
</head><body>
<header>
  <a href="{{ "/" | relURL }}">{{ .Site.Title }}</a>
  <nav>
    <a href="{{ "/" | relURL }}">Home</a>
    <a href="{{ "/about/" | relURL }}">About</a>
    <a href="{{ "/blog/" | relURL }}">Blog</a>
    <a href="{{ "/contact/" | relURL }}">Contact</a>
  </nav>
</header>
<main>{{ block "main" . }}{{ end }}</main>
<footer>© {{ now.Year }} {{ .Site.Title }} • Built with <a href="https://gohugo.io/">Hugo</a></footer>
</body></html>
EOF

cat > layouts/_default/index.html <<'EOF'
{{ define "main" }}
<section class="hero">
  <h1>Welcome to {{ .Site.Title }}</h1>
  <p>Thoughts on entrepreneurship, purpose & balance.</p>
  <img src="/images/home-hero.jpg" alt="Ocean" style="margin-top:2rem;max-width:100%;border-radius:12px">
</section>
<div class="container">{{ .Content }}</div>
{{ end }}
EOF

cat > layouts/_default/list.html <<'EOF'
{{ define "main" }}
<div class="container">
  <h1>{{ .Title }}</h1>
  {{ range .Pages }}
    <article style="margin-bottom:2rem">
      <h2><a href="{{ .RelPermalink }}">{{ .Title }}</a></h2>
      <p><em>{{ .Date.Format "January 2, 2006" }}</em></p>
      <p>{{ .Summary }}</p>
    </article>
  {{ end }}
</div>
{{ end }}
EOF

cat > layouts/_default/single.html <<'EOF'
{{ define "main" }}
<div class="container">
  <h1>{{ .Title }}</h1>
  <p><em>{{ .Date.Format "January 2, 2006" }}</em></p>
  {{ .Content }}
</div>
{{ end }}
EOF

################################################################################
# 5. Dedicated contact template (layouts/contact/single.html) -----------------
################################################################################
mkdir -p layouts/contact
cat > layouts/contact/single.html <<'EOF'
{{ define "main" }}
<section class="hero">
  <h1>Get in Touch</h1>
  <img src="/images/contact-hero.jpg" alt="Laptop & coffee" style="margin-top:2rem;max-width:100%;border-radius:12px">
</section>
<div class="container">
  <p>Email me at <a href="mailto:hello@radreads.co">hello@radreads.co</a>.</p>
  <h2>Find me on the web</h2>
  <ul>
    <li><a href="https://www.linkedin.com/in/khehy/"      target="_blank">LinkedIn</a></li>
    <li><a href="https://twitter.com/khemaridh"            target="_blank">X / Twitter</a></li>
    <li><a href="http://instagram.com/radreadsco"          target="_blank">Instagram</a></li>
    <li><a href="http://bit.ly/RRYouTube_"                 target="_blank">YouTube</a></li>
    <li><a href="https://www.tiktok.com/@radreadsco"       target="_blank">TikTok</a></li>
  </ul>
</div>
{{ end }}
EOF

################################################################################
# 6. Content pages -------------------------------------------------------------
################################################################################
# Home uses the index template — leave body blank for now
cat > content/_index.md      <<'EOF'
---
title: "Home"
---
EOF

cat > content/about.md       <<'EOF'
---
title: "About Me"
---

<img src="/images/about-hero.jpg" alt="Desk" style="width:100%;border-radius:12px">

## Hey — I’m Khe

I’m an ex-Wall Street trader turned entrepreneur who helps high-achievers
find purpose, balance, and joy without sacrificing excellence.  
RadReads is my home on the internet where I explore money, meaning, and mindset.
EOF

cat > content/contact.md     <<'EOF'
---
title:   "Contact"
layout:  "contact"
---
EOF

# Blog list stub so /blog/ has a hero-less list page
cat > content/blog/_index.md <<'EOF'
---
title: "Blog"
---
EOF

################################################################################
# 7. Three 500-word lorem-ipsum posts (content/blog/) --------------------------
################################################################################
POST_DATE=$(date +%Y-%m-%d)

for i in 1 2 3; do
cat > content/blog/post-$i.md <<EOF
---
title: "Lorem Post $i"
date: $POST_DATE
---

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed non risus. Suspendisse
lectus tortor, dignissim sit amet, adipiscing nec, ultricies sed, dolor. Cras elementum
ultricies diam. Maecenas ligula massa, varius a, semper congue, euismod non, mi. Proin
porttitor, orci nec nonummy molestie, enim est eleifend mi, non fermentum diam nisl sit
amet erat. Duis semper. Duis arcu massa, scelerisque vitae, consequat in, pretium a,
enim. Pellentesque congue. Ut in risus volutpat libero pharetra tempor. Cras vestibulum
bibendum augue. Praesent egestas leo in pede. Praesent blandit odio eu enim. Pellentesque
sed dui ut augue blandit sodales. Vestibulum ante ipsum primis in faucibus orci luctus et
ultrices posuere cubilia Curae; Aliquam nibh. Mauris ac mauris sed pede pellentesque
fermentum. Maecenas adipiscing ante non diam sodales hendrerit.

Sed a libero. Sed quis quam. Suspendisse sem magna, pharetra quis, malesuada sed, gravida
vel, velit. Morbi sollicitudin justo eget lacus. Etiam pulvinar, arcu et cursus commodo,
risus enim ornare nibh, quis laoreet nibh augue ut velit. Curabitur commodo vulputate
erat. Nam feugiat. Nullam viverra fermentum leo. Marin fermentum nulla non arcu dapibus
fringilla. Donec id sapien eu lectus tempor ultricies. Donec mollis eros dolor. Nulla
vulputate urna at magna. Ut tempus luctus est.

Integer in libero at nibh pretium pretium. Maecenas in erat. Pellentesque habitant morbi
tristique senectus et netus et malesuada fames ac turpis egestas. Fusce consectetuer
risus a nunc. Integer id quam. Morbi mi. Quisque nisl felis, venenatis tristique,
dignissim in, ultrices sit amet, augue. Proin sodales libero eget ante. Nulla quam. Aenean
laoreet. Vestibulum nisi lectus, commodo ac, facilisis ac, ultricies eu, pede. Ut orci
risus, accumsan porttitor, cursus quis, aliquet eget, justo.

Aliquam erat volutpat. Nam dui mi, tincidunt quis, accumsan porttitor, facilisis luctus,
metus. Phasellus ultrices nulla quis nibh. Quisque a lectus. Donec consectetuer ligula
vulputate sem tristique cursus. Nam nulla quam, gravida non, commodo a, sodales sit amet,
nisi. Pellentesque fermentum dolor. Aliquam quam lectus, facilisis auctor, ultrices ut,
elementum vulputate, nunc.

Praesent a nulla at nibh mattis pretium. Cras posuere libero vitae risus. Nullam at odio
velit. Sed consequat, leo eget bibendum sodales, augue velit cursus nunc, quis gravida
magna mi a libero. Fusce suscipit varius mi. Cum sociis natoque penatibus et magnis dis
parturient montes, nascetur ridiculus mus.
EOF
done

################################################################################
# 8. Unsplash hero images ------------------------------------------------------
################################################################################
curl -L https://images.unsplash.com/photo-1507525428034-b723cf961d3e?auto=format&fit=crop&w=1350&q=80 -o static/images/home-hero.jpg
curl -L https://images.unsplash.com/photo-1515378791036-0648a3ef77b2?auto=format&fit=crop&w=1350&q=80 -o static/images/about-hero.jpg
curl -L https://images.unsplash.com/photo-1521791136064-7986c2920216?auto=format&fit=crop&w=1350&q=80 -o static/images/contact-hero.jpg

################################################################################
# 9. Local preview -------------------------------------------------------------
################################################################################
hugo server -D        # visit http://localhost:1313

################################################################################
# 10. GitHub Pages workflow ----------------------------------------------------
################################################################################
cat > .github/workflows/hugo.yml <<'EOF'
name: Deploy to GitHub Pages
on:
  push:
    branches: [ main ]
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: peaceiris/actions-hugo@v2      # install Hugo 🚀
      with:
        hugo-version: '0.127.0'
        extended: true
    - run: hugo --minify                   # build to ./public
    - uses: peaceiris/actions-gh-pages@v3  # push to gh-pages
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./public
EOF

################################################################################
# 11. Commit & push ------------------------------------------------------------
################################################################################
git add .
git commit -m "Initial Hugo blog"
git branch -M main
git remote add origin https://github.com/YOUR_GH_USERNAME/REPO_NAME.git
git push -u origin main

# GitHub Actions will run; after it finishes, enable Pages
# Settings ➜ Pages ➜ Source: Deploy from gh-pages branch.

echo "🎉  Done! Your site will be live at:"
echo "    https://YOUR_GH_USERNAME.github.io/REPO_NAME/"
