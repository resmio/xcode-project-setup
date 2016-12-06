# xcode-project-setup
These are some nifty scripts to setup an Xcode project with rvm, ruby, Cocoapods and some useful git hooks.

Normally, you would use them like this:

1. Create an Xcode-project (if you don't already have one)
2. Download this repo as a zip
3. Move the contents of the xcode-project-setup folder into the base-folder of your Xcode-project  
4. If you already initialized a git repo in that folder, commit or stash your changes
5. Run `./setup <basename-of-your-xcode-project>`  
This will do the following things:
  - It saves the given project-basename into a file named `.projectname`  
*(The git-hooks as well as bundler will use this file, so if you change the name of your project (which I most certainly do 
not recommend because it spawns millions of strange Xcode-errors), you should run `./setup <basename-of-your-xcode-project>` 
again with the new name of your project, it will ask you if you want to change the projectname.)*
  - It checks if rvm is installed, if not, it installs the latest stable version
  - It checks if rvm uses the ruby version given in .ruby-version, if not, it tells rvm to use that version  
*(Therefore, if you want to use a different version, adapt the version in .ruby-version to your requirements before running `./setup`)*
  - It runs `gem install bundler`, which installs bundler (d'uh...)
  - It runs `bundle install`, which will install Cocoapods and synx (synx is used in the git hooks)  
*(At the moment, it also installs a specific version of the xcodeproject gem,  
to work around a bug in the version installed alongside cocoapods)*
  - It runs `pod init`, which initializes cocoapods with an empty Podfile. If a Podfile already exists, this will noop
  - It runs `pod install`, which installs the Pods specified in the Podfile (thus none if there wasn't a pre-existing Podfile) and creates an xcworkspace, which you must open instead of the xcodeproj from then on
  - It creates a `pre-push` and a `post-merge` hook in .git/hooks/  
  *(look into the pre-push and post-merge files to see what the hooks are doing)*
  - It runs `git init` which will also noop if a git repo was already initialized
  - It runs `git add .` and `git commit` to commit all the changes by setup


Running `./setup` multiple times should not break anything but it's not recommended because it takes some time.  
After you ran it one time, it should suffice to run `pod install` when you changed the requirements in the Podfile.  


### What do the git hooks do?

The `pre-push` and the `post-merge` hook essentially do the same. They run:  
- [`synx`](https://github.com/venmo/synx), a gem which synchronises the actual folder-structure of your xcode-project source-folder with the group-structure of your projectfile.
- [`xUnique`](https://github.com/truebit/xUnique), a python script which rewrites the uuids in the projectfile to be deterministic, and sorts the file and group entries in the projectfile alphabetically to make resolving merge-conflicts in the projectfile way easier.

When a hook is run: 
1. All uncommitted changes are stashed
2. `synx` and `xUnique` are run
3. All changes made by `synx` and `xUnique` are committed

The pre-push hook exits non-zero if changes were made and committed by it, so you have to `git push` again.
This is best practice because the hook made and committed changes which you should be able to review before pushing.

You can also run the scripts manually by running `./run_synx_and_xUnique`.  
This is necessary whenever you added/moved/removed files/groups/run-scripts or similar in your xcode-project.

### What else is included?

Apart from the setup-script and the git-hooks, this repo includes:  
- a default Xcode .gitignore (the version which **ignores** the Pods folder)
- a .gitattributes file which states a `union` merge policy for the projectfile

