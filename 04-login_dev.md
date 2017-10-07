# Chapter 4: OmniAuth Login (Development Environment)

In this chapter, you will provide OmniAuth login capabilities in the development environment.  The OmniAuth logins will NOT work in the production environment yet.  (You will provide this capability later.)

## New Branch
Enter the command "git checkout -b omniauth_login_dev".

## Adding the new_with_session Method to the User Model
```
  def self.new_with_session(params, session)
    super.tap do |user|
      if data = session['devise.facebook_data'] && session['devise.facebook_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      elsif data = session['devise.github_data'] && session['devise.github_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      elsif data = session['devise.google_data'] && session['devise.google_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      elsif data = session['devise.twitter_data'] && session['devise.twitter_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      end
    end
  end
```
* Please note that each user MUST have an email address, password, last name, first name, and username.  In addition, a user must be confirmed in order to log in.  Therefore, the self.from_omniauth definition provides these parameters for those who wish to login to the app.


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
* Enter the command "test1".  Now all the tests pass.

## Adding the new_with_session Method to the User Model
```
  def self.new_with_session(params, session)
    super.tap do |user|
      if data = session['devise.facebook_data'] && session['devise.facebook_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      elsif data = session['devise.github_data'] && session['devise.github_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      elsif data = session['devise.google_data'] && session['devise.google_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      elsif data = session['devise.twitter_data'] && session['devise.twitter_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      end
    end
  end
```
* Please note that each user MUST have an email address, password, last name, first name, and username.  In addition, a user must be confirmed in order to log in.  Therefore, the self.from_omniauth definition provides these parameters for those who wish to login to the app.

## User Parameters
* Add the provider and uid parameters to the user model by entering the following command:
```
rails generate migration AddOmniauthToUsers provider:string uid:string
```
* Enter the command "rails db:migrate".

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
git commit -m "Added omniauth logins (test)"
git push origin omniauth_login_dev
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Code Climate will flag this branch because of the similarities between the facebook and google_oauth2 definitions in  app/controllers/users/omniauth_callbacks_controller.rb.  Mark this issue as "Wontfix".
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".  In your production site, you won't be able to login from Facebook or Google just yet.  You have not yet entered the proper credentials in Heroku.







* In your browser, log into Heroku.  Go to your app, go to the settings tab, and click on "Reveal Config Vars".  Add the environment variables listed in your .env file, and give them the appropriate values.
* In the terminal, enter the command "heroku restart" and then wait a moment.
* You now should be able to log into your site with Google OmniAuth in Heroku.  However, you won't be able to log into your site with Facebook in Heroku.
* Go back to the [Facebook for Developers page](https://developers.facebook.com/).  Go to your app, and click on "Facebook Login".  In the settings, go to the list of Valid OAuth redirect URIs, and remove all of the localhost URLs.  (Keep ONLY the production environment URL.)  Save your changes.