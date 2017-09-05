# Chapter 2: General Preparations for OmniAuth 

In this chapter, you will update the user model.

## New Branch
Enter the command "git checkout -b omniauth_prepare"

## Gemfile
* Add the following lines to the end of the Gemfile:
```
gem 'omniauth'
gem 'dotenv-rails' # Needed to keep private information (like the API_ID and APP_SECRET values) out of the source code
```
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.  If all goes well, you are ready to move on.
* Enter the following commands:
```
git add .
git commit -m "Added the omniauth and dotenv-rails gems"
```

## User Parameters
* Add the provider and uid parameters to the user model by entering the following command:
```
rails g migration AddOmniauthToUsers provider:index uid:index
```
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.  If all goes well, you are ready to move on.
* Enter the following commands:
```
git add .
git commit -m "Added the provider and uid parameters to users"
```

## User Model
* In the list of devise modules in app/models/user.rb, add the attribute ":omniauthable".
* Just before the end of the public section, add the following lines:
```
  def self.find_for_oauth(auth)
    user = User.where(uid: auth.uid, provider: auth.provider).first

    unless user
      user = User.create(
        uid:      auth.uid,
        provider: auth.provider,
        email:    User.dummy_email(auth),
        password: Devise.friendly_token[0, 20]
      )
    end

    user
  end

  def self.new_with_session(params, session)
    super.tap do |user|
      if data = session["devise.facebook_data"] && session["devise.facebook_data"]["extra"]["raw_info"]
        user.email = data["email"] if user.email.blank?
      end
    end
  end
```
* Just before the end of the private section, add the following lines:
```
  def self.dummy_email(auth)
    "#{auth.uid}-#{auth.provider}@example.com"
  end
```
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.  If all goes well, you are ready to move on.
* Enter the following commands:
```
git add .
git commit -m "Added the omniauthable devise module"
```

## Routing
* In the user section of config/routes.rb, add "omniauth_callbacks: 'users/omniauth_callbacks'" to the list of controllers under devise.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.  If all goes well, you are ready to move on.
* Enter the following commands:
```
git add .
git commit -m "Added the omniauth callbacks to the routing"
```

# OmniAuth Callbacks Controller
* In the file app/controllers/users/omniauth_callbacks_controller.rb, add the following lines just before the last "end":
```
  private

  def callback_from(provider)
    provider = provider.to_s

    @user = User.find_for_oauth(request.env['omniauth.auth'])

    if @user.persisted?
      sign_in_and_redirect @user, :event => :authentication #this will throw if @user is not activated
      set_flash_message(:notice, :success, :kind => provider.capitalize) if is_navigational_format?
    else
      session["devise.#{provider}_data"] = request.env["omniauth.auth"]
      redirect_to new_user_registration_url
    end
  end
```
* 

## Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Updated the user model to prepare for OmniAuth"
git push origin omniauth_prepare
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```