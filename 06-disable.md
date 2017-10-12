# Chapter 6: Disable the Normal Login Process for OmniAuth Users

In this chapter, you will disable OmniAuth users from the normal login process.

## New Branch
Enter the command "git checkout -b omniauth_disable_login".

## Integration Test
* Enter the command "rails generate integration_test omniauth_disable_login".
* In the resulting test/integration/omniauth_disable_login_test.rb file, replace the contents between the line "class OmniauthDisableLoginTest < ActionDispatch::IntegrationTest" and "end" with the following:
```
  def check_login_disabled (e, p)
    login_user(e, p, false)
    assert page.has_text?('Please log in with Facebook, GitHub, or Google.')
  end

  test 'omniauth users may not log in throught the normal login process' do
    create_omniauth_users
    check_login_disabled('mzuckerberg@facebook.com', 'I love fake news!')
    check_login_disabled('cwanstrath@github.com', 'More popular than BitBucket!')
    check_login_disabled('sbrin@gmail.com', 'More popular than Yahoo!')
  end
```

## Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Disabled the normal login process for OmniAuth users"
git push origin omniauth_disable_login
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
