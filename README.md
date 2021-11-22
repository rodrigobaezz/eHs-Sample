## Getting Started

- Please run everything while using node v8.9.4 `nvm install 8.9.4`
- In the project directory, install dependences with `npm install`
- Setup your [shopify dev store](https://partners.shopify.com/489730/stores)
- [Generate API credentials](https://help.shopify.com/api/getting-started/api-credentials#get-credentials-through-the-shopify-admin) for your local environment
- Create `config.yml`  in the project directory and add your store information and private app credentials:
  - **store:** the Shopify-specific URL for this store/environment (ie. my-store.myshopify.com)
  - **theme_id:** the unique id for the theme you want to write to when deploying to this store. You can find this information in the URL of the theme's online editor at Shopify [admin/themes](https://shopify.com/admin/themes).
  - **password:** the password generated via a private app on this store.  Access this information on your Shopify [admin/apps/private](https://shopify.com/admin/apps/private) page.


### [Slate Commands](https://shopify.github.io/slate/docs/0.14.0/commands/)

```bash
slate start [-e][-m] # Runs build, deploy, then watcher
slate watch [-e][-n] # Runs watcher, then deploy
slate deploy [-e][-m] # Builds `dist` folder and replaces the theme set in config.yml
slate build # Creates a production-ready `dist` bundle
slate zip # Creates a zip file for manually uploading your theme
slate -h # Help
```

### NPM Scripts

```bash
npm run start # Installs Slate globally and locally to start working on any project.
npm run hooks # Installs a Git Hook that prevents branch changes without stopping the watcher (sets a touch -a to the config.yml to force the watcher stop)
npm run start-dev # Runs the `gulp start` task which runs all gulp tasks and then starts a watcher
npm run build # Runs the `gulp build` task which compiles scss and javascript in production mode (minification enabled)
```

### Starting Development

Development requires 2 terminal processes, one for slate and one for gulp.  To get started:

- Open up one terminal window and run `slate watch`.
- Next, open up another window and `npm run start-dev`, this will run the scripts and styles task as well as start the non-slate watcher on files in these directories.
- To make sure things are working correctly, edit a SCSS file, the gulp task should run and then the slate watcher should upload the file immediately after.

##### Gulp Tasks

All gulp tasks can be found in the `gulp/tasks` directory.  You can run them independently at any time with `gulp {{ task_name }}`.

All task configuration can be found in `gulp/config.js`.  This config defines the various tasks and the filepatterns, input and output directories associated with each of them.

**Note:** For the `scripts` and `styles` tasks, there are multiple entry points as we need a few different files for different parts of the site (or different layouts).  Inside the config you'll notice that some of the files / bundles entries have been commented out, leaving only one enabled at a time, this is a fix to make the build more efficient.  By watching one file at a time, Slate will only upload one file at a time on change.  For instance, if we leave all main entry point JS files (checkout, theme, vendor) un-commented, then Slate will re-upload 3 files everytime anything in the `_scripts` directory changes.  Typically we only need to work on one of these files at a time (i.e. theme and checkout are used mutually exclusively) so commenting out is not a problem.

##### Underscored Directories

This project uses Slate behind the scenes to handle file changes and uploads.  It comes with a watcher that watches over files inside of two specific source directories titles `styles` and `scripts`.  This watcher runs a task every time files are changed that re-compiles any top-level files and moves them into the `dist` directory for uploading.  This works well except for the fact that it compiles and uploads _all_ of those top level files each time anything changes.  For example, because we have 4 JS files, this means we have to wait for 4 files to upload everytime we make a change to one.  To get around this, we put our styles and scripts in directories with _underscore prefixes_ and hook them up to our own gulp watch task that compiles and outputs them directly to the `dist/assets` directory (which uploads single files on change).


