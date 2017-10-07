# Chapter 2: OmniAuth Login (Test Environment)

In this chapter, you will provide OmniAuth login capabilities in the test environment.  The OmniAuth logins will NOT work in the development or production environments yet.  (You will provide this capability in later chapters.)

## New Branch
Enter the command "git checkout -b omniauth_login_test".

## Integration Tests
* Enter the command "rails generate integration_test omniauth_login".
* Edit the file test/integration/omniauth_test.rb and replace the section between "class OmniauthLoginTest < ActionDispatch::IntegrationTest" and the last "end" with the following code:
```
  def login_and_logout_fb
    click_on 'Sign in with Facebook'
    assert page.has_text?('Successfully authenticated from Facebook account.')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  def login_and_logout_github
    click_on 'Sign in with GitHub'
    assert page.has_text?('Successfully authenticated from GitHub account.')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  def login_and_logout_google
    click_on 'Sign in with Google'
    assert page.has_text?('Successfully authenticated from Google account.')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  def login_and_logout_twitter
    click_on 'Sign in with Twitter'
    assert page.has_text?('Successfully authenticated from Twitter account.')
    click_on 'Logout'
    assert page.has_text?('Signed out successfully.')
  end

  test 'Can login with Facebook credentials' do
    OmniAuth.config.mock_auth[:facebook] = OmniAuth::AuthHash.new(
      provider: 'facebook', uid: '100001', confirmed_at: Time.now,
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

  test 'Can login with GitHub credentials' do
    OmniAuth.config.mock_auth[:github] = OmniAuth::AuthHash.new(
      provider: 'github', uid: '100002', confirmed_at: Time.now,
      info: { last_name: 'Wanstrath', first_name: 'Chris',
              email: 'cwanstrath@github.com' }
    )
    # From home page
    visit root_path
    login_and_logout_github

    # From user login page
    visit root_path
    click_on 'Login'
    login_and_logout_github
  end

  test 'Can login with Google credentials' do
    OmniAuth.config.mock_auth[:google_oauth2] = OmniAuth::AuthHash.new(
      provider: 'google_oauth2', uid: '100003', confirmed_at: Time.now,
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

  test 'Can login with Twitter credentials' do
    OmniAuth.config.mock_auth[:twitter] = OmniAuth::AuthHash.new(
      provider: 'twitter', uid: '100004', confirmed_at: Time.now,
      info: { last_name: 'Dorsey', first_name: 'Jack',
              email: 'jdorsey@twitter.com' }
    )
    # From home page
    visit root_path
    login_and_logout_twitter

    # From user login page
    visit root_path
    click_on 'Login'
    login_and_logout_twitter
  end
```
* Enter the command "sh test_app.sh".  You'll see the "uninitialized constant OmniauthTest::OmniAuth" errors, a result of not having OmniAuth installed.
* Enter the command "alias test1='command for running the tests that failed minus the TESTOPTS portion'".
* Enter the command "test1".  You'll see the OmniAuth test errors again.

## Gemfile
* Add the following lines to the end of the Gemfile:
```

# BEGIN: omniauth
gem 'omniauth-facebook'
gem 'omniauth-github'
gem 'omniauth-google-oauth2'
gem 'omniauth-twitter'
# END: omniauth
```
* Enter the command "bundle install".
* Enter the following commands:
```
gem list "^omniauth-facebook$"
gem list "^omniauth-github$"
gem list "^omniauth-google-oauth2$"
gem list "^omniauth-twitter$"
```
* Pin the version numbers of the omniauth-related gems in your Gemfile.
* Enter the command "bundle install; test1".  Now the test failures are due to missing hyperlinks.

## Home Page
* Add the following lines to app/views/static_pages/home.html.erb immediately after the "Sign up now" button:
```
      <br><br>
      <%= link_to "Sign in with Facebook", user_facebook_omniauth_authorize_path, class: "btn btn-sm btn-primary" %>
      <br><br>
      <%= link_to "Sign in with GitHub", user_github_omniauth_authorize_path, class: "btn btn-sm btn-primary" %>
      <br><br>
      <%= link_to "Sign in with Google", user_google_oauth2_omniauth_authorize_path, class: "btn btn-sm btn-primary" %>
      <br><br>
      <%= link_to "Sign in with Twitter", user_twitter_omniauth_authorize_path, class: "btn btn-sm btn-primary" %>
```
* Enter the command "test1".  Now the test failures are due to undefined paths.

## Routing
* In the user section of config/routes.rb, add "omniauth_callbacks: 'users/omniauth_callbacks'" to the list of controllers under devise.
* Enter the command "test1".  Now the tests won't even run simply because ":omniauthable" is not specified in the user model.

## Adding the OmniAuth Attribute to the User Model
* In the list of devise modules in app/models/user.rb, add the following attributes:
```
:omniauthable, omniauth_providers: [:facebook, :github, :google_oauth2, :twitter]
```
* Enter the command "test1".  The tests fail because the expected confirmations of successful logins do not occur.
* Go to the tmux window where you are running the local server and restart the server by pressing Ctrl-c and then entering the command "sh server.sh".
* In your browser, go to the home page of your app and then try to sign in with any of the OmniAuth services.  In your browser, you will get the message "Not found. Authentication passthru."  
* Open the file log/test.log for a more detailed view of what happens during the tests.  At this point, clicking on any of the links to login with the OmniAuth services leads to a 404 (not found) error.
* The error messages in each of the 4 failed tests is:
```
Completed 404 Not Found
```
* The preceding action is:
```
Processing by Users::OmniauthCallbacksController#passthru as HTML
  Rendering text template
  Rendered text template
```
* The last actions prior to the processing by the user OmniAuth callback controller are the following:
  * For the Facebook test: Started GET "/users/auth/facebook"
  * For the GitHub test: Started GET "/users/auth/github"
  * For the Google test: Started GET "/users/auth/google_oauth2"
  * For the Twitter test: Started GET "/users/auth/twitter"

