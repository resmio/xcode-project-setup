# xcode-project-setup
These are some nifty scripts to setup a new Xcode project with rvm, ruby, Cocoapods and useful git hooks.

Normally, you would use them like this:

1. Create an Xcode-project
2. Download this repo as a zip
3. Unzip it into the base-folder of your Xcode-project  
4. If you already initialized a git repo in that folder, commit or stash your changes
5. Run `./setup <basename-of-your-xcode-project>`  
This will do the following things:
  - It saves the given project-basename into a file named `.projectname`  
*(The git-hooks as well as bundler will use this file, so if you change the name of your project (which I most certainly do 
not recommend because it spawns millions of strange Xcode-errors), you should run `./setup <basename-of-your-xcode-project>` 
again with the new name of your project, it will ask you if you want to change the projectname.)*
  - It checks if rvm is installed, if not, it installs the latest stable version
  - It checks if rvm uses the ruby version given in .ruby-version, if not, it tells rvm to use that version
*(Therefore, if you want to use a different version, adapt the version  
in .ruby-version to your requirements before running `./setup`)*
  - It runs `gem install bundler`, which installs bundler (d'uh...)
  - It runs `bundle install`, which will install Cocoapods and synx (synx is used in the git hooks)  
*(At the moment, it also installs a specific version of the xcodeproject gem,  
to work around a bug in the version installed alongside cocoapods)*
  - It runs `pod init`, which initializes cocoapods with an empty podfile. If a Podfile already exists, this will noop
  - It runs `pod install`, which installs the Pods specified in the Podfile (thus none if there wasn't a pre-existing Podfile) and creates an xcworkspace, which you must open instead of the xcodeproj from then on
  - It creates a `pre-push` and a `post-merge` hook in .git/hooks/  
  *(look into the pre-push and post-merge files to see what the hooks are doing)*
  - It runs `git init` which will also noop if a git repo was already initialized
  - It runs `git add .` and `git commit` to commit all the changes by setup


Running `./setup` multiple times should not break anything but it's not recommended because it takes some time.  
After you ran it one time, it should suffice to run `pod install` when you changed the requirements in the Podfile.  
