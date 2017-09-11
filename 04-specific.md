# Chapter 4: Adding Facebook Authentication For Specific Services

## New Branch
Enter the command "git checkout -b omniauth_specific".

## Facebook Credentials
* If you don't already have a Facebook account, create one.
* Go to the [Facebook for Developers page](https://developers.facebook.com/).
* Create a new app.  Fill in the name of your app with the same name you've been using in GitHub and Heroku.
* Go to the Dashboard to see the App ID and App Secret.
* For easier reference, save your App ID and App Secret in KeePassX (or your preferred password manager).
* From your app's Dashboard, add the Facebook Login feature.  In the list of "Valid OAuth redirect URIs", add the following URLs:
```
http://localhost:3000/
http://localhost:3001/
http://localhost:3002/
http://localhost:3003/
http://localhost:3004/
http://localhost:3005/
http://localhost:3006/
http://localhost:3007/
http://localhost:3008/
http://localhost:3009/
http://localhost:3010/
```
* In addition, add the URL of your app in the production environment.

## .env
* If the .env file does not already exist in your app, create it.
* Add the following content to the .env file:
```
FACEBOOK_APP_ID='APP_ID'
FACEBOOK_APP_SECRET='APP_SECRET'
```
* Replace APP_ID and APP_SECRET with the values you saved from your Facebook App dashboard.

## config/initializers/devise.rb
Add the following line just before the last "end" line in config/initializers/devise.rb:
```
  config.omniauth :facebook, 'FACEBOOK_APP_ID', 'FACEBOOK_APP_SECRET', callback_url: 'http://localhost:3000/users/auth/facebook/callback'
```

## Gemfile
* Add the following line to the end of the Gemfile:
```
gem 'omniauth-facebook'
```
* Enter the command "bundle install".
* Enter the following command:
```
gem list "^omniauth-facebook$"
```
* Pin the version of omniauth-facebook in the Gemfile.
* Enter the command "sh git_check.sh".

## User Model
* In the file app/models/user.rb, add "omniauth_providers: [:facebook]" to the list of devise modules.
* NOTE: If "omniauth_providers" is already listed as a devise module, add ":facebook" to the list of omni_auth providers.
* Add the following lines just before the line "private":
```
  def self.new_with_session(params, session)
    super.tap do |user|
      # BEGIN: new_with_session for specific services
      if data = session["devise.facebook_data"] && session["devise.facebook_data"]["extra"]["raw_info"]
        user.email = data["email"] if user.email.blank?
      end
      # END: new_with_session for specific services
    end
  end
```

## app/controllers/users/omniauth_callbacks_controller.rb
* In the file app/controllers/users/omniauth_callbacks_controller.rb, add the following lines just before the line "private":
```
  def facebook
    @user = User.from_omniauth(request.env["omniauth.auth"])

    if @user.persisted?
      sign_in_and_redirect @user, :event => :authentication
      set_flash_message(:notice, :success, :kind => "Facebook") if is_navigational_format?
    else
      session["devise.facebook_data"] = request.env["omniauth.auth"]
      redirect_to new_user_registration_url
    end
  end
```

## Home Page
* Add the following lines to app/views/static_pages/home.html.erb immediately after the "Sign up now" button:
```
      <br>
      <%= link_to "Sign in with Facebook", user_facebook_omniauth_authorize_path, class: "btn btn-sm btn-primary" %>
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