## config/initializers/devise.rb
* Add the following lines just before the last "end" line in config/initializers/devise.rb:
```
  config.omniauth :facebook, ENV['FACEBOOK_ID'], ENV['FACEBOOK_SECRET'], callback_url: 'facebook/callback'
  config.omniauth :github, ENV['GITHUB_ID'], ENV['GITHUB_SECRET'], callback_url: 'users/auth/github/callback'
  config.omniauth :google_oauth2, ENV['GOOGLE_ID'], ENV['GOOGLE_SECRET'], callback_url: 'users/auth/google/callback'
  config.omniauth :twitter, ENV['TWITTER_ID'], ENV['TWITTER_SECRET'], callback_url: 'users/auth/twitter/callback'
```
* Enter the command "test1".  In the screen output, you'll see that the Facebook test fails because no route matches "/v2.6/dialog/oauth", the GitHub test fails because no route matches "/login/oauth/authorize", the Google test fails because no route matches "/o/oauth2/auth" for the Google test, and the Twitter test fails with a code 400 (bad request).
* Restart the local server, and then go to your local version of the app.
  * When you try to login with Facebook, you end up on a Facebook page with the error message "The parameter app_id is required".
  * When you try to login with GitHub, you end up on a GitHub page with the 404 (not found) error message.
  * When you try to login with Google, you end up on a Google page with a 400 error and messages telling you that you made an invalid request and are missing the client_id parameter.
  * When you try to login with Twitter, you remain on your localhost with a 400 (bad request) error message.
* In other words, you are missing the parameters required.  For the development and production environments, you'll provide the parameters later.  The next step is to address this issue in the test environment.

## config/environments/test.rb
* Edit the file config/environments/test.rb and add the following line just before the final "end" statement:
```
  OmniAuth.config.test_mode = true
```
* This change bails you out of the need to provide the parameters in the test environment.  Please note that this does NOT do bail you out of the need to do so in the development or production environments.
* Enter the command "test1".  Now the tests fail because the facebook, github, google_oauth2, and twitter actions are missing in the user OmniAuth callbacks controller.

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

  def github
    @user = User.from_omniauth(request.env['omniauth.auth'])
    if @user.persisted?
      sign_in_and_redirect @user, event: :authentication
      set_flash_message(:notice, :success, kind: 'GitHub') if is_navigational_format?
    else
      session['devise.github_data'] = request.env['omniauth.auth']
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

  def twitter
    @user = User.from_omniauth(request.env['omniauth.auth'])
    if @user.persisted?
      sign_in_and_redirect @user, event: :authentication
      set_flash_message(:notice, :success, kind: 'Twitter') if is_navigational_format?
    else
      session['devise.twitter_data'] = request.env['omniauth.auth']
      redirect_to new_user_registration_url
    end
  end

  def failure
    redirect_to root_path
  end
```
* Enter the command "test1".  Now your tests fail because the from_omniauth method is missing in the user model.

## Adding the from_omniauth Method to the User Model
* Just before the end of the public section of app/models/user.rb, add the following lines:
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
      user.last_name = name_auth_array.last
      user.first_name = name_auth_array.first
      if auth.provider == 'google'
        user.last_name = auth.info.last_name
        user.first_name = auth.info.first_name
      end
    end
  end
```
* Enter the command "test1".  Now the tests fail because the provider parameter is missing from the user model.

## Adding User Parameters
* Enter the command "rails generate migration AddProviderToUsers provider:string".
* Enter the command "rails db:migrate".
* Enter the command "test1".  Now the tests fail because the uid parameter is missing from the user model.
* Enter the command "rails generate migration AddUIDToUsers uid:string"
* Enter the command "rails db:migrate".
* Enter the command "test1".  Now all the integration tests in this chapter should pass.
* Enter the command "sh git_check.sh".  All of the tests should pass, but there will be RuboCop offenses.

## .rubocop.yml
* Add app/controllers/users/omniauth_callbacks_controller.rb to the list of files exempt from Metrics/LineLength.
* Add app/models/user.rb to the list of files exempt from Style/SymbolArray.
* Add the following lines to the .rubocop.yml file:
```

Metrics/AbcSize:
  Exclude:
    - app/models/user.rb

Metrics/MethodLength:
  Exclude:
    - app/models/user.rb
```
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

## Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added omniauth authentication (testing)"
git push origin omniauth_login_test
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Code Climate will flag this branch because of the similarities in the facebook, github, google_oauth2, and twitter definitions in app/controllers/users/omniauth_callbacks_controller.rb.  Mark this issue as "Wontfix".
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".  In your production site, you won't be able to login from Facebook or Google just yet.  You have not yet entered the proper credentials in Heroku.
