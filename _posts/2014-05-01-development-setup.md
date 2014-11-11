---
layout: page
title: "Development Setup"
category: start
date: 2014-05-01 15:02:23
order: 2
---

Once you have installed all the [dependencies](/supplejack/start/dependencies.html) you are now ready to install the rest of the stack. As all the components require configuration to interact we strongly recommend that you use the [installation template](https://github.com/DigitalNZ/supplejack_installation) which is a [Rails Application Template](http://guides.rubyonrails.org/rails_application_templates.html) which is provided for Supplejack.

If you are a Mac user using [Homebrew](http://brew.sh/) we strongly recommend you cleanly run brew doctor before installing. 

`brew update`

`brew doctor`

Resolving issues that `brew doctor` picks up will alleviate many problems you might encounter installing Supplejack.

Please also note there is a known installation issue involving the _ibv8_ gem for Yosemite users. We are working to resolve this.


## Install the Supplejack Stack

The Supplejack Stack refers to the Supplejack API, Supplejack Manager and the Supplejack Worker applications. 

The  [installation template](https://github.com/DigitalNZ/supplejack_installation) creates a new Rails application using the Supplejack API and then installs and configures the Supplejack Manager and Supplejack Worker applications.

To install the full stack run the following command:

```bash
# You should replace 'mysupplejack_api_name' with the name of your app.

rails _3.2.12_ new mysupplejack_api_name --skip-bundle -m https://raw.github.com/digitalnz/supplejack_installation/master/supplejack_api_template.rb
```

**Note:** Be sure to note down the user API key that is printed to the terminal during the install process.

Once complete you will be able to access your API (at http://localhost:3000/records.json?api_key=USER_API_KEY) and harvest new data from external sources.
