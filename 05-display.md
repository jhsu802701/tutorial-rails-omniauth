# Chapter 5: Updating the Displays

In this chapter, certain pages will be updated to reflect the use of OmniAuth services.

## Objectives
* Change the text reading "Sign in with GoogleOauth2" on the user login page with "Sign in with Google".
* Provide a message on the home page notifying the OmniAuth user of the specific OmniAuth service used.
* Add the user's OmniAuth service to the user profile page and the index page.

## New Branch
Enter the command "git checkout -b omniauth_display".

## Integration Test
* Enter the command "rails generate integration_test omniauth_display".
* In the resulting test/integration/omniauth_display_test.rb file, replace the contents between the line "class OmniauthDisplayTest < ActionDispatch::IntegrationTest" and "end" with the following:
```
  test "'GoogleOauth2' has been replaced with 'Google'" do
    visit root_path
    click_on 'Login'
    assert page.has_link?('Sign in with Google', href: user_google_oauth2_omniauth_authorize_path)
    assert_not page.has_link?('Sign in with GoogleOauth2', href: user_google_oauth2_omniauth_authorize_path)
  end

  test "Facebook user's home page and profile page show that Facebook was used to login" do
    create_omniauth_users
    visit root_path
    click_on 'Sign in with Facebook'
    click_on 'Home'
    assert page.has_text?('You logged in with Facebook.')
    click_on 'Your Profile'
    assert page.has_text?('OmniAuth Service Provider: Facebook')
    click_on 'Logout'
  end

  test "GitHub user's home page and profile page show that GitHub was used to login" do
    create_omniauth_users
    visit root_path
    click_on 'Sign in with GitHub'
    click_on 'Home'
    assert page.has_text?('You logged in with GitHub.')
    click_on 'Your Profile'
    assert page.has_text?('OmniAuth Service Provider: GitHub')
    click_on 'Logout'
  end

  test "Google user's home page and profile page show that Google was used to login" do
    create_omniauth_users
    visit root_path
    click_on 'Sign in with Google'
    click_on 'Home'
    assert page.has_text?('You logged in with Google.')
    click_on 'Your Profile'
    assert page.has_text?('OmniAuth Service Provider: Google')
    click_on 'Logout'
  end

  test 'The user index should show the OmniAuth Service provider' do
    create_omniauth_users
    login_as(@a1, scope: :admin)
    visit users_path
    assert page.has_text?('Provider')
    assert page.has_text?('Facebook')
    assert page.has_text?('GitHub')
    assert page.has_text?('Google')
  end
```
* Enter the command "sh test_app.sh".  All 6 new tests fail.
* Enter the command "alias test1='(command used to rerun failed tests MINUS the TESTOPTS portion)'".
* Enter the command test1.  All 7 new tests fail.

## app/views/users/shared/_links.html.erb
* In this section, you will change the text reading "Sign in with GoogleOauth2" on the user login page with "Sign in with Google".
* Edit the file app/views/users/shared/_links.html.erb.
* Replace the entire if conditional containing "if devise_mapping.omniauthable?" with the following code:
```
<%- if devise_mapping.omniauthable? %>
  <%- resource_class.omniauth_providers.each do |provider| %>
    <%- if "#{OmniAuth::Utils.camelize(provider)}" == 'GoogleOauth2' %>
      <%= link_to "Sign in with Google", omniauth_authorize_path(resource_name, provider) %><br />
    <%- else %>
      <%= link_to "Sign in with #{OmniAuth::Utils.camelize(provider)}", omniauth_authorize_path(resource_name, provider) %><br />
    <% end -%>
  <% end -%>
<% end -%>
```
* Enter the command "test1".  Now only 6 of the tests fail.

## app/views/users/passwords/new.html.erb
* In this section, you will add a message to the password reset request page noting that this action is not necessary for those who used an OmniAuth service to log in.
* Edit the file app/views/users/passwords/new.html.erb.
* Just before the line containing "form_for", add the following lines:
```
<br><br>
NOTE: If you rely on Facebook, GitHub, or Google to login to this site, then you do NOT need a special password.
<br><br>
```
* Enter the command "test1".  Now only 5 of the tests fail.

## app/views/users/confirmations/new.html.erb
* In this section, you will add a message to the user confirmation request page noting that this action is not necessary for those who used an OmniAuth service to log in.
* Edit the file app/views/users/confirmations/new.html.erb.
* Just before the line containing "form_for", add the following lines:
```
<br><br>
NOTE: If you rely on Facebook, GitHub, or Google to login to this site, then you do NOT need a confirmation.
<br><br>
```
* Enter the command "test1".  Now only 4 of the tests fail.  The test failures are all due to the missing method create_omniauth_users.

## test/test_helper.rb
* In the test/test_helper.rb file, add the following lines:
```
def create_omniauth_users
  OmniAuth.config.mock_auth[:facebook] = OmniAuth::AuthHash.new(
    provider: 'facebook', uid: '100001', confirmed_at: Time.now,
    info: { last_name: 'Zuckerberg', first_name: 'Mark',
            email: 'mzuckerberg@facebook.com' }
  )
  OmniAuth.config.mock_auth[:github] = OmniAuth::AuthHash.new(
    provider: 'github', uid: '100002', confirmed_at: Time.now,
    info: { last_name: 'Wanstrath', first_name: 'Chris',
            email: 'cwanstrath@github.com' }
  )
  OmniAuth.config.mock_auth[:google_oauth2] = OmniAuth::AuthHash.new(
    provider: 'google_oauth2', uid: '100003', confirmed_at: Time.now,
    info: { last_name: 'Brin', first_name: 'Sergey',
            email: 'sbrin@gmail.com' }
  )
  visit root_path
  click_on 'Sign in with Facebook'
  click_on 'Logout'
  click_on 'Sign in with GitHub'
  click_on 'Logout'
  click_on 'Sign in with Google'
  click_on 'Logout'
end
```
* Enter the command "test1".  Now the tests fail because some of the expected content is missing.

## Home Page
* In the app/views/static_pages/home.html.erb page, add the following code just before the end of the user section:
```
    <% if current_user.provider == 'facebook' %>
      <br>You are logged in with Facebook.<br>
    <% elsif current_user.provider == 'github' %>
      <br>You are logged in with GitHub.<br>
    <% elsif current_user.provider == 'google-oauth2' %>
      <br>You are logged in with Google.<br>
    <% end %>
```
* Enter the command "test1".

## User Profile Page


## Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Updated the OmniAuth displays"
git push origin omniauth_display
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
