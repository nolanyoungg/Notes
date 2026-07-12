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
