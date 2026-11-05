# Cairn site redesign: prototype notes

`prototype.html` is a single-file layout proposal for cairnrpg.com. It works offline, except the 3D dice, which load from jsDelivr the first time the dice roller is used, and the adventure covers and module images, which are hotlinked from itch.io and cairnrpg.com (offline, each card shows a panel with the adventure's name instead). It uses the site's real content from [yochaigal/cairn](https://github.com/yochaigal/cairn): © Yochai Gal, CC-BY-SA 4.0. Open it in a browser. The IA notes overlay (a Show IA notes button with 20 numbered notes and a source tag on each piece of copy) was removed on 2026-10-30; the decisions it explained are written up in this file.

To rebuild from the live repo:

```bash
python3 -I build_prototype.py --refresh
```

The build sparse-clones only the Markdown files, the site logo (`img/logo.svg`) and 5 Alegreya fonts (Regular, Medium, Bold, Italic, Bold Italic) into `.cairn-src/` (about 5 MB). The logo is rebuilt from its shapes only, drawn in the heading colour, with its viewBox cropped to the artwork (`LOGO_VIEWBOX`; its canvas has wide empty margins). If the logo's shapes change, the build stops and asks for the crop and the header's offset to be measured again. It fails if any expected count changes: 145 monsters; 124 adventure pages in the repo (5 first-party, 57 originals, 62 conversions); 6 Player's Guide, 23 Warden's Guide and 20 Backgrounds chapters.

The Core Rules translations are downloaded into `cache/l10n/` by `--refresh`, or by `--translations`, which doesn't re-clone. A plain build only reads that cache. `--src DIR`, `--cache DIR` and `--out FILE` point the checkout, cache and page elsewhere, for tests. `--help` lists every option.

**Theme:** dark only. The light theme and its header switch were removed on 2026-10-29.

## Hero art
The home hero's background is the cover of the [Cairn Player's Guide](https://cairnpress.itch.io/cairn-players-guide) (Cairn Press). It was taken from that page's share image and cropped to its top 68%, so the cover's own logo and title don't repeat the hero logo. It settles in with a slight Ken Burns zoom: it starts 6% larger and eases over 14s to the resting frame, around the figures in the tree. Only the art moves, not the scrim or text, and it stays still for anyone with reduced motion turned on. It's saved as `assets/players-guide-cover.jpg` (1920×992, 166 KB) and inlined by the build. Its licence is under Licensing to confirm.

**Legibility:** on tablets and desktops the scrim is a soft-edged curved shape (a blurred SVG path) that hugs the text: narrow beside the logo, out past the paragraph and buttons, then sweeping right along the bottom edge. The figures in the tree and the monster sit outside it, at full strength, and the art gets a slight saturation boost (`saturate(1.2)`). The shape is drawn relative to the text column's left edge, so it follows the text at any width. Phones keep a full-width scrim, because the text sits over the art there. Measured, the paragraph's worst spot is 7.5–8.0:1 at 1024–2560px, 6.3:1 at 800px and 6:1 on a phone.

## Start step art
"Start playing in three steps" is a **full-bleed slideshow**: the Player's Guide section-opener silhouettes fill the whole section, one step at a time, with each step's text at the bottom. The section heading ("Start playing in three steps") isn't shown, so nothing covers the top of the art; it stays in the page for screen readers and as the target of the hero's "Start here" link. The art is: Section 4 Procedures (p.68–69) for Overview & Principles, which has no opener of its own; Section 1 Character Creation (p.6–7); and Section 3 Rules (p.60–61), in the spreads PDF.
- **Joins:** checked against the PDF's images, the art doesn't continue from one image to the next pixel for pixel (edge differences of 57–115 on a 0–255 scale, against 12 between neighbouring columns inside one image). But the outer 10–15% of every image is about 80% black forest. So each step overlaps the one before by 48px with a soft fade, and as the show slides the forest reads as one wood.
- **Size:** the full window width, cut to a band of the art (1600:520), each slide showing the part of its art that holds its moving figures: Overview from the top (the bird and bow arm), Core Rules from 50 units down (both swords, with the top edge trimmed), Character Creation from a third of the way down (the rider's head clear of the top edge, the horse still above the text's shade). From 760px up the band is a short strip; on a phone it's taller than the art's shape, so all of the art shows and Character Creation uses it whole (`fitScenes` swaps each scene's viewBox). 463px tall at 1440px, 624px at 1920px. It's never taller than 60% of the window, nor than the window below the header, and never shorter than 340px. Phones use 400px with a centre crop. The home divider is on the hero's bottom edge, above Adventures.
- **Moving through it:** round ‹ › buttons sit on the left and right edges, centred on the art, and wrap around (› on step 3 goes back to step 1). Swipe, trackpad and the keyboard (when the slideshow has focus) also move it, and it snaps step by step.
- **Auto-advance:** each step waits for its animation first: 7s of motion, then 7s more, so 14s a step (measured 14.3s and 15.1s on steps 1 and 2, including the slide). It holds while the pointer is over it or anything in it has focus, while it's less than half on screen, and while the tab is hidden. It never runs for anyone who asks for reduced motion. A **Pause / Play** button sits bottom right (moving content needs one to stop it).
- **Motion:** each step's animation is slow and eases out, ending exactly on the book's drawing. They play when the step first comes into view and each time the slideshow returns to it. Until then the figures wait in their start pose, so the end state never shows first. A step goes back to its start pose once the show has moved off it. With reduced motion, both are the still drawing.
  - **Step 1:** the eagle swoops in from the upper right, down toward the archer (7s). The archer's bow arm, with the bow, strings and arrow, rises 11° into place (6s). It turns about the drawing hand, where the strings and arrow meet, so they stay joined.
  - **Step 2:** the rider and horse gallop in from the left along the path (6s, from 264px left), with a stride bounce that settles as they arrive.
  - **Step 3:** the two swords swing into the clash. Hers, with her hand on the grip, swings down and to the right into place, turning 12° about her grip (6s). His, with his fist and the pommel, rises 7° into place about his grip, half a second behind hers.
- **Backlit linen:** all three steps are shown like a shadow play, matched to a reference image. The art's white becomes linen lit from behind: a warm taupe at about rgb(178, 166, 148) where it's brightest, behind the middle of the scene, dimming toward the edges. The weave is a fine crosshatch of threads with some slubs and soft mottling. The art is multiplied onto the linen and its edges softened by a 0.9px blur. Its black is lifted to the reference's deep brown, rgb(30, 24, 16). The linen is `assets/backlit-linen.jpg` (43 KB, 1600×784), generated by script rather than photographed. The same linen sits behind every step. It's dim at the edges, so the joins between steps meet dark cloth on both sides and stay seamless. It runs at full frame rate.
- **How it's faked:** each moving step is drawn as one small SVG, cropped like the other steps, of layers in the same frame:
  - **A base:** the art with the moving figures painted out. The eagle and arm become white sky. A stub of the arm, the fist and chest stay, so no cut ever opens a gap. The horse becomes the white clearing, and where its legs met the grass, grass copied from beside it (that strip sits under the step text's shade).
  - **Each figure as a transparent silhouette,** cut from the PDF's full-size image: `step-procedures-bird.png` (24 KB), `step-procedures-arm.png` (11 KB), `step-character-creation-horse.png` (44 KB), and `step-rules-left.png` and `step-rules-right.png` for the swords. Each sword is the one shape joined to its blade, so his hair just below the blade stays in the base; the cuts are at her wrist and just above his lace cuff, with a stub of each kept in the base. The eagle and the horse are each one connected shape. The horse's lower legs are filtered so thin grass blades stay in the base. Small pieces left on their own in the clearing within 40px of the horse (the dress hem, the tail's last curl, a hoof's edge) move with it. Anything farther out, like the bushes and leaves in front of it, stays in the base. The arm is cut at the shoulder and beside the bowstring, keeping the head and body in the base. Only what's joined to the bow moves, so the hair tips beside the string stay with the head.
  - Put back together, the layers match the originals to within 0.15% of pixels (step 1) and 0.03% (step 3), all of it soft edges. The uncut originals (`step-procedures.jpg`, `step-character-creation.jpg`, `step-rules.jpg`) stay in `assets/`, unused.
- **Text:** each step's gold number, title, "Covers" line and, on step 2, the Character Generator link at the bottom. It carries its own shade (near-black, fading out over 72px), plus soft gradients over the art, so every line sits on near-black: contrast is at least 10:1 at every width. The text is inset `max(text edge, 64px)` so it clears the arrows and the joins.
- **Files:** each base is the PDF's own embedded image (3309px wide, black on white) with its moving figures painted out and the thin white strip along its bottom cropped off, resized to 1600px JPEGs at quality 70: `assets/step-procedures-base.jpg`, `step-character-creation-base.jpg` and `step-rules-base.jpg` (190–255 KB), inlined by the build with the cut-out figures. The art is decorative (`alt=""`), since each step's title says the same thing.
- **In JTD:** an optional `image:` front matter key on each of the three pages, read by the home layout, plus CSS scroll-snap and a small script for the arrows and the timer.
- **Licence:** Cairn Press's art; see Licensing to confirm.

## Podcast (removed 2026-10-31)
The home page's podcast section (Between Two Cairns, with a player and the latest episodes) was removed, and Adventures took its place. The build no longer calls Apple's lookup API. `cache/podcast.json` and `assets/podcast-art.jpg` are left on disk, unused.

## News and community
The home page closes with **News and community** (`#home-share`), which replaced the separate "From the blog" and "Made to be shared" sections on 2026-10-30. It has two halves, each under a small uppercase label (an H3): **From the blog** and **Share your work**. From 860px they sit side by side, split by the book's hairline stood upright (an open circle at each end, two-thirds brightness, as in the home divider). Narrower, they stack with no line. If no blog post is cached, Share your work fills the row and the line goes too. The nav's Community menu opens it as "News and community".

**From the blog** shows the newest post from Yochai's blog, [New School Revolution](https://newschoolrevolution.com/): its title, linked to the post, and the opening of the post under it. The post title is an H4, and "More posts on New School Revolution" sits under the excerpt. There's no date. It's plain, with no card, and the post sits in a 38rem column.
- **Source:** the blog's Atom feed (`/feed/`, made by Bear Blog). Its `summary` is empty, so the excerpt is the post's first paragraph that has text in it, cut at the last sentence end within 220 characters. If no sentence ends there, it's cut at a word boundary with "…".
- **Why it's saved by hand:** the site is behind Cloudflare, which answers scripts (curl) with a 404 on every page, and the feed sends no CORS headers. So neither the build nor a visitor's browser on cairnrpg.com can fetch it. We don't try to get around the block.
- **Build:**
  - The post is cached in `cache/blog.json` (title, link, excerpt, and the date it was saved). To update it, open `https://newschoolrevolution.com/feed/` in a browser, save the page source, and run `python3 -I build_prototype.py --blog-feed FILE`.
  - The build checks that the file is an Atom feed, that the newest entry has a title, a link on newschoolrevolution.com (so nothing like a `javascript:` URL gets in) and a paragraph. Otherwise it stops and says why, leaving the cache as it was.
  - `--refresh` doesn't touch the blog.
  - With no cache, the home page leaves the section out, with a warning.
  - When the cached post is more than 30 days old, the build prints a reminder.
- **Live site:** see Proposals (blog feed).

## Adventures (Scrivener, 2026-10-31)
**What it is:** [yochaigal/scrivener](https://github.com/yochaigal/scrivener) is Yochai's card catalog of Cairn adventures, built with [Gorlab](https://github.com/girtablu/gorlab) and live at https://yochaigal.github.io/scrivener/. Each adventure is a Markdown file in `posts/adventures/` with front matter only: `name`, `author`, `source-url`, `cover-image`, `summary`, `cost`, `tags`, sometimes `genre`. Yochai keeps editing it as he does now; the prototype only reads it.
- **Build:** a shallow clone of its `posts/` into `.scrivener-src/` (`--refresh` re-clones it into a temporary folder and swaps it in only on success). A plain build reads it offline. Without it, the catalog lists the Cairn repo's pages only, with a warning. A small parser reads the front matter (no PyYAML here), including the four posts whose front matter has no closing `---` (CAS-1 to CAS-3, Beyond the Pale).
- **Merging:** Scrivener's entries and the Cairn repo's `adventures/` pages are matched by name, ignoring case, punctuation, a `CAS-N:` prefix and a "(Cairn)" note. A match takes Scrivener's cover, summary and price, and the repo's page. ("Salthaven" and "Salthaven Town Gazetteer" are two different books, both in the repo.)
- **Today's counts (from the build):** 149 adventures in the merged catalog: 5 First Party, 82 originals, 62 conversions. Of Scrivener's 80 entries, 54 match a repo page and 26 are only in Scrivener. Not in Scrivener: CAS-4, and the originals Alfking, Heart & Forest, Priest King of Beaver Island, Salthaven Town Gazetteer, Sylvan Stele and Taintedweald (they show as text cards that link to their itch.io page). Rise of the Blood Olms is both CAS-2 and an Original in the repo; it's shown once.
- **Groups:** First Party (Scrivener's `first-party` tag, or `adventures/first-party/`), Conversions (`adventures/conversions/`), Originals (everything else).
- **Conversions:** their author and link come from the page's credit line (the first line that links out): the name after "by". Three credit no one by name ("Get the adventure here", "Get the original here", "Converted from Mark Penn & Steve Wilson's …"), so their cards show no author.
- **Covers:** Scrivener's own itch.io URLs, hotlinked with `referrerpolicy="no-referrer"` and `loading="lazy"`, so each loads only as it nears the screen; not downloaded. CAS-4's comes from its page's first image on cairnrpg.com. One cover is gone today: "A Sliver of Evidence" (itch.io answers 403). A card whose cover is missing or doesn't load shows a panel with its group and name.
- **Catalog (`#adventures`):** one form of six fields: Search (names, authors, summaries, tags), Group (with counts), Author, Price (Free / Paid / Not listed), Tags and Sort (A to Z, Z to A, and Free first: by price, free first and unlisted last, whatever the currency). They sit in one row from 1100px, each select as wide as its longest option (Author capped at 12rem) and Search taking the rest; below that, two rows of three; on phones, two columns with Search across them, and one column on the narrowest screens. The count is a live status. Every adventure is listed at once; each filter change re-renders the list (fine at 149 cards; worth a second look at several hundred). The filters live in the address (`#adventures?g=originals&q=forest`), so a reload, Back and shared links keep them; a value the form doesn't offer (an unknown author or tag) falls back to All.
- **Cards:** one link each: the cover, the name, the author, the summary's first paragraph (three lines at most), and badges for the price ("Free" or "Paid": the amount is left to the store's page, though Sort still orders by it) and the group. A card opens the adventure's page here if it has one. Otherwise it goes straight to the store's own page, in the same tab, so the store does the selling: those cards say "On itch.io ↗" (or "At the Cairn store ↗") at the end of the badge row, and search results say the same, so nobody leaves the site unawares.
- **Detail view (`#adv-<name>`):** no longer linked from the catalog or search; it stays for older and shared links, and for an entry with no store link (none today). It shows: the cover, name, author, badges, tags, the whole summary, a "Get it on itch.io" (or "at the Cairn store", from the link's host) button, and Back to the catalog with its filters.
- **Readable pages:** CAS-1 to CAS-4 and all 62 conversions are pages in the prototype, from their own Markdown, with the same converter, ids and parity check as the Second Edition. They have an Adventures sidebar (First Party and Conversions) and drawer, crumbs Home › Adventures › group › page, previous/next within the group, and the header marks Adventures. The CAS pages keep their own "available in print / as a PDF" links at the top; the ⋯ menu drops the Second Edition PDF link on adventure pages. They're English only.
  - **Headings:** each page's headings move by the same number of levels so its top level becomes its sections: the modules' H1 chapters move down one (the first H1 is dropped only when it's the page title), and conversions that start at `###` or `####` move up. Headings above the first section get ids too.
  - **Images:** the modules' 27 images and CAS-4's 18 map blocks (`{::nomarkdown}<img …>`) are hotlinked from cairnrpg.com, lazy-loaded, with a box held for them. The two file names with spaces (White Tower Fens, Temple of the Fungal Lizards) are encoded. The maps have no alt text in the source; the proposed ones are "Map: <its section>", and four by name (`MODULE_IMAGE_ALT`).
  - **Other markup:** `<br>` in table cells is a line break, code fences are `<pre>`, and any other raw HTML stays escaped as text, as the converter always did. As on the live site, `3)` doesn't start a list, and an indented paragraph after a blank line stays in its list item.
  - **Typos in the source, left as they are** (and shown the same on the live site): neverland.md's credit link ends in `/]`, and willow.md has an unclosed `**` in "Mutilation pack".
- **Home (`#home-adventures`):** "Adventures for Cairn", in the podcast's old place under the hero. The heading's right end has the group counts linking into the catalog, then "Browse all adventures" as a secondary button at the far right. Below are four cards (`FEATURED` in the build, by entry id; by default the first-party modules readable here). The proposed Adventures summary is on the catalog page only. On phones the heading, links and button stack. The cards end 80px above the Start slideshow (the usual 56px section padding plus 24px). 4 across at 1200px and up, 2×2 below that.
- **To pass on to Yochai:** the dead cover, and the seven adventures missing from Scrivener (listed above).

## Second Edition pages
Every Second Edition page opens in the prototype, in full, from the author's own Markdown: the Second Edition index, the Player's Guide (6 chapters), the Warden's Guide (23), Backgrounds (20), Tools, Game Files and Print Copies. That's 56 pages. The Character Generator is an interactive tool, so it stays a link to cairnrpg.com.
- **Nothing changes for the author.** The build only reads the Markdown; on the live site, Just the Docs keeps rendering the same files as it does today. The converter below exists only so the prototype can show the real content.
- **Routes and ids:**
  - A page opens at `#<file name>` (`#aurifex`). The two Valds are `#pg-vald` and `#wg-vald`. Core Rules stays at `#rules`, with `#core-…` headings, so links shared before still work.
  - Every heading, H2 to H5, has the id `<page>-<slug>`, numbered on repeats the way GFM does (Procedures has two "Light" headings).
  - The build stops if any id repeats across pages or clashes with one of the template's own ids.
- **Links:** every Second Edition link opens its page in the prototype: in the sidebar, the home page, Resources and inside the text, including relative ones like the Warden's Guide's own contents (`setting-seeds#factions`). A `#fragment` lands on the right heading, through each page's GFM anchors. Links to the rest of cairnrpg.com and to other sites stay as they are.
- **Layout:** the Rules view shows one page at a time (Core Rules is the default):
  - its breadcrumbs from the page's parents (Home › Second Edition › Player's Guide › Core Rules), only when the sidebar is hidden, from 760 to 1099px. Then they're the page's top row, above everything else. Phones (below 760px) have none: the Menu drawer and previous/next lead up. Resources has no breadcrumb;
  - the sidebar, with the current page marked and its group open;
  - "On this page", hidden when a page has no sections. On phones it's a button under the title, and on Character Creation under the "Roll a Character right now!" callout;
  - previous and next within its group, in the sidebar's order;
  - the ⋯ menu beside the title: Edit on GitHub, Download as PDF, Open on cairnrpg.com;
  - back to top.
  
  Index pages list their child pages after their text, as JTD does.
- **Core Rules:** word-for-word from `core-rules.md`, except the Reactions table, rebuilt as Roll / Reaction, and a caption on the Scars table. The illustrative Tip callout was removed on 2026-10-30; it wasn't in the source.
- **Aligned tables:** in six sections, a run of same-shaped roll tables shares one set of column widths, so their columns line up down the page:
  - Character Creation: Character Traits (8 tables).
  - Dungeon Seeds: Creating a Dungeon (5) and Draw Paths (4).
  - Forest Seeds: The Spirit of the Forest (2) and Trails (5).
  - NPC Tables: Traits (2).
  - **Marketplace:** all six price lists line up across the whole page (item, cost).
  - **How:** `alignTables()` in the template (`table-layout: fixed`). It applies in every mirror translation too.
    - A short column (a die or a price, up to 10 characters) gets a fixed width in characters, so a phone never squeezes it.
    - The long columns share the rest, in proportion to their longest entry across the run.
    - Long row headers wrap, so on a phone a table never scrolls sideways.
  - **More:** add a section's id to `ALIGNED`, or a page's to `ALIGNED_PAGES`.
  - **In JTD:** a small CSS rule on that section's tables, or the same script.
- **Converter additions:** ordered lists, lists nested to any depth by indent, `#####`, `* * *` rules, kramdown's hard line breaks (`<br>`), HTML entities such as `&harr;`, backslash escapes, and empty links (shown as text). Tables too wide for the column scroll sideways, and take keyboard focus when they do. An empty header cell, or an empty first cell in a row (the Vald calendar), becomes an ordinary cell.
- **Example maps:**
  - The 4 Warden's Guide maps (Dungeon Seeds, Forest Seeds, Pointcrawls, Setting Seeds) come from the checkout (`img/2e/`, in the sparse checkout).
  - They're resized to 1600px wide as WebP into `assets/2e/` and inlined, about 50-150 KB each. Each links to its full size on cairnrpg.com.
  - **Alt text, a proposal:** the source's alt text is "Click Me!", so `IMAGE_ALT` in `build_prototype.py` describes each map. The author could copy these into the Markdown.
- **Broken links in the source**, shown as plain text:
  - Naming Procedures links to `#poi-names-formula` and `#terrain`, but those headings are "Names Formula" and "Terrain Synonyms".
  - The Russian and Turkish Naming Procedures carry the same two links, and the Russian has one unclosed `**`.
- **Parity check:** `python3 -I build_prototype.py --parity` fetches every page from cairnrpg.com, and each translated page from its language site.
  - It compares H2, H3, H4/H5, tables, table rows, list items and images inside `<main>`, leaving out JTD's own child list.
  - **Result on 2026-10-30:**
    - English: 55 of 56 pages identical. Core Rules differs only because its Reactions table is rebuilt on purpose, as Roll / Reaction rows (19 rows here, 15 live).
    - All 185 translated pages on fi, ru, zh and pt-BR are identical, once their sites' empty header rows are ignored (we drop those).
    - he and tr have no site, so they can't be compared.
- **Size:** `prototype.html` is about 9.6 MB with every page and translation inlined. The build warns above 12 MB.

## The rest of the site (2026-11-03)
Every page of cairnrpg.com that has Markdown in the repo now opens in the prototype, so the only links that leave are storefronts, Google Docs and creators' pages. `check.py` fails on any link in a page, the hub or the menus that goes to cairnrpg.com, apart from the tools below and the hotlinked adventure images.
- **What's in** (`SECTIONS` in `build_prototype.py`: a top-level file and the folder beside it, and the group its pages belong to):

  | Section | Group | Pages |
  | --- | --- | --- |
  | First Edition (`first-edition/`): the SRD, FAQs, Example of Play, Print Copies, Accessibility, Tools | `fe` | 11 |
  | Resources (`resources/`, 14 pages; the hub stays the tiles view at `#resources`) | `res` | 14 |
  | Hacks | `hk` | 13 |
  | Barebones (rules, game files, print) | `bb` | 12 |
  | Submissions, Third-party, Official App | `sub`, `tp`, `app` | 4, 3, 1 |
  | Localization: the two guides, the index pages and the SRD in Portuguese, Russian and Turkish (marked with their language) | `loc` | 8 |

  The 122 earlier pages keep their ids and their text (checked page by page; seven adventures only changed their SRD links, which now open it).
- **Stays a link, on purpose:** the pages that are only a `redirect_to` (Discord, Cairn Press, the store, Cottonbureau, the newsletter, the First Edition game files in Google Drive, and the itch.io adventures). A link to one goes straight to its target, not through cairnrpg.com. The interactive tools (the Second Edition Character Generator, the First Edition generator, the Barebones generator) stay on the live site. The two `.epub` files link to the repo that holds them.
- **How a page is placed:** its parent is the Markdown file named like its folder (`resources/x.md` under `resources.md`, `barebones/rules/x.md` under `barebones/rules.md`), because `parent:` in the front matter is a page title that repeats ("Tools" twice) or names a page that isn't there ("Third Party Hacks"). The sidebar, the breadcrumbs, previous / next and the page counts read that tree (`D.sections`). The Resources pages sit under the hub, so their breadcrumb starts at Resources.
- **Ids:** Second Edition and adventure ids never change. A page from the rest of the site takes its section as a prefix when its file name is shared: `fe-tools`, `fe-print-copies`, `bb-game-files`, `loc-localization-guide`, `loc-second-edition-localization-guide`. Old addresses the Markdown still links to (`/cairn-srd`, `redirect_from:`, `/hacks/hexcrawl-procedures`, `/hacks/third-party`) are mapped to their pages.
- **Details in the text:** the author's `<details>` examples (the SRD, the FAQs, GLOG Magic) are real disclosure widgets, and the FAQs' "expand all" is a button. The SRD's table of contents is dropped (the page has its own list), and so are kramdown's `{: ...}` lines and the FAQ's script tag. Links from `More Spellbooks` and the SRD to a spell's own heading point at headings that don't exist (216 and 107 of them, on the live site as well), so their text stays and the link is dropped. The Monsters index and each monster's page open the Monsters view.
- **Not done:** bundling the hotlinked adventure images; the other languages' First Edition SRDs (they redirect to their own sites).

## Translations
The flag button, just left of ⋯ on the Core Rules title row, switches the page between the community's own translations. They aren't ours: each one is read from the repo that publishes its language site, so the translators keep owning their text and corrections land with them.

| Language | Repo | File | Site | Mode |
|---|---|---|---|---|
| Português (Brasil) | `RenanStigliani/cairn` | `regras/core-rules.md` | pt-br.cairnrpg.com | own: 11 H2, 19 H3, 3 H4 |
| Suomi | `kari/cairn-fi` | `second-edition/players-guide/core-rules.md` | fi.cairnrpg.com | mirror |
| Русский (beta) | `TheVarik/cairn-ru` | `second-edition/players-guide/core-rules.md` | ru.cairnrpg.com | mirror |
| 中文 | `Ascll-Ray/cairn-zh` | `players-guide/core-rules.md` | zh.cairnrpg.com | mirror |
| עברית (unofficial) | `EliyahuRotenberg/cairn-he` | `second-edition/players-guide/core-rules.md` | none yet | mirror |
| Türkçe (unofficial) | `cuceGenco/cairn-tr` | `second-edition/players-guide/core-rules.md` | none yet | mirror |

All six say CC-BY-SA in their README or licence file; the build checks that on every refresh. As of 2026-10-30.
- **How they're found** (`discover_translations` in `build_prototype.py`):
  1. **Languages:** each Second Edition stub in `localization/second-edition/` claims its language. Its code comes from the filename (`finnish` → `fi`). Stubs redirecting to a `*.cairnrpg.com` site are shown.
  2. **Repo:** the one that publishes that site, meaning its `CNAME` names the domain or its homepage is the site. Candidates are forks of `yochaigal/cairn` with that homepage, plus repos linked from the site's home page. A repo that's only credited or linked never wins. If two qualify, neither is shown, and both are reported.
  3. **Files and mode, per page:**
     - **Finding the page:** the repo's `core-rules.md` fixes its Second Edition root (fi: `second-edition/`, zh: the repo root). Every other page is looked for at the same path under that root. pt-BR has its own layout (`regras/`), so its pages are matched by file name.
     - **What counts:** a page counts as translated when at least half its lines differ from English.
     - **`mirror`:** the same headings (H2-H5) and the same tables and lists per section as English. It's paired with English heading by heading and shares English's ids, so `#core-saves` works in every language.
     - **`own`:** its own structure, with ids `<page>-<code>-<n>`.
     - **When it's set:** the mode is set when a page is first found, and again whenever its file changes. A page that stops fitting its mode is left out with a warning; it's never quietly relabelled.
- **Unofficial:** for a language with no stub, a fork named `cairn-<code>`, as the localization guide (`localization/localization-guide.md`) tells translators to name it. It must carry a `core-rules.md` that's at least half translated, with the English structure. Country codes are mapped (`cz` → `cs`, `ua` → `uk`), and codes are compared by base language. If several forks qualify, none is shown, and all are reported. When Yochai lists the language's site, it switches to the repo behind that site.
- **Left out on purpose:**
  - **Spanish:** its stub points to the commercial Player's Guide on itch.io, so no Spanish is shown, and `cairn-es` is reported but not used. `L10N_OVERRIDES` also excludes `es`.
  - **Forks of a listed language:** `cairn-pt`, `cairn-zh-cn` and `cairn-cn` are reported, not shown.
  - **Machine translation:** none from us. The localization guide itself suggests DeepL or Google Translate as a translator's starting point, so a community fork may have begun that way.
- **Corrections:** we follow the repo that publishes the site. A fix someone makes in their own fork arrives once it's merged there. Using a different fork means pinning it in `L10N_OVERRIDES`; nothing picks between forks on its own.
- **Overrides** (`L10N_OVERRIDES`):
  - pt-BR's file (`regras/`).
  - zh's repo: it's not a fork and its site doesn't link it, but its `CNAME` names the site. The pin is re-checked on every refresh, with a warning if it stops publishing the site.
  - ru's "beta" note.
  - Spanish excluded.
- **Parsing:**
  - The translators' tables and Reactions layout are kept as written, without English captions, since we don't translate.
  - Heading ids follow each fork's Markdown settings (GFM keeps Unicode). The translators' own `#` links are rewritten to the ids used here, and a mirror language falls back to the English anchors.
  - Relative and `/` links point at that language's site.
  - Only `#`, relative, `http(s)` and `mailto` links are kept, and raw HTML is escaped.
  - `{: … }` attributes are dropped, and their `#id` is kept.
  - `####` renders one level below its section, so no level is skipped. `>` becomes a blockquote, and an all-empty first table row is dropped.
- **Per page:**
  - The chosen language applies to every page.
  - A page a language hasn't translated shows English, with the note "Not translated into … yet". That language is greyed out ("not translated yet") in that page's menu.
  - Pages today:

    | Language | Pages translated (of 56) |
    |---|---|
    | Русский | 54 |
    | Suomi | 52 |
    | Türkçe | 52 |
    | Português | 48 |
    | 中文 | 31 |
    | עברית | 25 |
- **Deep links:**
  - A link to a heading the current language doesn't have opens the language that has it (English first), for viewing only.
  - The saved choice (`localStorage` `cairn-rules-lang`) changes only from the menu.
  - Picking from the menu resets the address to the page's own `#id`.
- **Left in English:** the sidebar, breadcrumbs and previous/next.
- **Cache and network:**
  - The cache is `cache/l10n/index.json` plus `cache/l10n/<code>/<English path>` for each translated page.
  - Today's single-file Core Rules cache is moved into that layout on the first build.
  - About 300 files are downloaded, four at a time, from `raw.githubusercontent.com`.
  - A refresh uses about 16 unauthenticated GitHub API calls, out of 60 an hour. Candidate forks are checked through `raw.githubusercontent.com`, which isn't limited.
  - If the API refuses, the previous list is kept. A language whose file is missing is left out, with a hint.

## Dice roller
A **Dice** pill at the header's far right, after search, opens a drawer from the right. It's laid out like the character creation drawer: a sticky header, a scrolling body that is mostly the roll history, written like a chat, and a footer with the dice and a field to type a roll. Any die written in the text rolls too.
- **Library:** [dice-box](https://github.com/3d-dice/dice-box) 1.1.4 (2024-08-24), MIT; its dice models and textures are CC0. It's loaded from jsDelivr, pinned to that version, only the first time the tray opens or a roll is made (about 2.2 MB: the script, a physics engine and the dice theme). It's never loaded otherwise.
- **Dice colour:** sampled from the hero art. The default is **Tree teal #41697d**, the trunk's dusty slate-teal (hue 180–200°, which the cover has very little of). **Settings** offers four: Tree teal, Hero deep rose #ae3f6c (the monster's reds), Tree purple #44345c (the bark's deep tone), and Blood red #b3122e (a truer red, to compare). All four get white numbers (dice-box picks dark numbers only above a brightness of 175; the teal is 95). The choice is kept between visits (`cairn-dice-colour`). A new colour applies to the next roll; if dice are on the table, it re-rolls them in it. The character builder's bar, and its highlight on the rolled table row, follow the colour too.
- **Drawer:** a native manual popover (`popover="manual"`, opened and closed by the pill's `popovertarget`), so the page stays usable while it's open: you can click a table's die with it open. It runs the window's full height, over the site header, 360px wide (the full width on phones), and slides in (not with reduced motion). Its ✕ and Escape close it and return focus to the pill, which opens it again (the pill sits under the drawer while it's open). It stays open from page to page and from visit to visit until closed (`cairn-dice-open`). Restoring it on load doesn't move focus; opening it from the pill puts focus on the first die. It needs Safari 17, Chrome 114 or Firefox 125 or later.
  - **Header:** a sliders button on the left (Settings; inside Settings it becomes ‹ Back, in the same slot as the builder's Back), the title, and ✕ on the right.
  - **Body:** the history. A short history sits at the foot, as in a chat.
  - **Footer:** the picked dice and the field, under a 3px line in the dice colour, like the builder's bar.
  - **Result card:** a roll made while the drawer is closed shows as a small card under the pill, which also appears for rolls made through the API (except in the builder's walkthrough). Clicking the card opens the drawer, and Escape hides it. The dice and the card stay 8 seconds after the dice land, then clear (in the builder's walkthrough the dice go sooner: see its Dice notes). On a phone with 3D dice, the drawer steps aside for a roll so the dice are seen, and the card shows the result.
- **Settings** (the sliders button swaps the history for it; Back or Escape returns): the **3D dice** switch, the dice colour, and **Clear history**, which confirms with a notice (below). The 3D switch is on by default and kept between visits (`cairn-dice-3d`). Off, rolls use plain random numbers and dice-box isn't loaded. With reduced motion, or without WebGL, it's off and disabled, with the reason under it. Turning it off clears dice on the table. A roll made from the page while Settings is showing switches back to the history.
- **The dice row** (footer): six dice, d4 to d20, about 46px each, that work like adding an item to a cart. Tapping a die adds one, and a gold count appears on its corner. The count and the stepper are one control: tapping the count opens it into **−**, the count and **+**, where − is a bin at 1 (it removes the die); it folds back to the gold count. It closes when you click or Tab elsewhere, press Escape, tap the count, or leave it alone for half a second, but never while a keyboard user is inside it (or the pointer is over the stepper). Pointing at the gold count opens it without adding one; pointing at the die itself does nothing, and clicking the die adds one and opens it. The pill grows out of the gold circle and folds back into it, in about a fifth of a second (the − and + have no width until it's open, and they're not focusable then); with reduced motion it just switches. The first die's pill grows to the right, the others' to the left, so it stays inside the drawer. Removing the last of a die takes its count away at once. Up to 10 of each die, 20 in all. Once dice are picked, a line above them shows the pool ("2d6 + 1d8") with Clear; with nothing picked there's no line (it stays in the page for screen readers, which hear the pool and the "Up to 10 of each die" and "Loading dice…" messages there).
- **The Roll field** (footer): one field and one Roll button, as in a chat. Roll sends the field's text if it has any (`2d6+1`, `d8+d8`), otherwise the picked dice, summed; text wins if both are there. The field clears after a roll and the dice stay, so the same dice can be rolled again. Notation it can't read shows an error above the field, and the field is marked invalid. Roll is off only when both are empty.
- **History:** each roll is a message: its label and notation, the total, each die's value, any table row, a note if 3D dice were wanted but the roll was made without them, and the time. Oldest first, the newest at the foot, and the drawer scrolls there when it opens and after a roll. A new roll adds one message, so a screen reader hears only it. It keeps the last 50, shared by 3D and plain rolls, the dice row, the field and the text's dice, and survives a reload (`cairn-dice-history`). **Clear history** (in Settings) empties it, and a green notice says so. The character builder's rolls aren't kept in it.
- **Dice in the text:** as on cairnrpg.com (its `jekyll-dice-tray` plugin), the build turns dice notation into buttons: `1d8`, `d8+d8`, `2d6+1`, in paragraphs, lists, table cells and stat lines, in every language that writes dice that way (Chinese included; Russian's `1к6` isn't matched). Only real dice (d2, d3, d4, d6, d8, d10, d12, d20, d100), not d66, not a map's "Areas D0 and D3", and never inside a link or a heading. A click rolls it and opens the drawer (on a phone with 3D dice, the card shows instead). A die heading a roll table's first column (`data-die`) also marks the row it lands on and scrolls it into view; rows can be numbers, ranges ("1-3", "1–3"), "5+", or d100's "00". The roll's label is the table's caption or title column, or, on a stat line, the creature and weapon ("Ifrit · Flaming sword").
- **"d8+d8" keeps the highest:** Core Rules say several weapons' dice are rolled together and the highest kept, and the live site's tray reads it so. A die in the text and the typed field follow that rule for any sum of single dice (`d8+d8`, `d10+d8`); the result reads "Highest 7". Dice picked in the pool are still added up. The builder's rolls are unchanged.
- **Fallbacks:** offline, rolls use plain random numbers (`crypto.getRandomValues`) and say so (the result's `fallback` is `'offline'`; `'plain'` for other cases where 3D was wanted but couldn't run). With the 3D switch off there's no note: plain dice were asked for.
- **Live site:** copy dice-box's `dist` files into `assets/dice-box/` instead of using the CDN, the script into `assets/js/dice.js`, and the pill into `_includes/header_custom.html`.

### Notices
The result of an action shows as a **notice**: a card that slides down from the top centre of the screen (420px wide at most; 16px from each edge on a phone). It's separate from the dice result card under the pill.
- **Uses today:** Clear history (green, success) and the character builder's Print failures (red, error): the sheet not ready yet, printing blocked, or a pop-up blocker stopping the sheet. The builder's progress lines ("Filling in your sheet…", "Ready to print…") stay in its status line, and a field's own error (dice notation it can't read) stays by the field.
- **Look:** the surface card with a 4px bar and a round icon in the kind's colour: ✓ green `--ok` #6cc479 (6.4:1 on the card), ! red `--err` #f07a6a (5:1), i gold for info. The text is the heading colour, whatever the kind.
- **Timing:** success and info go after 5 seconds, held while pointed at or focused; errors stay until their ✕. Escape closes a focused notice. Up to 3 show, newest on top; a fourth drops the oldest, and the same message again replaces the one showing. They slide in and out; with reduced motion they fade.
- **Screen readers:** the cards are hidden from them except for their ✕ ("Dismiss: …"). The message is said through two always-present live regions: polite for success and info, an alert for errors. A notice never takes focus; closing one that has it returns focus to where it was (or, if that's gone, hidden or behind the builder's modal, to Roll, Next or ✕ there, else the Dice pill). Messages are written to the regions 100ms after clearing them, so a repeat is heard, and the regions are emptied before they move into or out of the builder's modal, so an old alert isn't read again.
- **Layering:** the dice drawer and the builder's walkthrough are both in the top layer, so the notices are a manual popover, re-shown (which puts them back on top) whenever one is added, the drawer opens, or the walkthrough opens or closes. While the walkthrough's modal is open they move into it with the dice, as everything outside it is inert.
- **API:** `CairnNotify(message, {kind: 'success' | 'error' | 'info'})`.

### Dice API
The site is static, so the "endpoint" is a JavaScript API on the page. The tray rolls through it, and so does the character builder (below), its first user.
```js
const str = await CairnDice.roll('3d6', {label: 'Strength'});   // the dice tumble, then:
str.total        // e.g. 11
```
- `CairnDice.roll(request, {label, show3d = true, highest = false, record = true, detail})` returns a Promise of a result. `request` is notation (`'3d6'`, `'1d8+1d6'`, `'2d6+1'`, `'1d8+1d6-2'`), an array of those, or `[{sides: 6, qty: 3, modifier}]`. Notation it can't read rejects with an error. `highest` keeps the highest of a sum of single dice; `record: false` leaves it out of the history (the default inside the builder's walkthrough); `detail(total)` returns a line shown with the result (a table's row).
- **Result:** `{id, label, notation, total, mode: 'sum' | 'highest', detail, modifier, dice: [{sides, value}], groups: [{sides, qty, values, subtotal}], method: '3d' | 'random', fallback: '' | 'plain' | 'offline', rolledAt}`.
- Rolls run one at a time, so `Promise.all` of several rolls is safe; they resolve in order.
- Every roll also fires `document` event `'cairn-dice:rolled'` with the result as `detail`.
- `CairnDice.history` is the history, the last 50 results, newest first. `CairnDice.open()` and `close()` open and close the drawer. `CairnDice.clear({fade = false})` takes the dice off the table at once, or with `fade`, after a 0.4s fade (instant with reduced motion).
- A roll in a background tab finishes when the tab is shown again (the 3D dice only move while the page is visible).

## Character builder ("Roll a Character right now!")
On English Character Creation, a one-line callout under the title starts a guided build: "Roll a Character right now!", with a **Start** button on the right. Once a build is under way it reads "Continue your character · Step n of N", with Start over and Continue. Start opens a full-screen walkthrough, one step at a time. Its controls: the sheet drawer's sticky header holds **‹** (Back) left of the title and **✕** (Close) top right; on phones that header is fixed to the top and reads "Roll a character". The foot (a footer fixed to the bottom on phones) holds only buttons: **Roll** (then **Roll again**, outlined once rolled, with **Next** primary) rolls that step's dice with the 3D dice, and **Next** locks the result in; Skip and the swap choices appear on their steps. While a roll runs, Roll reads "Rolling…". ✕ or Escape closes it, keeping the progress. Under the step's name, one line says what the roll picks and where ("Roll a d20: the number picks your background from the table."). The rolled row is highlighted.
- **The walkthrough:** a modal `<dialog>` over the whole screen.
  - **One area of the sheet at a time (2026-11-02):** the steps follow the sheet, so the zoom doesn't bounce.
    - **The order:** Background → **Armor and weapon** → Gold (gear) → the background's first table (zoomed on Inventory) → its second (zoomed on Notes) → STR, DEX, WIL, Swap → HP (and bonus HP) → Traits → Age → Bond (and the second) → Omen → Name → Review.
    - **Armor and weapon:** a step with no roll, before the gear is listed. Its card shows the Armor value and the armor's name, or "None". It also shows each weapon in the background's gear, with its damage (the dice opening its bracket, e.g. "Long Sword · d10"; Fletchwind's bow is "from your tables"). On the sheet, the Armor box and the weapons' slots get a gold ring.
    - **Notes:** the background's description goes last in Notes. Print adds it only if it fits whole (usually it doesn't, after the tables' notes), and it never causes the "Notes is full" warning.
    - **Traits:** an "All 8d10" switch at the right of the title, on by default. Off, each Roll is a d10 for the next table, and Next waits for all eight; once they're in, Roll again starts over.
    - **Age:** "Or type an age" (digits only) works like the typed name. A review edit of the traits' Age, or a roll, replaces it.
    - **Headers:** each step's header band meets the drawer header's stroke, with no gap.
  - **One step at a time:** each step is a card, built from `D.builder`, not a slice of the page.
    - **Header:** "Step n of 15" (plus "Your background: …" on that background's steps), the step's name, its dice and the one-line instruction. The controls don't repeat it.
    - **Body:** the step's table as clean numbered rows, with the roll marked. The 20 backgrounds and 10 names sit in two columns (under the names, an "Or type a name" field: what's typed fills the sheet as you type and counts as the step, and a roll afterwards replaces it), and the rolled background's quote shows under its name in the rolled row, so it's read where the eye already is. Gold shows the starting gear and a gold box. The background's two tables show their rows, and a row with two cells shows its first as a bold heading. Traits shows 8 small blocks, all marked. The bonds and omens show their 20 rows; a skipped omen says so.
    - **No-table steps** (STR, DEX, WIL, HP, Bonus HP, Age): one line of the rule and a large framed value, dashed until rolled. Swap shows the three scores after any swap.
    - **Text:** bold and italics kept; links shown as their words.
    - **The review:** still the page's own section, moved in, so its ids stay single.
    - **The page itself:** still built with its panel and highlights, as the trail once the walkthrough closes.
  - **Leaving:** ✕ or Escape closes it, keeping progress (the cards hold no links; Download PDF leaves it open). The page is rebuilt, and focus goes back to Continue. Leaving the page closes it too.
  - **Dice:** the 3D dice, the result card and the dice tray move into the dialog while it's open; a modal would otherwise cover them and leave them inert. In the walkthrough there's no result card: a roll shows on the 3D dice and the cards. Once the dice land and the rolled row is highlighted, they fade out over 0.4s so the row can be read; Next, Back, Skip and ✕ clear any still on the table, so each table starts clear. While a roll runs, Roll reads "Rolling…", and a status such as Loading dice… is announced through the result line.
  - **Keyboard:** the step area and the drawer both scroll, so both take focus. Each new step focuses its heading.
- **The vines:** the Warden's Guide p.31 pattern fills the screen behind the step.
  - **Growth:** the first time the first table shows, they climb from the bottom of the screen to the top. Six soft-edged columns along the bottom edge (40% wide, at 0–100%) grow from 0 to 160% high, each at its own pace (delay plus duration at most 5s, nearly linear). The front is about 40% up at 2.5s, and every column reaches the top between 4 and 4.5s.
  - **After that:** `cb.grown` is saved, so a roll, Back or a reload shows them grown. Start over grows them again. With reduced motion, they're simply there.
  - **Mechanics:** `@property --v1…--v6` animate the masks; the tile itself is masked on the `::before`. Without `@property`, they show at once.
  - **Source:** the spreads PDF embeds them as their own masked image (PDF page 16, xref 1069 with SMask 1070), apart from the door on top. The pattern repeats every 572px, so one repeat is a seamless tile: `assets/vines.png` (400×400, 62 KB, an alpha mask), inlined by the build, drawn in a muted linen tone (#8f8370) at 260px.
  - **Accessibility:** decoration only.
- **The sheet stage (760px and up):** the walkthrough's left side is the official **portrait** character sheet (`assets/cards/sheet-portrait.webp`, the fillable PDF's own 2550×3300 image), filling in as you roll. It lies on the stage like paper with a soft shadow, running full bleed off the stage's edges; wherever a zoom brings one of the paper's own edges into view, that edge stops exactly 66px in (never a sliver), so a band of vines shows beyond it. On the first Start the whole sheet sits in the middle of the stage, at least 66px in on every side, while the vines grow in round it (5s). When they finish, the sheet zooms to Background and the drawer slides in; until then the drawer is hidden, so Escape is the only control (the stage's ✕ is in the hidden drawer) and focus waits on the stage, named "Your sheet. The first step opens in a moment". Phones get the same first start without the stage: only the vines and the header (with ✕) show while they grow, and when they finish the footer slides up from the bottom and the Background step rises in behind it, fading in, a beat later. Both move on springs generated with Motion's `spring(visualDuration, bounce)` as CSS `linear()` easings (footer .5s/.15, step .65s/.12), with a cubic-bezier fallback. Later opens and reduced motion go straight to Background. Each step zooms to its section, title and all (at most 1.8×, the section at about 60% of the stage, so the page stays in view), and a soft golden glow, gently pulsing, marks the area about to be filled in; nothing is dimmed. Next and Back pan over .8s (instant with reduced motion). The fields are the portrait PDF's own rects; its traits are four lines of pairs, as Print writes them. The written lines sit .5em in from the printed rule's start, and a single line 16 sheet px higher (about 8px at the stage's 1.8×; Name 8, trait lines 12, as their boxes sit differently); STR, DEX and WIL 4 sheet px and HP 12 higher (about 6px on the stage, less on the unzoomed review cards); multi-line boxes keep their lines on the printed rules, and a long bond or omen is set a little smaller (by up to a quarter) so it fits its box whole, and the scores, Armor and Gold stay centred. STR, DEX, WIL and HP fill only the box left of the slash, bold and centred as on landscape: the box right of it is for the character's current state in play, so it stays blank on screen and in print, on both sheets (landscape's is the box under each score). At the review the whole sheet sits in the middle of the stage, at least 66px in on every side, editable; the Landscape/Portrait picker switches the stage's preview between the two sheets with the same data, and Print fills the one shown (portrait unless one was picked).
  - **The drawer there** holds the step's card (header and table) and its buttons; the results list is for phones only.
  - **Dice:** while the walkthrough is open, the 3D dice's canvas takes the stage's box, and dice-box re-fits its world on the resize event, so dice roll and settle inside the sheet's space (checked with a real roll).
  - **Phones** keep the earlier layout: the step card and the results list. At the review they show that list again, with every value editable (see The review).
- **The drawer, "Your sheet" (desktop and tablet, 760px and up):** it slides in from the right on Start and stays open (`min(440px, 40vw)`). Under a sticky header (‹, "Your sheet", ✕) it builds the character as a list, a row per result in the order they were rolled: Background, Name, Gold and Gear, the background's two answers, STR/DEX/WIL (after any swap), HP (with any bonus), the 8 traits, bonds, age and omen. The current step's row waits, dashed, until rolled; a changed value fades in; on desktop the current row is kept in view. Values come from the same character the review and Print use, edits included. Only the step's buttons sit at its foot. Each roll lands in the drawer's list, as well as on the dice and the step card's marked row.
  - **Phones (under 760px):** there's no drawer. The walkthrough scrolls as one column: the step, then the list under "Your sheet". The drawer's header is fixed to the top ("Roll a character", ‹, ✕), the buttons are a footer fixed to the bottom, and the column keeps both heights free (padding and scroll padding, measured). At the top of the footer, **"Your sheet · n of N filled"** (with the newest result under it) is a collapsible row: chevron on the left, no box. Tapping it grows the footer into a full-screen sheet, its top edge rising from where the footer starts (.35s); the list fades in as it arrives. The sheet's header is the same row, chevron pointing down, and tapping it (or Escape) collapses it back down. With reduced motion it opens and closes at once.
  - **Tab order:** the step, then the header's ‹ and ✕, then the list and the foot's buttons.
  - **At the review:** the drawer keeps only its header (‹ and ✕, in a 340px column), because the full sheet is the main view; its foot has no buttons there and is hidden.
- **Steps:**
  - background (d20);
  - name (d10);
  - gold (3d6);
  - the background's two d6 tables;
  - STR, DEX and WIL (3d6 each), then an optional swap of two;
  - HP (d6);
  - the 8 traits, together (8d10: one die per table, in the book's order; Roll again re-rolls all 8);
  - bond (d20);
  - age (2d20+10);
  - omen (d20; optional, with Skip, unless the background calls for one);
  - review and print.
  
  That's 15 steps. A background can add a **Bonus HP · d4** step (Fieldwarden, Kettlewright) or a **second bond** (Fieldwarden, Outrider 6); a repeat bond is re-rolled. It can also force a secret omen (Foundling, Mountebank 3).
- **Your background:** once rolled, the background's quote, names, starting gear and two tables are built under the Backgrounds table, and each becomes its step's content.
- **Data:** `D.builder`, parsed from the Markdown by `builder_data()` in the build.
  - **Effects:** each background row and bond gets its effects on the character: items with their petty/bulky/armor tags, gold, extra rolls, Marketplace picks.
  - **Drafted, then corrected:** a cautious extractor drafts them, and `BUILDER_FIXES` holds the hand corrections (body changes, companions and mounts aren't items; Fletchwind's bow; Hexenbane's chainmail; Valor's temporary HP).
  - **Report:** `python3 -I build_prototype.py --builder-report` prints every row's final effects, for checking.
- **The cards:** each card on the official landscape sheet is cut out on its own, along its own rough border. The fillable PDF holds the sheet as one 3300×2550 image (three times the preview), and 11 cards were cut from it: Name and Background, Armor, Petty Items (with Gold), Inventory, STR, DEX, WIL, HP, Traits, Bonds (with Omens), Notes.
  - **How:** in a box round each card, the dark lines are grown a few px to close gaps in the border. The paper outside is flood-filled from the box's edge, and what it can't reach is the card. The card's face is then closed, hole-filled and grown by the border's width, so the card keeps its own edge and printed lines but none of the grass or tree beside it.
  - **Traits:** the sheet's skulls lie over its lower-left corner. The cut card has its own paper there (the rules stay), and its left and bottom edges continue from the card's own.
  - **Files:** `assets/cards/<card>.webp` (alpha, at the sheet's resolution, 10–70 KB each), `sheet.webp` (the whole sheet at 2200px, for the review), and `cards.json` (each cut's bounds). The build checks the images and inlines the bounds as `D.builder.cards`. The images are fetched beside the page on first use (about 800 KB). The per-card images aren't shown anywhere at the moment (phones' review is a list); they, `cards.json` and the build's check are kept in case the cards come back.
  - **On screen:** an `<img>` with a soft drop shadow that follows the card's edge, so it reads as a paper card. Fields sit on the fillable PDF's own rects in points, relative to the card's bounds; the multi-line fields are set on the printed rules.
- **The review:** the whole sheet (`sheet.webp`), filled in and editable in place, then printed. Petty items, Bonds, Omens and Notes are one box each, and so are Inventory and Traits: their rules are 19–24px apart, too close for an input per line (WCAG 2.5.8). Inventory keeps a line per slot, so a blank line is an empty slot; Traits are "Label: value", and their box starts where the lowest rules do, clear of the skulls. On this landscape sheet each field moves by its own `LAND_NUDGE` (2026-11-04), not portrait's CSS lifts: Name and Background drop onto their printed rules, leaving a gap under the label; STR, DEX, WIL and HP sit in one row, each centred in its box; Armor's number is centred in its ring; and every multi-line box drops 3pt so its text sits just above each rule. Each score's second box (current state, in play) is left blank, on screen and in print. Below 760px it's the list the footer's "Your sheet" panel built, in the steps' order, with each value a field: Background, Armor, Gold (gp), Inventory, Petty items, STR, DEX, WIL, HP, the eight traits, Age, Bonds, Omen, Name, then Notes (which holds the background's answers and any swap). The multi-line ones say "one per line"; Inventory doesn't wrap, so a long item can't look like two slots; Age takes digits only, as in its step. Print fills the official **Fillable Letter** sheet, Landscape or Portrait.
  - **Where they live:** `assets/sheets/landscape.pdf` and `portrait.pdf`. They're fetched beside the page on first use, not inlined, which would add about 8.5 MB.
  - **Field mapping:** Name, Background, `STR/DEX/WIL/HP_CURRENT` and `_MAX`, Armor, Gold, 10 slots, petty items (7 on Landscape, 5 on Portrait), traits, bonds, omens and Notes.
  - **Traits:** Landscape has 10 lines, "Physique: Stout" and so on, then "Age: 34". Portrait has 4 lines of pairs, the last with Virtue, Vice and Age.
  - **Inventory:** a bulky item takes two slots, and 100gp or more takes a Coins slot. Armor counts only carried armor, capped at 3. A full inventory, or items that don't fit, are flagged.
  - **Bonds and omens:** they wrap over their lines, each starting a line of its own, and the set is printed whole at the largest size from 8pt down to 6pt that fits. Only when nothing fits at 6pt does it fall back to 8pt: Portrait then uses each one's first sentence, and what doesn't fit continues in Notes. With the book's text that's 4 of the 20 omens on Portrait, and 7 bonds and 9 omens on Landscape.
  - **Notes:** filled at a fixed 7pt, one line per printed rule on Landscape, and never shrunk. What won't fit is flagged on the review card.
  - **Filling:** [pdf-lib](https://pdf-lib.js.org/) 1.17.1 (MIT), from jsDelivr, loaded on first use. The sheet stays fillable.
- **Print:**
  - The filled PDF is made as soon as the review card shows, and again after every edit, so Print only has to open it.
  - Chrome and Edge print it through an off-screen frame. Safari and Firefox open it in a tab, opened inside the click. A blocked pop-up points to **Download PDF**.
- **Kept:** the build in `localStorage` (`cairn-builder`), so a reload picks up where it was. **Start over** clears it.
- **Languages:** English only. On a translated Character Creation, the callout offers "Roll a character (in English)".
- **Licence:** the sheets and the vines are Cairn Press's; see Licensing to confirm.
- **The Tools page's Character Generator** (an iframe of kettlewright.com) stays as the complete, separate tool. The builder is the guided, read-along version.
- **Button font:** `<button class="btn">` inherits Alegreya, as link buttons do.
- **Build requirement:** PyMuPDF (`fitz`), which checks that the sheets' field names are still there.
- **In JTD:** a small include on Character Creation loads the builder script, with the sheets in `assets/`. The data could be a `_data/builder.json` written by the same extractor.

## Character sheets
The home page's section after Resources for your game offers the two Second Edition character sheets, laid out like Mörk Borg's home page. The sheets are on the right, the options on the left. Under the heading is the line about where the free PDFs come from, with a link to the two-to-a-page Letter file. Then two switches: Landscape / Portrait, and Letter / A4 / Fillable Letter. Last comes a **Download** button for the chosen file. The switches are real radio buttons drawn as segmented controls, so the arrow keys move them. Their labels ("Layout", "Paper") are there for screen readers only. The Download button's spoken name says which file it gets, for example "Download the Portrait sheet, A4 (PDF)".
- **Previews:** gallery images 8 and 9 on that page (1650×1275 and 1275×1650 PNGs), resized to 1100px JPEGs: `assets/character-sheet-landscape.jpg` and `assets/character-sheet-portrait.jpg` (about 310 KB each), inlined by the build. Shown side by side at the same height from 1100px up, under the options below that, and stacked on phones.
- **Links:** itch.io has no lasting links to single files, so the Download button opens the itch.io page. On the live site, the author could put the PDFs in `assets/` so the button downloads the chosen file, and list them as a `sheets:` front matter list on `game-files.md`.
- **Licence:** Cairn Press's art; see Licensing to confirm.

## Book design language
Accents taken from the **Cairn 2e Player's Guide** spreads (free PDF; section-opening spreads, p.4 "Section 1" and p.10 "Section 2"). They are vector line work in the PDF, so they're **redrawn** as CSS/SVG, not copied as images. Strokes are `--heading`; the rings are `--accent`.

| Element | In the book (pt) | In the prototype |
|---|---|---|
| Badge | 62.7×21.8 box with a hard offset shadow, Lora Italic 12pt | **Not used.** Kept here for reference. |
| Hairline | 0.5pt line along the badge's top edge to the right margin | 1px line |
| End circle | 4pt, 1pt stroke, just past the line's end | 6px, 1.5px stroke, left edge on the line's end |
| Rings | Ø 18.7 and 34.1 on the line, plus Ø 54.2 resting on it, clipped about 29% at the page top | Ø 25, 45 and 72px in an SVG, top 20px clipped, drawn in the brand gold (`--accent`) |

- **Where:** the Rules view's opener, and once on the home page, under the hero. The other home and Resources sections have no divider, only the same 112px gap. In the opener, the hairline has an end circle at **both** ends (like the book's footer rule), and the rings sit centred on it. The home divider is the same double-ended hairline, sitting exactly on the bottom edge of the hero. It overhangs the text column by 120px on each side from about 1400px up. Narrower than that, the end circles stop 6px from the window edge, but the overhang never drops below 44px from 760px up: where the column fills the window (about 760–1250px), the page's side margins grow from 24px to as much as 50px to make room. The header keeps its 24px margins because its nav needs the room. On phones the margin is 16px, so the overhang is 10px. It's drawn at two-thirds brightness (`opacity: .667`) so it sits quieter than the text.
- **Layout:** the rings are always centred on the text column, and the ornament is pulled up 18px. Under it, the title row is the page title on the left and the ⋯ page actions button on the right, centred on the title. Plain CSS grid and flexbox, no script.
- **Approximations:** 0.5pt strokes (about 0.67px) are rounded to 1px, and 1pt (about 1.33px) to 1.5px.
- **Diamond chain:** the book's single row of outlined diamonds (4.61×4.38pt, 0.45pt stroke, every 5.01pt; it frames the art bands) is redrawn as a repeating mask tile (6.1×5.8px diamond every 6.7px). **Used on the home page, above and below the Start slideshow**, full width and touching the art on both edges, so it frames the slideshow between Adventures and Resources. It was taken out of the Rules page on 2026-10-29. Its CSS is `.diamond-rule` in the template, and [snippets/diamond-chain.html](snippets/diamond-chain.html) previews it in the heading colour and in gold, with how to use it. `mask-repeat: round` fits whole diamonds across the column.
- **Framed cards (home, Resources for your game):** these use the frame from a reference image (2026-10-30), with the art in the middle left out. The frame is an outer stepped line with small square knots at the corners, round a panel whose corners are cut back in two steps, each with a floating square.
  - **How it's drawn:** it's redrawn as one SVG at the reference's own size (521×521, measured from the image) and applied as a 9-slice `border-image` (`86 fill / 43px`). The corners keep their exact shape at half scale, while the edges and centre stretch. The CSS is `.frame-tile` in the template.
  - **Colours:** the frame lines are in the heading white. The panel is `--surface` (#2e2d34), the same as the Character sheets section's background (the reference's is white; dark here since 2026-10-30). The title is in the heading white and the count in `--muted`. Hover and focus swap in a gold copy of the frame and underline the title.
  - **Sizes:** the cards never grow taller than they are wide. A CSS grid fits as many as it can across (up to four), each at least 220px, the narrowest square whose title and count still fit inside it ("Relics, Spellbooks & Equipment" needs 216px). So the cards step down 4 → 3 → 2 → 1, and each is capped at 300px wide and centred in its column. No script.
    - **Today's steps:** 4 across down to about 1045px, then 3 down to about 805, then 2 down to about 560, then 1.
    - **Only ever fewer columns as the window narrows:** below 760px the page's side margins drop from 50 to 24px, which makes the column suddenly 52px wider, enough for a third card to fit again just after the second, so the count would go 3, 2, 3, 2. Below 760px the minimum is therefore 246px: the 52px shared between two columns.
    - **Longer titles:** a tile whose text needs more than 220px needs a larger minimum: widen a tile in 2px steps until its title and count fit.
  - **Square or rectangle:** the cards are square. **To make them rectangles, delete the `aspect-ratio: 1` line in `.frame-tile`;** the corners stay true at any size.
  - **Where:** the Resources hub page keeps its own cards.
- **Licence:** see Licensing to confirm.

## Priorities (the IA)
1. **Read the Second Edition rules, free.** This is the hero's main button and the "Start playing in three steps" path. Every chapter is also listed on the Sitemap page, linked from the footer.
2. **Resources for your game.** One hub for monsters, adventures, tools, backgrounds, gear, hirelings, hacks and translations. Adventures sits right under the hero: four first-party modules and a way into the whole catalog.
3. **News and community.** The licence is up front, with submissions, Discord and third-party content. The GitHub link has real text instead of "here".
4. **Other editions.** First Edition and Barebones are kept, but demoted: since 2026-10-30 they're a **First Edition** tile on the Resources page (with the SRD, FAQs, tools and Barebones) and in the header's Resources menu, not a home section. The footer's link reads First Edition.
5. **Support the author.** Press, store, newsletter and app are in the header's Store menu. The home page's Support band was removed on 2026-10-30.
- **Header menus:** each top-level link lists what's inside it, so any page is two steps from anywhere. Hovering a link opens its list, and so does keyboard focus on the link, so Tab walks into it. There are no arrow buttons (removed 2026-10-30), so on touch screens a tap simply follows the link. Escape closes the list (a hovered one until the pointer leaves the nav, a focused one until focus leaves that item) and puts focus back on the link. Tabbing out or following a link closes it. The top links still go to their own pages. Contents: **Rules** (Core Rules, Player's Guide, Warden's Guide, Backgrounds, PDFs and game files, Print copies, First Edition), **Resources** (All resources plus the hub's nine tiles), **Adventures** (all, First Party, Originals, Conversions, each opening the catalog, and Submit an adventure), **Community** (News and community, Discord, Submission guide, Third-party content, Translations), **Store** (Books, Cairn Press, Merch, Newsletter, Official App). Below 960px the Menu drawer keeps its plain list. The header search shrinks from 300px to 160px, so the header fits on one row from 960px up.

## Views
- **Home:** the hero, then Adventures, the Start slideshow, Resources, Character sheets, and News and community (the newest New School Revolution post beside Share your work), which closes the page.
  - **News and community:** it has the page background (changed on 2026-10-30). Only Character sheets keeps `--surface`.
- **Footer** (2026-10-30):
  - **Width:** it isn't held to the body's 1200px column. It runs up to 1560px, with 16-56px side margins.
  - **Sitemap link:** a **Sitemap** link sits in its first column, beside the GitHub source link.
- **Sitemap** (`#sitemap`, 2026-10-30):
  - **What it is:** a page of its own, holding the Second Edition rules cards that used to be on the home page, laid out as they were:
    - Player's Guide;
    - the Warden's Guide by its three parts, with its intro line;
    - Backgrounds in columns, with the proposed summary.
  - **Links:** the PDFs and Print copies links sit beside its heading, and every chapter link opens the page in the prototype.
  - **In JTD:** a `sitemap.md` page whose layout loops over `site.pages` by `parent`, linked from the footer include.
- **Rules:** one Second Edition page at a time; see Second Edition pages.
- **Resources:** the hub, plus all 145 monsters; see Monsters.

## Monsters
All 145 monsters with their real stat lines, on the Resources page, laid out as one bestiary sheet: no cards, just one continuous stream of stat blocks 48px apart.
- **Column:** up to 38rem wide and centred on the page, as the sheet centres its column; an empty column on the right balances the rail. On phones it sits beside the rail, as in the sheet. Text is sized up from the card version: names 1.6rem, stat numbers 1.5rem, labels .95rem, attack and abilities 1.15rem.
- **Filter:** in the same column, centred and as wide as the stat blocks, with the monster count under it.
- **Letter rail:** on the left, starting level with the filter.
  - Letters are 1.45rem in 34px rows (1.2rem / 30px on phones), in a 56px rail balanced by an empty 56px column on the right. On phones the rail is 32px with a 16px gap.
  - It sticks just under the header (the header's height is measured, since it changes with the window). At 21 letters it's about 830px tall, so on windows shorter than about 950px it scrolls itself and keeps the current letter in view.
  - It lists only the letters that have monsters (Q, X and Y have none), and greys out letters the filter empties.
- **Current letter:** a scroll spy highlights the last letter group whose top has reached just under the header. A letter clicked on the rail is highlighted at once and stays highlighted while the page glides there. At the very bottom of the page, where the last letters can't reach that line, the clicked letter stays current until you scroll up. Clicking the letter already in the address still jumps.
- **Headings:** the letter groups have no visible headings (each is a section labelled with its letter); monster names are H3s.
- **Stat blocks:** in this order: name, the stat line, the attack (with any _detachment_), then the abilities as bullets. The three creatures with no attack (Boggart, Pixie, Will-o'-wisp) skip that row.
  - **The stat line:** HP, Armor, STR, DEX, WIL, each written number first, "10 HP · 1 Armor · 11 STR", as Cairn's own style guide has it. The number is larger (1.7rem) and Medium weight in Alegreya's equal-width digits (`tabular-nums`; the Medium file is the only extra font, 92 KB), centred vertically on the small label that follows it (the label is set 2px lower, to sit on the number's middle, and 2px further from it), with a dot between stats. It wraps on narrow widths, and a stat that starts a new line drops its dot, as in the sheet. That's CSS only: each dot sits in a 20px slot at the start of its stat, and the line is pulled 20px left with that strip clipped, so a dot at the start of a line is cut off. The die in the attack rolls (see Dice roller), and the search reads the attack's words, not its markup.
  - **Parsing:** the build splits the source's single stat line at the first part that isn't a number and a stat, and stops if a line doesn't fit.
  - **Rule:** under the name only (no rules before the attack or the abilities), sits the book's hairline with an end circle at both ends (`.book-rule.both`), at two-thirds brightness like the home divider.

## Where every line of copy comes from
Nothing is typed into the layout except section headings, button labels and short status labels, which would live in `_layouts/home.html`.

| Copy | Source |
|---|---|
| Hero paragraph | `README.md`, first paragraph (2 sentences) |
| Adventures (cards, catalog, detail views) | Scrivener's posts, merged with the Cairn repo's `adventures/` pages, at build time (see Adventures) |
| From the blog | The newest entry in newschoolrevolution.com's Atom feed, saved from a browser and passed with `--blog-feed` (see News and community) |
| Second Edition pages | Each page's own Markdown, in full (see Second Edition pages) |
| Translations | Each translation's own repo, page by page, at build time (see Translations). Credit lines name the repo owner. |
| Adventure pages (CAS-1 to CAS-4, 62 conversions) | Each page's own Markdown, in full; images hotlinked from cairnrpg.com |
| CAS-4 map alt text | Proposed (`MODULE_IMAGE_ALT`, or "Map: <section>"); the source's map blocks have none |
| Example map alt text | **Proposed** (`IMAGE_ALT`); the source's is "Click Me!" |
| "Share your work" line (News and community) | `README.md`, the licence sentence and "This is a game that values community contributions!" |
| Start step titles | Each page's own `title` |
| Start step "Covers" lines | That page's own `##` headings |
| Warden's Guide card line | `second-edition/wardens-guide.md`, first sentence |
| Warden's Guide parts | `wardens-guide.md`'s own table of contents |
| Chapter, tool and link names | Page titles from front matter (or the first `#` heading) |
| Counts | Counted from the repo files every build: monsters, adventures, chapters, backgrounds, hacks (12), languages (19) |
| **Proposed summaries** (drafts) | No source exists yet. Each is the `summary:` line the author would add to that page. They're listed below and in `PROPOSED` in `build_prototype.py` |

Proposed `summary:` lines:

| Page | Proposed summary |
|---|---|
| `resources.md` | Monsters, adventures, tools and tables for running Cairn. |
| `resources/monsters.md` | Stat blocks for monsters and NPCs, ready to drop into an adventure. |
| `adventures.md` | First-party adventures, community originals, and conversions from other games. |
| `second-edition/backgrounds.md` | Each background comes with its own tables for names, gear and details. |
| `hacks.md` | Optional rules and variants: classes, loadouts, funnels, magic and more. |
| `localization.md` | Cairn translated by the community, with a guide to adding your own. |

Resource tiles that group several pages (Tools; Relics, Spellbooks & Equipment; Hirelings & Pregens; Guides for Wardens) have no blurb, only the page titles.

## How the author keeps managing it (Just the Docs 0.12.0)
| Prototype element | In Just the Docs |
|---|---|
| Nav, breadcrumb, order | Unchanged `parent` / `nav_order` / `has_children`. JTD's breadcrumb moves to the top of the page and shows only from 760 to 1099px, when the sidebar is hidden. |
| Header links | `_includes/header_custom.html`: Rules, Resources, Adventures, Community and Store, then search, then the Dice pill at the far right. Each link's menu is a nested `<ul>` in the same include, opened by CSS `:hover` / `:focus-within`, plus a few lines of JS for Escape |
| Home | `README.md` (permalink `/`) switches to a `home` layout. Card text comes from an optional `summary:` front matter key. |
| Scoped section nav | A custom nav include that shows only the current top-level section |
| "On this page" | A custom include. 0.12.0 has none, so `jekyll-jtd-toc-nav` is turned off. |
| Previous / next, edit link | A custom include, plus `gh_edit_link` |
| Theme | `_sass/color_schemes/cairn.scss` + `_sass/custom/custom.scss`. Dark only, as today. |
| Monsters index | Loop over `site.pages` where `parent == "Monsters"`. URLs don't change. `generate_monster_json.rb` could feed it. |
| Search | Unchanged, except the shortcut: Ctrl+K (⌘K on a Mac) instead of a bare `k`, which can't be turned off and so fails WCAG 2.1.4 |
| Second Edition pages | Nothing new: JTD already renders these pages from the same Markdown. The parity check confirms the prototype matches it. |
| Adventures catalog | The same build step writes `_data/adventures.json` from Scrivener's posts and the `adventures/` pages, and an `adventures` layout (catalog) and a home include (the row) loop over it, with a few lines of JS for the filters. Or keep Scrivener as its own site and link to it. The module and conversion pages need nothing new: JTD already renders them. |
| Translations | A scheduled GitHub Action copies each translation's pages into `_l10n/<code>/` (or a `_data` file), using the same discovery, and a small include renders the flag switcher on every page. The simpler alternative: link out to each language site. |

**Still to check when building:** how a custom home layout fits into JTD 0.12.0's `default` layout.

## Licensing to confirm
The game's text is CC-BY-SA, and the author has given permission to fork the repo. Neither covers Cairn Press's art or the book's design. Before going live, confirm each of these with the author (or the book's designer), and credit the artist by name:
- **Hero art:** the Player's Guide cover. The itch.io page doesn't name the artist.
- **Start step art:** the Player's Guide section-opener silhouettes, cut and animated.
- **Character sheets:** the previews, the cards the builder cuts from the fillable sheet, the portrait sheet image its walkthrough fills in, and the PDFs it fills are Cairn Press's free downloads.
- **Vines:** the Warden's Guide p.31 pattern, as the builder walkthrough's background. The guide's credits put its text under CC BY-SA 4.0, not its art.
- **Book design language:** the accents, the diamond chain and the framed cards are simple geometric shapes, redrawn, but they're the book's design.
- **Translations:** CC-BY-SA, checked on every refresh; see Translations.

## Proposals to confirm with the author
- **Tools:** gather every edition's tools under Resources. Build an Adventure Site and Converting Monsters exist only in First Edition today.
- **Navigation:** move First Edition, Barebones and the support links down the nav order.
- **Blog feed:** to show the newest New School Revolution post automatically, a GitHub Action could read the feed at build time and write `_data/blog.json` for the home layout, or the home page could fetch it live if the blog sent CORS headers. Either way the blog's Cloudflare settings would have to let the request through; today scripts get a 404.

## Checks run
- **`python3 check.py`** (run before sharing): fails on pyflakes errors, a JS syntax error (`node --check`), a failed build (including the `EXPECT` counts) and raw Markdown left in an English page. It reports, without failing: pylint's unused-name, shadowing and size warnings; JS names and CSS selectors nothing uses, and ids looked up but never written (leads only, since some class names are built at runtime); colour literals outside `:root`; `!important`; identical CSS rules; comment density; the longest functions; repeated statements; and raw Markdown that comes from upstream typos or translations. It builds to a temporary file, so `prototype.html` is left alone.
- **Widths:** 1440, 1280, 1024, 768, 375 and 320. No horizontal scroll in any view. The home tiles go 4 → 3 → 2 → 1 across. The rules view goes 3 → 2 → 1 columns: below 1100px the Second Edition nav is hidden (the breadcrumbs at the top down to 760px, previous/next and, below 960px, the Menu drawer still reach it) and "On this page" stays as a right-hand column; it becomes a button under the title only on phones (below 760px), where the breadcrumbs go. The header Menu drawer appears below 960px.
- **Pre-PR pass (2026-11-02):** with the builder out, the full review ran again: code, design and accessibility. No critical or serious findings.
  - **Fixed:** heading levels under an H3 that opens a page; the TOC and section nav following the measured header; placeholder contrast; the dice pool, results and the slideshow's arrows as status messages; the catalog count waiting until typing pauses; the monster letter groups no longer being 26 landmarks.
  - **axe-core 4.10.2:** 0 violations on 12 routes at 1440 and 320.
- **Accessibility** (target WCAG 2.2 AA; full pass 2026-11-01):
  - **What was checked:** 12 routes: Home, Core Rules in English and in Brazilian Portuguese, Monsters, Adventures, an adventure detail, CAS-1, a conversion, Resources, Character Creation, the sitemap and the Player's Guide.
  - **axe-core 4.10.2** (WCAG 2.0 to 2.2 A and AA rules): **0 violations** on every route at 1440 and 320px.
  - **In the browser:**
    - A real Tab pass found no keyboard trap, and every focused control showed a ring.
    - Each route moves focus to its `h1`, or to the heading of the section it targets.
    - Escape closes each menu and the drawer and returns focus to the button that opened it.
    - No focused link sits under the sticky header (`scroll-padding-top` follows its measured height).
    - No target is under 24×24 px, and nothing scrolls sideways at 320px.
    - The WCAG text-spacing overrides clip nothing.
    - Motion waits for `prefers-reduced-motion: no-preference`.
  - **Structure:**
    - One `h1` per view.
    - Every control has a name.
    - Tables have `th`.
    - Forced-colours mode keeps every selected or current state visible: segments, Swap, the current nav link, the A–Z letter and the active TOC entry.
  - **Known gaps:**
    - Three adventure pages skip a heading level, from their source Markdown.
    - Some conversion pages have "here" links in the authors' text.
    - Module images whose own alt text reads like a file name get a proposed description in `MODULE_IMAGE_ALT` (seven in CAS-1 to CAS-3 so far), and the build warns about any new one.
- **Contrast:** all colour-token pairs pass. The lowest is 4.05:1 for input borders (3:1 needed); text is 5.5:1 or higher.
- **Second Edition pages (2026-10-30):**
  - All 56 pages open, by script, with the right title, crumbs, previous/next and current sidebar entry.
  - Every `#` link lands, and no raw Markdown or entity is left: no `####`, heading-shaped `# `, `>`, `{:`, `**`, `](` or `&name;`.
  - The ids are unique across the site (680).
  - The parity check matches the live site; see Second Edition pages.
  - Deep links like `#procedures-wilderness-exploration` and `#setting-seeds-factions` open the page and focus the heading.
  - Search finds every page and heading, and labels the two Valds by guide.
  - The home page, Resources and the footer have no links left to Second Edition pages on cairnrpg.com, except the Character Generator.
  - axe-core 4.10.2 found 0 violations on all 56 pages in English, and on a sample in every language: fi, ru, zh, he, tr and pt-BR.
- **Character builder (2026-10-30):**
  - **The sheets:** read back with pdf-lib, every field holds its value. pdf.js renders of both sheets show nothing spilling over, and Landscape's Notes sits on its printed lines.
  - **Special cases:** Fieldwarden (Bonus HP and Bond 2), Foundling and Mountebank 3 (forced omen), Kettlewright 2 (Marketplace note, no gold), Hexenbane 3 (chainmail, Armor 2, 2 slots), Greenwise 5 (110 gold and a Coins slot), Fletchwind (bow from table 2). Undoing a bonus step drops it.
  - **The rest:**
    - Edits refill the sheet and rename the download. A reload resumes, and Start over clears.
    - The translated callout works.
    - No console errors.
  - **Not confirmed:** the browser's own print dialog. Print was checked only as far as loading the filled sheet into the print frame.
- **Character builder, walkthrough (2026-11-01):**
  - **The run:** with the real 3D dice, at 1440, 375 and 320 (and, before the controls moved into the drawer, 812×375). Start opens the walkthrough, the drawer slides in and the vines grow in from the edges. The masks were sampled every 550ms, growing from 0 to 150% over about 3.5s. They don't replay on a roll or a reload.
  - **Each step:** it shows only its own section, focus is on its heading, and the drawer has the right cards with the controls at its foot (desktop). On phones the controls stay fixed at the bottom while the step scrolls. Above them a "Your sheet" row shows how many steps are filled and the newest result; tapping it expands the footer into the sheet so far as plain text, full screen, until its header row or Escape collapses it (focus goes back to the row). It's hidden at the review, which shows the same list with every value editable. Traits rolls 8 dice once. The dice show above the walkthrough, fade once the rolled row lights up, and are gone on Next.
  - **Closing and resuming:** ✕ closes it, keeps the progress, rebuilds the page, moves the dice back and focuses Continue. Continue reopens on the same step. A link in a step closes the walkthrough and follows.
  - **The review:** the sharp full sheet at 1440; at 375, the editable list. Edits (name, an inventory line, a trait, age, bonds, omen) survive a reload, a non-number age is refused, and an edited name, slot and trait reach the filled PDF, read back with pdf-lib.
  - **axe-core 4.10.2:** 0 violations on a step and on the review at 1440 and 320.
  - **Reflow and errors:** no sideways scroll at 320, and no console errors.
  - **Not checked:** a screen reader; Escape itself (the `close` path was run through ✕); reduced motion and forced colours (not emulated); the print dialog itself.
- **Character builder, step cards and climbing vines (2026-11-01):**
  - **Every step at 1440 and 320,** after a full run with the real dice: the right header, rows or value box, and the roll marked. Traits marks all 8, and focus is on the card's title.
  - **Tables:** all 20 backgrounds' tables render without leftover Markdown or links. That includes Outrider's horses, Prowler's mixed rows, and Kettlewright's and Rill Runner's linked cells.
  - **Vines:** with the growth paused and stepped through, the six columns reach the top between 4 and 4.5s and are fully grown at 5s.
  - **axe-core 4.10.2:** 0 violations on Background, STR, Traits, a background table and the review, at 1024–1440 and 320. No duplicate ids, and no sideways scroll at 320.
  - **Not checked:** the growth as it plays in real time (it was stepped through by hand); a screen reader; reduced motion.
- **Footer and Sitemap (2026-10-30):**
  - At 1440px the footer runs 1425px wide against the body's 1200px.
  - The footer's Sitemap link opens `#sitemap`, which retitles the tab and focuses the page's heading. Its 52 chapter links open pages in the prototype, and Back returns to it.
  - There's no horizontal scroll at 1440, 1024, 768 or 375px. This also fixed an older 94px overflow at tablet width: the Start slideshow's minimum height was widening it, and it now has an explicit width.
  - axe-core finds 0 violations on Home, the Sitemap and the footer.
- **IA notes removed (2026-10-30):** no `.ia-note`, source tag or toggle is left. The home page, Resources and Core Rules still show all their copy, the console is clean, and axe-core finds 0 violations on Home and Core Rules.
- **Translations (2026-10-30):**
  - All seven languages have their own section counts (pt-BR 11 H2, the rest 12). Each sets `lang` on the text and title, Hebrew sets `dir="rtl"`, and the page title, credit and ⋯ links follow the language.
  - Every in-page link lands, and no link is anything but `#`, `http(s)` or `mailto`. No raw Markdown is left.
  - axe-core 4.10.2 found 0 violations in each language with the menu open.
  - A reload keeps the choice. Links into another language switch to it without changing the saved one.
  - Search follows the language.
  - The flag menu opens on the current language. The arrows move, Enter picks and returns focus to the flag, and Escape or a click outside closes it. ⋯ uses the same code and still works.
  - At 375px the title row fits, with no overflow.
  - With downloads failing, the previous list is kept. With no cache, the build ships English only.
  - **Auto-discovery:** the test ran on a scratch copy of the checkout, with `--src`, `--cache` and `--out` pointing at it, so the real cache was untouched.
    - With `finnish.md` removed, `kari/cairn-fi` was picked up on its own as an unofficial fork.
    - With the stub back, it became official, reported as "changed".
  - The parser checks: `javascript:` links are dropped, sections that no longer line up are refused, and there are no empty table headers.
- **Adventures (2026-10-31):**
  - **Build:** all 80 Scrivener posts parse, including the four with no closing `---`. The match report prints from the build.
  - **Parity:** all 122 pages (56 Second Edition, 66 adventures) match cairnrpg.com, except Core Rules' rebuilt Reactions table, as before. Every translation still matches its site.
  - **Catalog:** search, Group, Author, Price, Tags and Sort each narrow or order the list, and together. A reload, Back and a shared link keep the filters.
  - **Detail and pages:** an Original's card goes straight to its itch.io page. CAS-1 to CAS-4 and a conversion open with their sidebar, drawer, crumbs and previous/next, and the header marks Adventures. All of CAS-1's images load, including the two with spaces in their names. CAS-4's maps render with alt text, CAS-1's table-cell breaks render, and what-child-is-this's map key is a `<pre>`. No raw `{::`, `<img` or heading marks are left.
  - **Links:** the header, its menu, Resources' tile (149 adventures, with the group counts), the footer and search reach the catalog. `#res-adventures` still lands on the Resources tile. Search finds adventures by name and by author.
  - **Home:** no podcast markup, script or CSS is left, and the build makes no Apple request. The four cards show their covers.
  - **Quality:** axe-core 4.10.2 finds 0 violations on Home, the catalog (filtered and not), two detail views, CAS-4, a conversion and Resources. No horizontal scroll at 1440, 768 and 375px. The only console errors are the dead cover's 403s.
- **Keyboard:**
  - Skip link; `k` focuses search, and the arrow keys move through results.
  - The Menu drawer opens with focus on its first link, and Escape closes it and returns focus to Menu.
  - The "On this page" toggle exposes `aria-expanded`.
  - Changing view moves focus to the view's `h1` and updates the page title.
