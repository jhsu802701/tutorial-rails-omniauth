# Chapter 3: Updating the Source Code

## New Branch
Enter the command "git checkout -b omniauth".

## .gitignore
* Enter the command "touch .env".  This is where you will later add the environment variables needed for OmniAuth services.
* Enter the command "git status".  You'll see that .env is an untracked file.
* Add the following lines to the end of the .gitignore file:
```

# Keep OmniAuth credentials out of the source code
.env
```
* Enter the command "git status".  You'll see that .gitignore has changed, but .env is no longer mentioned.

## Integration Tests
* Enter the command "rails generate integration_test omniauth".
* Edit the file test/integration/omniauth_test.rb and replace the section between "class OmniauthTest < ActionDispatch::IntegrationTest" and the last "end" with the following code:
```
  def login_and_logout_fb
    click_on 'Sign in with Facebook'
    assert page.has_text?('Successfully authenticated from Facebook account.')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  def login_and_logout_google
    click_on 'Sign in with Google'
    assert page.has_text?('Successfully authenticated from Google account.')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  test 'Can login with Facebook credentials' do
    OmniAuth.config.mock_auth[:facebook] = OmniAuth::AuthHash.new(
      provider: 'facebook', uid: '123545', confirmed_at: Time.now,
      info: { last_name: 'Zuckerberg', first_name: 'Mark',
              email: 'mzuckerberg@facebook.com' }
    )
    # From home page
    visit root_path
    login_and_logout_fb

    # From user login page
    visit root_path
    click_on 'Login'
    login_and_logout_fb
  end

  test 'Can login with Google credentials' do
    OmniAuth.config.mock_auth[:google_oauth2] = OmniAuth::AuthHash.new(
      provider: 'google_oauth2', uid: '123546', confirmed_at: Time.now,
      info: { last_name: 'Brin', first_name: 'Sergey',
              email: 'sbrin@gmail.com' }
    )
    # From home page
    visit root_path
    login_and_logout_google

    # From user login page
    visit root_path
    click_on 'Login'
    login_and_logout_google
  end
```
* Enter the command "sh test_app.sh".  You'll see the "uninitialized constant OmniauthTest::OmniAuth" errors, a result of not having OmniAuth installed.
* Enter the command "alias test1='command for running the tests that failed minus the TESTOPTS portion'".
* Enter the command "test1".  You'll see the OmniAuth test errors again.

## Gemfile
* Add the following lines to the end of the Gemfile:
```

# BEGIN: omniauth
gem 'dotenv-rails' # Needed to export API and secret values into environment variables
gem 'omniauth-facebook'
gem 'omniauth-google-oauth2'
# END: omniauth
```
* Enter the command "bundle install".
* Enter the following commands:
```
gem list "^dotenv-rails$"
gem list "^omniauth-facebook$"
gem list "^omniauth-google-oauth2$"
```
* Pin the version numbers of the omniauth-related gems in your Gemfile.
* Enter the command "bundle install; test1".  Now the test failures are due to missing hyperlinks.

## Home Page
* Add the following lines to app/views/static_pages/home.html.erb immediately after the "Sign up now" button:
```
      <br><br>
      <%= link_to "Sign in with Facebook", user_facebook_omniauth_authorize_path, class: "btn btn-sm btn-primary" %>
      <br><br>
      <%= link_to "Sign in with Google", user_google_oauth2_omniauth_authorize_path, class: "btn btn-sm btn-primary" %>
```
* Enter the command "test1".  Now the test failures are due to undefined paths.

## Routing
* In the user section of config/routes.rb, add "omniauth_callbacks: 'users/omniauth_callbacks'" to the list of controllers under devise.
* Enter the command "test1".  Now the tests won't even run simply because ":omniauthable" is not specified in the user model.

