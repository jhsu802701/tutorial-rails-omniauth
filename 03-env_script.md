# Chapter 3: OmniAuth Environment Variables Script

In this chapter, you will provide the environment variables needed for OmniAuth capabilities.

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

## build_fast.sh
In the build_fast.sh script, add the following line to the beginning (just after "#!/bin/bash"):
```
sh config_env.sh
```

## dot_env.txt
Create the file dot_env.txt and give it the following content:
```
FACEBOOK_APP_ID='INSERT_FACEBOOK_APP_ID'
FACEBOOK_APP_SECRET='INSERT_FACEBOOK_APP_SECRET'
GITHUB_APP_ID='INSERT_GITHUB_APP_ID'
GITHUB_APP_SECRET='INSERT_GITHUB_APP_SECRET'
GOOGLE_APP_ID='INSERT_GOOGLE_APP_ID'
GOOGLE_APP_SECRET='INSERT_GOOGLE_APP_SECRET'
```

## Gemfile
* Enter the following line to the Gemfile:
```
gem 'dotenv-rails' # Needed to export ID and secret values into environment variables
```
* Enter the command "bundle install".
* Enter the command "gem list ^dotenv-rails$".
* Pin the version number of the dotenv-rails gem in your Gemfile.
* Enter the command "bundle install".

## config_env.sh
Create the file config_env.sh and give it the following content:
```
#!/bin/bash

echo 'Enter the Facebook App ID: '
read FACEBOOK_ID

echo 'Enter the Facebook App Secret: '
read FACEBOOK_SECRET

echo 'Enter the GitHub App ID: '
read GITHUB_ID

echo 'Enter the GitHub App Secret: '
read GITHUB_SECRET

echo 'Enter the Google App ID: '
read GOOGLE_ID

echo 'Enter the Google App Secret: '
read GOOGLE_SECRET

cp dot_env.txt .env
sed -i.bak "s|INSERT_FACEBOOK_APP_ID|$FACEBOOK_ID|g" .env
sed -i.bak "s|INSERT_FACEBOOK_APP_SECRET|$FACEBOOK_SECRET|g" .env
sed -i.bak "s|INSERT_GITHUB_APP_ID|$GITHUB_ID|g" .env
sed -i.bak "s|INSERT_GITHUB_APP_SECRET|$GITHUB_SECRET|g" .env
sed -i.bak "s|INSERT_GOOGLE_APP_ID|$GOOGLE_ID|g" .env
sed -i.bak "s|INSERT_GOOGLE_APP_SECRET|$GOOGLE_SECRET|g" .env
```

## Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added environment variables script for omniauth"
git push origin master
```
* Enter the command "sh heroku.sh".
