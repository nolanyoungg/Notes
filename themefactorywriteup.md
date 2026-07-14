1. Choose a template from wordpress-template-themes/
  1.a) script runs that takes that zip, copies it, paste it in wp-content/themes/
  1.b) script runs and un-zips that coppied theme
  1.c) script runs that runs "npm ci" in the copied themes build folder
  1.d) script runs that runs "npm run dev" - this watches the file,
       so as the theme files and scss files are built, th emodel can see
       bundle.css being updated, as well as bundle.js
2. this step, and only this step,
    we call codex and Run AI generation only inside that prepared folder only.
   codexs only function in this repo is to edit the selected theme that gets copied into wp-content/themes/
   that is it, it should not have to set anything up as npm run dev is already running, it just needs to flourish the theme. 
5. Run practical WordPress quality checks
6. Generate/update the static preview, this will be a static preview that
   gets created from the wordpress theme, it shoudl live in : docs/preview-themes
 
7. Package the ZIP
8. Rebuild the preview gallery



One-pass generation contract

For a full theme:run:

Source ZIP preflight rejects traversal, duplicate paths, symlinks, encrypted entries, archive bombs, and unsafe file types.
The source archive is copied to run staging and extracted safely.
A valid single WordPress theme root is discovered and copied into wp-content/themes/<slug>/.
npm ci executes exactly once in that prepared theme.
Codex executes exactly once with the requested model and reasoning setting.
Filesystem and Git snapshots prove the theme boundary was respected.
The theme-local npm run build executes deterministically.
PHP/JavaScript syntax, duplicate PHP declarations, metadata, package-content, and build-artifact checks run.
A clean installable ZIP is assembled and read back for verification.
There is no automatic retry, repair pass, or fallback model. If a generation fails any gate, the tool does not package it as successful.
