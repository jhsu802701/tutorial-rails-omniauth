# Chapter 2: General Preparations for OmniAuth 

In this chapter, you will make the general preparations needed for all OmniAuth services.

## New Branch
Enter the command "git checkout -b omniauth_prepare".

## Gemfile
* Add the following lines to the end of the Gemfile:
```
gem 'omniauth' # Allows users to use outside services for authentication
gem 'dotenv-rails' # Needed to keep export API and key values into environment variables
```
* Enter the command "bundle install".
* Enter the following commands:
```
gem list "^omniauth$"
gem list "^dotenv-rails$"
```
* Pin the version numbers of the omniauth and dotenv-rails gems in your Gemfile.
* Enter the command "bundle install".

## .gitignore
Add the following lines to the end of the .gitignore file:
```

# Keep OmniAuth credentials out of the source code
.env
```

## User Parameters
* Add the provider and uid parameters to the user model by entering the following command:
```
rails generate migration AddOmniauthToUsers provider:string uid:string
```
* Enter the command "rails db:migrate".

## User Model
* In the list of devise modules in app/models/user.rb, add the attribute ":omniauthable".
* Just before the end of the public section, add the following lines:
```
  def self.from_omniauth(auth)
    where(provider: auth.provider, uid: auth.uid).first_or_create do |user|
      user.email = auth.info.email
      user.password = Devise.friendly_token[0,20]
      name_auth = auth.info.name
      name_auth_array = name_auth.gsub(/\s+/m, ' ').strip.split(' ')
      user.last_name = name_auth_array.first # assuming the user model has a name
      user.first_name = name_auth_array.last # assuming the user model has a name
      user.username = name_auth.delete(' ')
      user.confirmed_at = Time.now
    end
  end
```

## Routing
In the user section of config/routes.rb, add "omniauth_callbacks: 'users/omniauth_callbacks'" to the list of controllers under devise.

## OmniAuth Callbacks Controller
* In the file app/controllers/users/omniauth_callbacks_controller.rb, add the following lines just before the last "end":
```
  def failure
    redirect_to root_path
  end
```
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Made general OmniAuth preparations"
```

## Wrapping Up
* Enter the following command:
```
git push origin omniauth_prepare
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
