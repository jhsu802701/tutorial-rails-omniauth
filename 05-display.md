# Chapter 5: Updating the Displays

In this chapter, certain pages will be updated to reflect the use of OmniAuth services.

## Objectives
* Change the text reading "Sign in with GoogleOauth2" on the user login page with "Sign in with Google".
* Provide a message on the home page notifying the OmniAuth user of the specific OmniAuth service used.
* Add the user's OmniAuth service to the user profile page and the index page.
* Allow the list of users to be searched by the provider parameter.

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
* Enter the command "sh test_app.sh".  All 5 new tests fail.
* Enter the command "alias test1='(command used to rerun failed tests MINUS the TESTOPTS portion)'".
* Enter the command test1.  All 5 tests fail.

## app/views/users/shared/_links.html.erb
* One of the tests fails because the user login page contains the text "Sign in with GoogleOauth2".  Changing it to "Sign in with Google" will make it pass.
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
* Enter the command "test1".  Now only 4 of the tests fail.

## test/test_helper.rb
* At this point, all 4 tests fail because the method create_omniauth_users is undefined.
* In the test/test_helper.rb file, add the following lines:
```
# rubocop:disable Metrics/AbcSize
# rubocop:disable Metrics/MethodLength
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
# rubocop:enable Metrics/AbcSize
# rubocop:enable Metrics/MethodLength
```
* Enter the command "test1".  Now the 4 tests fail because some of the expected content is missing.

## Home Page
* 3 of the tests fail because the home page does not specify which OmniAuth service was used to log in.
* In the app/views/static_pages/home.html.erb file, add the following code just before the end of the user section:
```
    <% if current_user.provider == 'facebook' %>
      <br>You logged in with Facebook.<br>
    <% elsif current_user.provider == 'github' %>
      <br>You logged in with GitHub.<br>
    <% elsif current_user.provider == 'google_oauth2' %>
      <br>You logged in with Google.<br>
    <% end %>
```
* Enter the command "test1".  4 tests still fail.  Now those 3 tests alluded to earlier fail because the user profile page does not specify which OmniAuth service was used.

## User Profile Page
* In the app/views/users/show.html.erb file, add the following code just before the delete button section:
```
    <% if @user.provider == 'facebook' %>
      OmniAuth Service Provider: Facebook<br>
    <% elsif @user.provider == 'github' %>
      OmniAuth Service Provider: GitHub<br>
    <% elsif @user.provider == 'google_oauth2' %>
      OmniAuth Service Provider: Google<br>
    <% end %>
```
* Enter the command "test1".  Now only 1 test still fails.

## User Index Page
* In the app/views/users/index.html.erb file, add the following line just before the line containing "</tr>":
```
    <td><b>Provider</b></td>
```
* In the /app/views/users/_user.html.erb file, add the following line just before the line containing "</tr>":
```
  <td>
  <% if user.provider == 'facebook' %>
    Facebook
  <% elsif user.provider == 'github' %>
    GitHub
  <% elsif user.provider == 'google_oauth2' %>
    Google
  <% else %>
    N/A
  <% end %>
  </td>
```
* Enter the command "test1".  Now all tests pass.

## User Index Search
* Edit the file app/models/user.rb and add "provider" to the list of RANSACKABLE_ATTRIBUTES.
* Enter the command "test1".  Now all tests should still pass.

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
