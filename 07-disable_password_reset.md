# Chapter 7: Disabling Password Resets

In this chapter, you will disable the password reset feature for OmniAuth users.

## New Branch
Enter the command "git checkout -b omniauth_disable_password reset".

## Integration Test
* Enter the command "rails generate integration_test omniauth_disable_password_reset".
* In the resulting test/integration/omniauth_disable_password_reset_test.rb file, replace the contents between the line "class OmniauthDisablePasswordResetTest < ActionDispatch::IntegrationTest" and "end" with the following:
```
  def check_password_reset_disabled(e)
  
  end

  test 'omniauth user password resets are disabled' do
    create_omniauth_users
    begin_user_password_reset('mzuckerberg@facebook.com')
    assert page.has_text?('You have no password to reset.')
    assert page.has_text?('Please log in with Facebook, GitHub, or Google.')
  end
```
