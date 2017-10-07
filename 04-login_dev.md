# Chapter 4: OmniAuth Login (Development Environment)

In this chapter, you will provide OmniAuth login capabilities in the development environment.  The OmniAuth logins will NOT work in the production environment yet.  (You will provide this capability later.)

## Credentials
At this point, trying to log in through the browser on your local version of the app gives you errors even though the tests pass.  You need to create your app on each of the OmniAuth services you wish to provide, get the right credentials, and then provide them in environment variables.

### Facebook
* Go to the [Facebook for Developers page](https://developers.facebook.com/).
* Create a new app.  Fill in the name of your app with the same name you've been using in GitHub and Heroku.
* Go to the Dashboard to see the App ID and App Secret.  For easier reference, save your App ID and App Secret in KeePassX (or your preferred password manager).
* From your app's Dashboard, click on "Add Product".  Select "Facebook Login" and press "Set Up".  Select "Web".
* Click on "Settings".
* Add the following URLs under Valid OAuth redirect URIs:
```
http://localhost:3000/users/auth/facebook/callback
http://localhost:3001/users/auth/facebook/callback
http://localhost:3002/users/auth/facebook/callback
http://localhost:3003/users/auth/facebook/callback
http://localhost:3004/users/auth/facebook/callback
http://localhost:3005/users/auth/facebook/callback
http://localhost:3006/users/auth/facebook/callback
http://localhost:3007/users/auth/facebook/callback
http://localhost:3008/users/auth/facebook/callback
http://localhost:3009/users/auth/facebook/callback
http://localhost:3010/users/auth/facebook/callback
```
* Click on "Save Changes" to save your settings.

### GitHub
* From your browser, log into your GitHub account.  Go to the upper right corner of the page and click on the menu button.  Select "Settings".
* On the left side of your settings page, go down to the "Developer Settings" section and click on "OAuth Apps".
* Click on "Register a New Application".  Fill in the name of your app with the same name you've been using in GitHub and Heroku.  Fill in the URL of your app on Heroku for the Homepage URL.  For the callback URL, use "http://localhost:3000/users/auth/github/callback".  (NOTE: If the host machine port that maps to port 3000 in Docker, then use that port instead of 3000 in this URL.)
* Get the client ID and secret ID.  For easier reference, save these values in KeePassX (or your preferred password manager).

### Google
* Go to the [Google Developers Console](https://console.developers.google.com).
* Click on "Create Credentials", and select "OAuth client ID".  Select "Web application".  Fill in the name of your app with the same name you've been using in GitHub and Heroku.
* In the list of URLs under "Authorized JavaScript origins", add the following URLs:
```
http://localhost:3000
http://localhost:3001
http://localhost:3002
http://localhost:3003
http://localhost:3004
http://localhost:3005
http://localhost:3006
http://localhost:3007
http://localhost:3008
http://localhost:3009
http://localhost:3010
```
* In the list of URLs under "Authorized redirect URIs", add the following URLs:
```
http://localhost:3000/users/auth/google_oauth2
http://localhost:3001/users/auth/google_oauth2
http://localhost:3002/users/auth/google_oauth2
http://localhost:3003/users/auth/google_oauth2
http://localhost:3004/users/auth/google_oauth2
http://localhost:3005/users/auth/google_oauth2
http://localhost:3006/users/auth/google_oauth2
http://localhost:3007/users/auth/google_oauth2
http://localhost:3008/users/auth/google_oauth2
http://localhost:3009/users/auth/google_oauth2
http://localhost:3010/users/auth/google_oauth2
```
* Press the "Create" button.
* Get the client ID and client secret.  For easier reference, save these values in KeePassX (or your preferred password manager).

### Twitter
* Go to the [Twitter Application Page](https://apps.twitter.com/).
* Press "Create New App".
* Fill in the name and description with the same name you've been using in GitHub and Heroku.  Fill in the URL of your app on Heroku for the Website URL.  Use http://localhost:3000/users/auth/twitter/callback for the Callback URL.
* Read the Twitter Development Agreement, and toggle on the box to acknowledge that you read and agree to it.
* Press "Create your Twitter application".
* Click on "Keys and Access Tokens".  Get the Consumer Key (API Key) and 	Consumer Secret (API Secret).  For easier reference, save these values in KeePassX (or your preferred password manager).

## Development Environment Setup
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
      elsif data = session['devise.twitter_data'] && session['devise.twitter_data']['extra']['raw_info']
        user.email = data['email'] if user.email?
      end
    end
  end
```
* In your command line terminal, enter the command "sh config_env.sh".  When prompted, enter the appropriate OmniAuth credential.
* Restart the local Rails server.


## New Branch
Enter the command "git checkout -b omniauth_login_dev".

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
