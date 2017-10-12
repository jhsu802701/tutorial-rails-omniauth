# Chapter 6: Disable Unnecessary Functions for OmniAuth Users

In this chapter, you will disable OmniAuth users from the normal login process, password resets, having confirmation emails resent, and unlocking their accounts.  External services (Facebook, GitHub, or Google) are responsible for these tasks.

## New Branch
Enter the command "git checkout -b omniauth_disable".

## Integration Test
* Enter the command "rails generate integration_test omniauth_disable".
* In the resulting test/integration/omniauth_disable_test.rb file, replace the contents between the line "class OmniauthDisableTest < ActionDispatch::IntegrationTest" and "end" with the following:
```
test 'omniauth user password resets are disabled' do
 create_omniauth_users
end

test 'omniauth users may not have confirmation emails resent' do
end

test 'omniauth users may not unlock accounts' do
end
```

## Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Disabled unnecessary functions for OmniAuth users"
git push origin omniauth_disable
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
