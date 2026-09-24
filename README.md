# Greenfratech - standalone static site

A self-contained copy of the Greenfratech website. No Odoo, no Python, no
database. Every page is plain HTML with one CSS file and one JS file.

## Deploying

Upload the whole folder to your web root. That is the entire process.

    nginx / Apache / cPanel   upload as-is; index.html resolution is default
    Netlify / Cloudflare      drag the folder in, or point at the repo
    Local preview             python3 -m http.server 8000

**It must be served over HTTP, not opened as a file.** The pages reference
assets from the site root (`/assets/...`), so `file://` will not find them.

**It must be served from a domain root.** If you need it in a subfolder,
find-and-replace `"/assets/` with `"assets/` and fix the page links, or set the
subfolder as the document root instead.

## What is here

    index.html                 homepage, with the scroll-pinned 3D stage
    water-treatment/           \
    bamboo-geotextile/          |
    fiber-mastic-asphalt/       |- the five solution pages
    green-cleaning/             |
    green-lighting/            /
    about-us/
    contactus/
    assets/css/site.css        all styling
    assets/js/site.js          nav + scroll animations
    assets/fonts/              FontAwesome
    assets/img/  assets/video/ photography and the two product videos

URLs are unchanged from the Odoo site, so existing links still resolve.

## What changed in the move

**The contact form is gone.** It posted to Odoo's form handler, which no longer
exists. `/contactus` now leads with a mailto button to support@aceme.io and the
phone number. If you want a working form, sign up for a form service
(Formspree, Basin, Getform, or Netlify Forms if you host there) and put its
endpoint in a `<form action="...">` on that page.

**The site search is gone.** It queried the database.

**There is no page editor.** Content is edited by hand in these HTML files from
now on. They are formatted and readable, and the class names are documented in
the theme's SCSS if you need to restyle anything.

## Editing

Text and images: edit the HTML directly. Images live in `assets/img/`; replacing
a file keeps every reference to it working.

Styling: `assets/css/site.css` is compiled output, so it is minified and awkward
to edit. The site's own rules are all prefixed `gft-` and can be overridden by
adding your own stylesheet AFTER it in each page's `<head>`.

Behaviour: `assets/js/site.js` is readable. The top third is a small
compatibility shim; below it is the theme's scroll engine, comment for comment
as it was written.

## Regenerating

This folder was produced by `tools/export_static.py` in the theme module,
against a running Odoo. If the Odoo site is still the source of truth, re-run
that script after content changes rather than editing here:

    python3 tools/export_static.py --base-url https://your-odoo --out ./site

If this static copy is now the source of truth, ignore the script and edit
these files.

## Performance notes

`site.css` is the full compiled bundle, which includes a lot of Odoo styling
these pages never use. It gzips to roughly 90KB, so it is not a problem, but if
you want it smaller run purgecss against the built HTML:

    npx purgecss --css assets/css/site.css --content '**/*.html' \
        --output assets/css/

Check the homepage stage and the mobile nav afterwards; those depend on classes
that are added by JavaScript at runtime and are the likeliest thing a purge
would strip by mistake.
