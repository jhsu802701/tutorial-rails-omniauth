# Chapter 3: Updating the Source Code

## New Branch
Enter the command "git checkout -b omniauth_source_code".

## Integration Tests

## .gitignore
* Enter the command "touch .env".  This is where you will later add the environment variables needed for OmniAuth services.
* Enter the command "git status".  You'll see that .env is an untracked file.
* Add the following lines to the end of the .gitignore file:
```

# Keep OmniAuth credentials out of the source code
.env
```
* Enter the command "git status".  You'll see that .gitignore has changed, but .env is no longer mentioned.

## User Parameters
* Add the provider and uid parameters to the user model by entering the following command:
```
rails generate migration AddOmniauthToUsers provider:string uid:string
```
* Enter the command "rails db:migrate".


## Gemfile
* Add the following lines to the end of the Gemfile:
```
# BEGIN: omniauth
gem 'dotenv-rails' # Needed to keep export API and key values into environment variables
gem 'omniauth'
gem 'omniauth-facebook'
# END: omniauth

```
* Enter the command "bundle install".
* Enter the following commands:
```
gem list "^dotenv-rails$"
gem list "^omniauth$"
gem list "^omniauth-facebook$"
```
* Pin the version numbers of the omniauth-related gems in your Gemfile.
* Enter the command "bundle install".


## config/initializers/devise.rb
Add the following line just before the last "end" line in config/initializers/devise.rb:
```
  config.omniauth :facebook, 'FACEBOOK_APP_ID', 'FACEBOOK_APP_SECRET', callback_url: 'http://localhost:3000/users/auth/facebook/callback'
```

## User Model
* In the list of devise modules in app/models/user.rb, add the following attributes to the list of devise modules:
```
:omniauthable, omniauth_providers: [:facebook]
```
* Just before the end of the public section, add the following lines:
```
  def self.from_omniauth(auth)
    where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
      user.email = auth.info.email
      user.password = Devise.friendly_token[0,20]
      name_auth = auth.info.name
      name_auth_array = name_auth.gsub(/\s+/m, ' ').strip.split(' ')
      user.last_name = name_auth_array.first # assuming the user model has a name
      user.first_name = name_auth_array.last # assuming the user model has a name
      user.username = name_auth.delete(' ')
      user.confirmed_at = Time.now
    end
  end

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
    @user = User.from_omniauth(request.env["omniauth.auth"])
    if @user.persisted?
      sign_in_and_redirect @user, :event => :authentication
      set_flash_message(:notice, :success, :kind => "Facebook") if is_navigational_format?
    else
      session["devise.facebook_data"] = request.env["omniauth.auth"]
      redirect_to new_user_registration_url
    end
  end

  def failure
    redirect_to root_path
  end
```

## Home Page
Add the following lines to app/views/static_pages/home.html.erb immediately after the "Sign up now" button:
```
      <br><br>
      <%= link_to "Sign in with Facebook", user_facebook_omniauth_authorize_path, class: "btn btn-sm btn-primary" %>
```

## Routing
In the user section of config/routes.rb, add "omniauth_callbacks: 'users/omniauth_callbacks'" to the list of controllers under devise.

## .env
* Add the following content to the .env file:
```
FACEBOOK_APP_ID='APP_ID'
FACEBOOK_APP_SECRET='APP_SECRET'
```
* Replace APP_ID and APP_SECRET with the values you saved from your Facebook App dashboard.
* NOTE: Because the .env file is NOT in the source code, you must replace it every time you git clone the source code.

## Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added Facebook authentication"
git push origin omniauth_source_code
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
