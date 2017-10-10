# Chapter 5: Updating the Displays

In this chapter, certain pages will be updated to show the OmniAuth service in use.  These pages are the home page, the user profile page, and the index page.  The user search form will be updated to allow searching by provider.  The pages where users can request a password reset or the resending of a confirmation email will be updated to notify users who logged in with Facebook, GitHub, or Google that such actions are not necessary.

## New Branch
Enter the command "git checkout -b omniauth_display".

## Integration Test
* Enter the command "rails generate integration_test omniauth_display".
* In the resulting test/integration/omniauth_display_test.rb file, replace the contents between the line "class OmniauthDisplayTest < ActionDispatch::IntegrationTest" and "end" with the following:
```
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
    login_as(@a1, scope: :admin)
    visit users_path
    assert page.has_text?('Provider')
    assert page.has_text?('Facebook')
    assert page.has_text?('Google')
  end
```
* Enter the command "sh test_app.sh".  All three new tests fail.
* Enter the command "alias test1='(command used to rerun failed tests MINUS the TESTOPTS portion)'".
* Enter the command test1.  All three new tests fail.

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

## Home Page
* In the app/views/static_pages/home.html.erb page, add the following code just before the end of the user section:
```

```
* Enter the 

## User Profile Page


## Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Updated pages to show the user's OmniAuth provider"
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
