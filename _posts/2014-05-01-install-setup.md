---
layout: page
title: "Install & Setup"
category: start
date: 2014-05-01 15:02:23
order: 2
---

Once you have installed all the [dependancies](supplejack/start/dependancies.html) you are now ready to install the rest of the stack. As all the components require configuration to interact we strongly recommend that you use the [Rails Application Template](http://guides.rubyonrails.org/rails_application_templates.html) which is provided for Supplejack.

## Install the Supplejack Stack

The Supplejack Stack refers to the Supplejack API, Supplejack Manager and the Supplejack Worker applications. 

We have written a [Rails Application Template](http://guides.rubyonrails.org/rails_application_templates.html) which creates a new Rails application using the Supplejack API and then installs and configures the Supplejack Manager and Supplejack Worker applications.

To install the full stack run the following command:

```bash
rails _3.2.12_ new mysupplejack_api_name --skip-bundle -m https://raw.github.com/digitalnz/supplejack_template/master/supplejack_template.rb
```

**Note:** Be sure to note down the user API key that is printed to the terminal during the install process.

Once complete you will be able to access your API (at http://localhost:3000/records.json?api_key=USER_API_KEY) and harvest new data from external sources.