## User Model
* In the list of devise modules in app/models/user.rb, add the following attributes to the list of devise modules:
```
:omniauthable, omniauth_providers: [:facebook, :google_oauth2]
```
* Enter the command "test1".  The tests fail because the expected confirmations of successful logins do not occur.  You will need to take several additional actions in order to address this.
* Go to the tmux window where you are running the local server and restart the server by pressing Ctrl-c and then entering the command "sh server.sh".
* In your browser, go to the home page of your app and then try to sign in with any of the OmniAuth services.  In your browser, you will get the message "Not found. Authentication passthru."  You'll see that your local server shows a 404 (not found) error.
* Just before the end of the public section, add the following lines:
```
  def self.from_omniauth(auth)
    where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
      t_sec = Time.now.to_f
      t_usec = (t_sec * 1_000_000).to_i
      user.username = "user_omni_#{t_usec}"
      user.email = auth.info.email
      user.password = Devise.friendly_token[0, 20]
      user.confirmed_at = Time.now
      name_auth = auth.info.name
      name_auth_array = name_auth.gsub(/\s+/m, ' ').strip.split(' ')
      user.last_name = name_auth_array.first
      user.first_name = name_auth_array.last
      if auth.provider == 'google'
        user.last_name = auth.info.last_name
        user.first_name = auth.info.first_name
      end
    end
  end

  def self.new_with_session(params, session)
    super.tap do |user|
      if data = session['devise.facebook_data'] && session['devise.facebook_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      elsif data = session['devise.google_data'] && session['devise.google_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      end
    end
  end
```
* Please note that each user MUST have an email address, password, last name, first name, and username.  In addition, a user must be confirmed in order to log in.  Therefore, the self.from_omniauth provides these parameters for those who wish to login to the app with Facebook, Google, or other external services.

## config/environments/test.rb
Edit the file config/environments/test.rb and add the following line just before the final "end" statement:
```
  OmniAuth.config.test_mode = true
```

## User Parameters
* Add the provider and uid parameters to the user model by entering the following command:
```
rails generate migration AddOmniauthToUsers provider:string uid:string
```
* Enter the command "rails db:migrate".

## config/initializers/devise.rb
Add the following line just before the last "end" line in config/initializers/devise.rb:
```
  config.omniauth :facebook, ENV['FACEBOOK_APP_ID'], ENV['FACEBOOK_APP_SECRET'], callback_url: 'http://localhost:3000/users/auth/facebook/callback'
  config.omniauth :google_oauth2, ENV['GOOGLE_APP_ID'], ENV['GOOGLE_APP_SECRET'], callback_url: 'http://localhost:3000/users/auth/google/callback'
```

## app/controllers/users/omniauth_callbacks_controller.rb
* In the file app/controllers/users/omniauth_callbacks_controller.rb, add the following lines just before the last "end" statement:
```
  def facebook
    @user = User.from_omniauth(request.env['omniauth.auth'])
    if @user.persisted?
      sign_in_and_redirect @user, event: :authentication
      set_flash_message(:notice, :success, kind: 'Facebook') if is_navigational_format?
    else
      session['devise.facebook_data'] = request.env['omniauth.auth']
      redirect_to new_user_registration_url
    end
  end

  def google_oauth2
    @user = User.from_omniauth(request.env['omniauth.auth'])
    if @user.persisted?
      sign_in_and_redirect @user, event: :authentication
      set_flash_message(:notice, :success, kind: 'Google') if is_navigational_format?
    else
      session['devise.google_data'] = request.env['omniauth.auth']
      redirect_to new_user_registration_url
    end
  end

  def failure
    redirect_to root_path
  end
```
* Enter the command "test1".  Now your tests should pass, but you will won't be able to log in through the OmniAuth services on your local version of your app.  That's because the environment variables needed to provide credentials are not yet provided.

## .env
* Add the following content to the .env file:
```
FACEBOOK_APP_ID='APP_ID'
FACEBOOK_APP_SECRET='APP_SECRET'
GOOGLE_APP_ID='APP_ID'
GOOGLE_APP_SECRET='APP_SECRET'
```
* Replace APP_ID and APP_SECRET with the values you saved from your Omniauth dashboards.
* NOTE: Because the .env file is NOT in the source code, you must replace it every time you git clone the source code.
* In the server tmux window, restart your server.
* In your browser, go to your app and login and logout with each of your OmniAuth services.  You should now be able to do so successfully.
* Enter the command "sh git_check.sh".  All tests should pass, but there are a few offenses.

## .rubocop.yml
* Add app/controllers/users/omniauth_callbacks_controller.rb to the list of files exempt from Metrics/LineLength.
* Add app/models/user.rb to the list of files exempt from Style/SymbolArray, .

* In the .rubocop.yml file, add the following files to the list of exemptions from Metrics/LineLength and Metrics/MethodLength.
* Add the following lines to the .rubocop.yml file:
```

Metrics/AbcSize:
  Exclude:
    - app/models/user.rb

Metrics/MethodLength:
  Exclude:
    - app/models/user.rb

Metrics/CyclomaticComplexity:
  Exclude:
    - app/models/user.rb

Metrics/PerceivedComplexity:
  Exclude:
    - app/models/user.rb
```
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.


## Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added omniauth authentication"
git push origin omniauth
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
