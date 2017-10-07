# Chapter 3: OmniAuth Environment Variables Script

In this chapter, you will provide the environment variables needed for OmniAuth capabilities.

## New Branch
Enter the command "git checkout -b omniauth_env_script".

## .gitignore
* Enter the command "touch .env".  This is where you will later add the environment variables needed for OmniAuth services.
* Enter the command "git status".  You'll see that .env is an untracked file.
* Add the following lines to the end of the .gitignore file:
```

# Keep OmniAuth credentials out of the source code
.env
.env*
```
* Enter the command "git status".  You'll see that .gitignore has changed, but .env is no longer mentioned.

## dot_env.txt
Create the file dot_env.txt and give it the following content:
```
FACEBOOK_APP_ID='INSERT_FACEBOOK_APP_ID'
FACEBOOK_APP_SECRET='INSERT_FACEBOOK_APP_SECRET'
GOOGLE_APP_ID='INSERT_GOOGLE_APP_ID'
GOOGLE_APP_SECRET='INSERT_GOOGLE_APP_SECRET'
```

## Gemfile
* Enter the following line to the Gemfile:
```
gem 'dotenv-rails' # Needed to export ID and secret values into environment variables
```
* Enter the command "bundle install".
* Enter the command "^dotenv-rails$".
* Pin the version number of the dotenv-rails gem in your Gemfile.
* Enter the command "bundle install".

## config_env.sh
* Create the file config_env.sh and give it the following content:
```
#!/bin/bash

echo 'Enter the Facebook App ID: '
read FACEBOOK_ID

echo 'Enter the Facebook App Secret: '
read FACEBOOK_SECRET

echo 'Enter the Google App ID: '
read GOOGLE_ID

echo 'Enter the Google App Secret: '
read GOOGLE_SECRET

cp dot_env.txt .env
sed -i.bak "s|INSERT_FACEBOOK_APP_ID|$FACEBOOK_ID|g" .env
sed -i.bak "s|INSERT_FACEBOOK_APP_SECRET|$FACEBOOK_SECRET|g" .env
sed -i.bak "s|INSERT_GOOGLE_APP_ID|$GOOGLE_ID|g" .env
sed -i.bak "s|INSERT_GOOGLE_APP_SECRET|$GOOGLE_SECRET|g" .env
```
* Enter the command "sh config_env.sh".  Enter your app's credentials when requested.
* Restart the local Rails server.
* You should still be able to login to your local app through Facebook and through Google.

## build_fast.sh
In the build_fast.sh script, add the following line to the beginning (just after "#!/bin/bash"):
```
sh config_env.sh
```

## Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added environment variables script for omniauth"
git push origin omniauth_env_script
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
* Enter the command "heroku run sh config_env.sh".  Enter the OmniAuth credentials when requested.
* You should now be able to login to your app on Heroku with Facebook and Google.