# Chapter 3: Adding Facebook Authentication

## New Branch
Enter the command "git checkout -b omniauth_facebook".

## Facebook Credentials
* If you don't already have a Facebook account, create one.
* Go to the [Facebook for Developers page](https://developers.facebook.com/).
* Create a new app.  Fill in the name of your app with the same name you've been using in GitHub and Heroku.
* Go to the Dashboard to see the App ID and App Secret.
* For easier reference, save your App ID and App Secret in KeePassX (or your preferred password manager).

## .env
* If the .env file does not already exist in your app, create it.
* Add the following content to the .env file:
```
FACEBOOK_API='APP_ID'
FACEBOOK_KEY='APP_SECRET'
```
* Replace the APP_ID and APP_SECRET with the values you saved from your Facebook App dashboard.

## config/initializers/devise.rb
Add the following line just before the last "end" line in config/initializers/devise.rb:
```
  config.omniauth :facebook, ENV['FACEBOOK_API'], ENV['FACEBOOK_KEY']
```

## Gemfile
* Add the following line to the end of the Gemfile:
```
gem 'omniauth-facebook'
```
* Enter the command "bundle install".
* Pin the version of omniauth-facebook.
* Enter the command "sh git_check.sh".

## User Model
* In the file app/models/user.rb, add "omniauth_providers: [:facebook]" to the list of devise modules.
* NOTE: If "omniauth_providers" is already listed as a devise module, add ":facebook" to the list of omni_auth providers.
* Add the following lines just before the line "private":
```
  def self.new_with_session(params, session)
    super.tap do |user|
      if data = session["devise.facebook_data"] && session["devise.facebook_data"]["extra"]["raw_info"]
        user.email = data["email"] if user.email.blank?
      end
    end
  end
```

## app/controllers/users/omniauth_callbacks_controller.rb
* In the file app/controllers/users/omniauth_callbacks_controller.rb, add the following lines just before the line "private":
```
  def facebook
    callback_from :facebook
  end
```

## Home Page
* Add the following line to app/views/static_pages/home.html.erb immediately after the "Sign up now" button:
```
      <%= link_to "Sign in with Facebook", user_facebook_omniauth_authorize_path, class: "btn btn-lg btn-primary" %>
```

## Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added Facebook authentication"
git push origin omniauth_facebook
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
