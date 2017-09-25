# Chapter 1: Creating The App

* Install the GenericApp gem by entering the command "gem install generic_app".
* Enter the following commands:
```
DATE=`date +%Y%m%d-%H%M%S-%3N`
APP_NAME=omni-$DATE
echo $APP_NAME
```
* Enter the command "generic_app".  When prompted for the name of your new app's directory, copy and paste the output of the above echo command.  When prompted for the name of your new app, enter "Temporary OmniAuth App".
* Follow the instructions in the README-to_do.txt file.  If you want to save time, you can skip the section about converting from SQLite to PostgreSQL in the development environment.  After you have completed these tasks, you are ready to move on.
