# Chapter 5: OmniAuth Login (Production Environment)

## User Model
* In the user model, add the following lines just before the end of the public section:
```
  def self.new_with_session(params, session)
    super.tap do |user|
      if data = session['devise.facebook_data'] && session['devise.facebook_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      elsif data = session['devise.github_data'] && session['devise.github_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      elsif data = session['devise.google_data'] && session['devise.google_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      end
    end
  end
```
* The new_with_session function was not necessary for the test or development environments, but it is necessary for the production environment.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the new_with_session function to the user model"
git push origin master
```
* Enter the command "sh heroku.sh".

## Environment Variables
* In the development environment, it was necessary to set the environment variables in the .env file in order to allow you to login to your app with the OmniAuth services.  In the production environment, you also need to set the environment variables.
* Log into your Heroku account from your browser, go to your app, click on Settings, and click on "Reveal Config Vars".
* Open the local .env file in your app.  For each environment variable in the .env file, add it to your app with the associated value.
* In your shell terminal, enter the command "heroku restart" to restart the Heroku server.
* From your app's Heroku page, try to log in through Facebook.  You'll be logged in, but the URL will show localhost instead of Heroku.  Log out.
* From your app's Heroku page, try to log in through GitHub.  You won't be logged in, and the URL will show localhost instead of Heroku.
* From your app's Heroku page, try to log in through Google.  You'll be logged in, but the URL will show localhost instead of Heroku.  Log out.

## config/initializers/devise.rb
* The problem is config/initializers/devise.rb.  Note that the callback URL is localhost instead of Heroku.  In the lines beginning with "config.omniauth", replace localhost:3000 with the base URL of your app in Heroku.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Updated config/initializers/devise.rb with the production callback URLs"
git push origin master
```
* Enter the command "sh heroku.sh".
* From your app's Heroku page, try to log in through Facebook.  You'll an error message telling you that the redirect URI is not whitelisted in the app's Client OAuth Settings.
* From your app's Heroku page, try to log in through GitHub.   You won't be logged in, and the URL will show localhost instead of Heroku.
* From your app's Heroku page, try to log in through Google.  You'll get a 400 error message telling you that the redirect URI does not match the ones authorized for the OAuth client.

## Updating Facebook Settings
* Go to the [Facebook for Developers page](https://developers.facebook.com/).
* Go to your app.  Go to the Products section, and click on "Facebook Login".
* In the list of "Valid OAuth redirect URIs", enter the following production environment URL:
```
https://(base URL)/users/auth/facebook/callback
```
* Save the changes.
* On the left side of the screen, go to "App Review".
* MORE

## Updating GitHub Settings
* From your browser, log into your GitHub account.  Go to the upper right corner of the page and click on the menu button.  Select "Settings".
* On the left side of your settings page, go down to the "Developer Settings" section and click on "OAuth Apps".
* Click on your app.
* Change the Authorization callback URL of your app to http://(base URL)/users/auth/github/callback .  This means that you will not be able to log into the version of your app running on your local machine.
* Click on Update Application.
* MORE

## Updating Google Settings
* Go to the [Google Developers Console](https://console.developers.google.com).
* On the left side of the screen, click on "Credentials", and then create a new project.  Fill in the name of your app with the same name you've been using in GitHub and Heroku.
* Click on "Create Credentials", and select "OAuth client ID".  Fill in the OAuth consent screen.
* After you submit your OAuth consent form, you'll be asked for the application type.  Select "web application", and fill in the appropriate authorized redirect URLs.
* In the list of URLs under "Authorized JavaScript origins", add the base URL of your app.
* In the list of URLs under "Authorized redirect URIs", add the following URLs:
```
https://(base URL)/users/auth/google_oauth2/callback
```
* You should now be able to log into your app on Heroku through your Google account.
